When not to use this workflow 
-----------------------------

Following the pull-request workflow for every repository you push to can become quite cumbersome. Say you have a repository for your CV, following this workflow is unnecessarily complicated (even if there's a friend helping out with your CV). 

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
