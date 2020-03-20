A simple git branching model
============================

There are several git branching models out there. It won't be long before you start pulling your hair out not knowing what to do, resulting in something like this:

  .. image:: https://imgs.xkcd.com/comics/git.png
     :align: center
     :target: https://imgs.xkcd.com/comics/git.png

The following guide delineates a git branching model for small teams. It attempts to provide a clean way to effectively collaborate while touching upon the most commonly occuring problems caused by, ahem, hitherto uninitiated team members. 

Disclaimer: This guide assumes that you know the basics of ``git``. If not, then you can get started `here <https://rogerdudler.github.io/git-guide/>`_

So let's start off with some general dos and don'ts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Always make it a point to add files to your index (staging area) using::

    git add <filename>

  Pressing ``tab`` after ``git add`` will automatically bring up changed files (in most shells). Manually add one file after another or by specifying filenames seperated by spaces in the ``git add``. You can ensure that all your changed files have been added using::

    git status

  Commit all the required files that show up. Do not add binaries like ``.a``, ``.so``, ``.jar``, ``.war`` and so on. Also do not add files that might contain dynamically generated passwords or any kind of secrets. It is a good practice to add these recurring unnecessary files in ``.gitignore``. For e.g. a ``maven`` project's ``.gitignore`` might look like this::

    ./target/*

  **NEVER** use ``git add .`` or ``git add --all`` for you might unknowingly be adding files like the ones mentioned above to the repository.
  
  If this practice sounds a little too pedantic, I urge you to read `this <https://github.com/ChALkeR/notes/blob/master/Do-not-underestimate-credentials-leaks.md>`_.

* Oftentimes, when working with a repository with many collaborators, the following is a very common occurrence::

    To git@github.com:someguy/somerepo.git
    ! [rejected]        master -> master (non-fast-forward)
    error: failed to push some refs to 'git@github.com:someguy/somerepo.git'
    hint: Updates were rejected because a pushed branch tip is behind its remote
    hint: counterpart. Check out this branch and merge the remote changes
    hint: (e.g. 'git pull') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.


  Naturally, when you turn to the sacred texts (Stackoverflow) to solve the problem, the second most upvoted answer, which is also the most alluring one, owing to its brevity, is perhaps **the best thing you can do to lose your job**. (I kid you not, just look at the memes about this) The cursed command is none other than::

   git push -f origin master

  **NEVER** run this command. At any cost. This will overwrite all changes in the repository with the changes you have made, if the heads are not in sync. We will talk about how to deal with this problem a little later.


* A commit message is meant to be a summary of what the commit does. It needs to be descriptive, yet brief. The first line, should describe the most significant change that the commit does. First line commit messages longer than ``50`` characters are truncated and wrapped to the next line resulting in a messy view on the commits page of github. So brevity is key. If there are some more minor changes, they can follow on the subsequent lines. This is purely optional. 
  
  It is a good practice to have one major change per commit. Mixing up several changes in the same commit might create problems for someone who is trying to run ``git blame`` or  ``git bisect`` A good commit message looks something like this::

    Updated regex to allow underscores in username

    Fixed minor typos in the readme

    Signed-off-by: Mikhail Tal<tal@mikhailtal.com>

  Commits should be made using ``git commit``, and not using::
  
    git commit -m "<Commit message>" 
    
  since descriptive commit messages cannot be written using the latter option.
    
  Commit messages should certainly not look like this

    .. image:: https://imgs.xkcd.com/comics/git_commit.png
       :align: center
       :target: https://imgs.xkcd.com/comics/git_commit.png

  Generally, commit messages should not be:

   - Ambiguous or esoteric (``Fixed bug``, ``Added feature`` etc.)
   - Meaninglessly short (``x``, ``y``, ``a``, ``Add``, ``Update`` etc.)
   - Too long (See `this <https://chris.beams.io/posts/git-commit/>`_):: 

      Re-adding ConfigurationPostProcessorTests after its brief 
      removal in r814. @Ignore-ing the 
      testCglibClassesAreLoadedJustInTimeForEnhancement() 
      method as it turns out this was one of the culprits in the 
      recent build breakage. The classloader hacking causes 
      subtle downstream effects, breaking unrelated tests. The 
      test method is still useful, but should only be run on a 
      manual basis to ensure CGLIB is not prematurely 
      classloaded, and should not be run as part of the 
      automated build.

  You can read about how to write good git commit messages `here <https://chris.beams.io/posts/git-commit/>`_. I would not strongly advocate the use of imperative moods in commit messages. It's completely up to the developer. 

* Commit often. Even if the change is minor, it is a good practice to commit frequently. Commits should be atomic. As mentioned previously, if there are lots of changes in a single commit, then it makes the life of the guy trying to run ``git bisect``, very hard.  

With the most basic dos and don'ts out of the way, let us now get to the brass tacks. We will be looking at:

#. The importance of a branching model
#. A step-by-step guide to the branching model
#. Overcoming pitfalls when you're not using the model 


This guide is going to be rife with dense git parlance. If you are not already familiar with the them, read `this <https://linuxacademy.com/blog/linux/git-terms-explained/>`_ guide.

The need for a branching model
------------------------------

An article from `thenewstack <https://thenewstack.io/dont-mess-with-the-master-working-with-branches-in-git-and-github/>`_ quotes this about pushing directly to master::

    Do not mess with the master. If you make changes to the master 
    branch of a group project while other people are also working 
    on it, your on-the-fly changes will ripple out to affect 
    everyone else and very quickly there will be merge conflicts, 
    weeping, rending of garments, and plagues of locusts. 
    It’s that serious.

On a more serious note, there are plenty of reasons why you should not commit to master:

#. In a large project, there could be several people working on seperate features. If all the collaborators commit incomplete changes to the master, there will not be a stable branch to work on. Or worse, those changes might have unintended effects on other features or create a merge conflict hell. In such a scenario, new contributors will not even be able to get the project up and running to start working on it.

#. If there are no periodic release cycles for the project, the project becomes unusable since the master branch is littered with broken commits.

#. It will be unclear when a new feature was introduced and is fully functioning.

#. Those who fork from master, will get broken/incomplete changes.

#. Setting up Continuous Delivery or Continuous Deployment will become challenging.

So what's the remedy? The use of an effective git workflow (aka a branching model)


The branching model - Demystified
---------------------------------

The core idea is egregiously simple: Every collaborator works on their own fork of the main repository. They create branches in their forks to develop their feature and once ready, create a pull reuqest in the main repository. After all the tests pass and the maintainer reviews it, the branch is merged into the master branch of the main repository. 

This is by no means a new kind of workflow, this is exactly how contributions have been made to open-source projects for decades now. This guide simply aims to demystify this process. To illustrate this workflow, say that Bob and would like to contribute to Alice's repo called ``awesome-tools``

#. Bob first forks Alice's repo
#. Then he clones his fork using::

     git clone https://github.com/bob/awesome-tools

#. Bob now needs to add Alice's repository as an upstream, to fetch new changes::

     git remote add upstream https://github.com/alice/awesome-tools

#. Now Bob can start making changes, after he checks out into a new branch::

     git checkout -b coverage-tools

   The checkout **has** to be from the master branch. Otherwise it'll open a can of worms. Bob needs to ensure this by running ``git branch`` Before running the ``git checkout`` command

#. Now that Bob is on the ``coverage-tools`` branch, he can make the changes that he wants.

#. Once the changes are ready, he adds the files individually using::

     git add <filename>

   Note that Bob is on the ``coverage-tools`` branch

#. Once all the files have been added, Bob makes a commit using::

     git commit

#. Note that Bob cannot push directly to his branch on his fork. This will most likely result in a merge conflict if new changes have been made to the upstream repository

#. Therefore, after committing changes to ``coverage-tools`` branch, Bob checks out to master using::

     git checkout master

#. Now Bob has to pull any new changes added to Alice's repository, while he was working on his feature. He can do this by using::

     git fetch --all
     
#. If there indeed are any changes, then Bob can merge those changes to his master using::

     git merge upstream/master

#. Once the merge is complete, Bob can push these changes to his fork using::

     git push origin master

#. Now, Bob switches back to the ``coverage-tools`` branch, and rebases his changes with master::

     git rebase master

   If there are no conflicts then go to step 16

#. If there are any conflicts, they need to be sorted out and the conflict-free files must be added using::

     git add <filename>

#. Once all the conflicts in all the files have been fixed, then the rebase can continue::

     git rebase --continue

#. After the rebase is successful, Bob can push changes to his branch::

     git push origin coverage-tools

#. Bob now heads over to Alice's repo and creates a pull request

#. Alice, reviews Bob code and if she thinks it's a valuable addition to her repository, merges Bob's pull request. 
   
   Note that it is advisable to ``Squash and Merge`` instead of using the plain merge. ``Squash and Merge`` will not create a merge commit in master.

#. Bob must now update his fork using the following steps::

     git checkout master
     git fetch --all
     git merge upstream/master
     git push origin master

   It is essential to note that if Bob doesn't update his fork and checks out from the master (whose ``HEAD`` is not in sync with Alice's repo), to add a new feature, it will lead to a textbook case of the fork being ``X commits ahead and Y commits behind alice/awesome-tools``  


When should you not use this workflow - and avoiding common pitfalls
--------------------------------------------------------------------

Following the branching model for every repository you push to can become quite cumbersome. Say you have a repository for your CV, following this workflow is unnecessarily complicated (even if there's a friend helping out with your CV). 

Typically, when you're directly working with a repo, the process is quite straightforward, even if there is another collaborator:

#. Add files using::

    git add <filename>

#. Commit the changes::

    git commit

#. Now, before pushing these changes, if there are new changes added to the repo, run::

    git pull --rebase

  If there are no new changes, go to step 5

#. If there are any conflicts, fix them and do the following::

    git add <conflict-free-file>
    git rebase --continue

#. Once the rebase is successful, it is safe to push the commit::

    git push origin master
