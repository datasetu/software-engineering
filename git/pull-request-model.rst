Understanding the git pull-request workflow
-------------------------------------------

There are several git workflows out there. I personally like `Vincent Driessen's <https://nvie.com/posts/a-successful-git-branching-model/>`_ workflow. It is over a decade old and followed religiously by several companies. It even comes with an excellent tool called `git flow <https://github.com/nvie/gitflow>`_. It is very well suited for projects which do periodic semantically-versioned releases. However, it can become a bit of a burden and "make your work painful after your projects grow" (See `this <https://reallifeprogramming.com/git-process-that-works-say-no-to-gitflow-50bf2038ccf7>`_ article by Marek Piechut)   

In fact, this guide, based on Marek's article, provides a clean way to effectively collaborate while touching upon the most commonly occuring problems caused by, *ahem*, hitherto uninitiated team members. We will be looking at:

#. The importance of a workflow
#. A step-by-step guide to the workflow
#. Overcoming pitfalls when you're not using the model 

This guide is going to be rife with dense git parlance. If you are not already familiar with them, read `this <https://linuxacademy.com/blog/linux/git-terms-explained/>`_ first.

The need for a workflow
^^^^^^^^^^^^^^^^^^^^^^^

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

#. Undoing a feature addition will be a monumental pain in the neck

#. Those who fork from master, will get broken/incomplete changes.

#. Setting up Continuous Delivery or Continuous Deployment will become challenging.

So what's the remedy? The use of an effective git workflow to ensure that the HEAD of the master is always in a *production-ready* state


The Pull-Request Workflow - Demystified
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The core idea is really simple: Every collaborator works on their own fork of the main repository. They create branches in their forks to develop their feature and once ready, create a pull reuqest in the main repository. After all the tests pass and the maintainer reviews it, the branch is merged into the master branch of the main repository. 

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

     git checkout coverage-tools
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

   If Alice wants to retain Bob's commit history (so that undoing his feature can become easier), she can run::

     git merge --no-ff coverage-tools 

   Note that this will create a dummy merge commit. If she does not want this, then she can use ``Squash and Merge`` to squash all branch commits into one commit without creating a merge commit, or use the regular merge.

#. Bob must now update his fork using the following steps::

     git checkout master
     git fetch --all
     git merge upstream/master
     git push origin master

   It is essential to note that if Bob doesn't update his fork and checks out from the master (whose ``HEAD`` is not in sync with Alice's repo), to add a new feature, it will lead to a textbook case of the fork being some commits ahead and some commits behind master. For e.g. ``5 commits ahead and 3 commits behind alice/awesome-tools``  
