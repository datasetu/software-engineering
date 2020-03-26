Quick Checklist
---------------

**NOTE**: This is still WIP

* Make sure you're following the pull-request workflow to the letter (See :doc:`this <pull-request-model>`). Quick summary::

    #You are on your fork
    #You've made new changes to be committed
    #Switch to a new branch if you haven't already
    git checkout -b <branch>
    git add <file1> <file2> ...
    git commit

    git checkout master
    git fetch --all
    git merge upstream/master
    git push origin master

    git checkout <branch>
    git rebase master

    #Fix conflicts and then
    git add <conflict-free-file>
    git rebase --continue

    #If no conflicts then
    git push origin <branch>

    #Finally, head over to github and create a PR

    #After PR gets accepted
    git checkout master
    git fetch --all
    git merge upstream/master
    git push origin master

    #Repeat process to add new feature

* When you're following the model make sure your changes are on a branch and not on master. If they are on master, then use ``git checkout -b <branch>`` to switch to a new branch with the changes.
* Have a ``.gitignore`` file to avoid adding unwanted files
* Always use ``git add <filename>`` instead of ``git add --all`` or ``git add .``
* Commit messages should be descriptive (See :doc:`this <dos-donts>`)
* Do not forget to rebase with master before pushing to your branch::
    
    git checkout <branch>
    git rebase master

    #Fix conflicts and then
    git add <conflict-free-file>
    git rebase --continue

    git push origin <branch>

* If there is a merge conflict, **never** use ``git push -f <branch>`` or ``git checkout --theirs <file>``. Talk to your TL right away.
