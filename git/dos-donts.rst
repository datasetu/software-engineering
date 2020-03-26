General git dos and don'ts
--------------------------

* Always make it a point to add files to your index (staging area) using::

    git add <filename>

  Pressing ``tab`` after ``git add`` will automatically bring up changed files (in most shells). Manually add one file after another or by specifying filenames seperated by spaces in the ``git add``. You can ensure that all your changed files have been added using::

    git status

  Commit all the required files that show up. Do not add binaries like ``.a``, ``.so``, ``.jar``, ``.war`` and so on. Also do not add files that might contain dynamically generated passwords or any kind of secrets. It is a good practice to add these recurring unnecessary files in ``.gitignore``. For e.g. a ``maven`` project's ``.gitignore`` might look like this::

    ./target/*

  **NEVER** use ``git add .`` or ``git add --all`` for you might unknowingly be adding files like the ones mentioned above to the repository.
  
  If this practice sounds a little too pedantic, I urge you to read `this <https://github.com/ChALkeR/notes/blob/master/Do-not-underestimate-credentials-leaks.md>`_.

* When working with a repository with many collaborators, the following is a very common occurrence::

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
