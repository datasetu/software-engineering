Some good practices for the workflow
====================================

* It is not a good idea to open pull requests from the master branch of your fork. (See `this <https://blog.jasonmeridth.com/posts/do-not-issue-pull-requests-from-your-master-branch/>`_) This is mainly because all commits to ``origin master``, after opening the PR and before getting accepted, become a part of the pull request. 

* Feature branches can be named ``feature/*``, bugfixes as ``bugfix/*`` and hotfixes as ``hotfix/*``. This is purely optional, but it makes it easier for other developers to know what was done in the branch.

* The above convention might also come in handy when configuring automated docker builds for your repository. For e.g. docker image update branch names can be ``docker/*`` and the appropriate regex can be configured in docker hub. 

* Merging conventions

  - If you want to retain the commit history of the branch, then use the ``fast forward`` option while merging::

      git merge --no-ff <branch>

    Doing this helps in identifying the exact commits where the feature started and ended.

  - If the branch has a lot of silly commits (maybe you were trying to get travis to work), say something like this:

    .. image:: https://miro.medium.com/max/663/1*H78wgOYCyDH1njyW0LOokg.png
       :align: center

    Surely, you wouldn't want your clean git log to now show up these commits (these are terrible commit messages by the way). So the solution is to ``Squash and Merge``. If you're doing it on the terminal, then::

      git checkout master
      git merge --squash <branch>
      git commit
      git push origin master

  - The third option is to just use ``merge`` which fast-forwards commits by default. If done on the terminal::

      git checkout master
      git merge <branch>
      git commit
      git push origin master
