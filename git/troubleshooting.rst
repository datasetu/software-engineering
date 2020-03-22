Troubleshooting guide
---------------------

Disclaimer: Use these commands only if know exactly what you're getting into. Actually if you're here, you should be talking to your TL

Merge conflict hell 
^^^^^^^^^^^^^^^^^^^

Sometimes merge conflicts can become very problemmatic if the modules have not been divided properly between devs. So running::

  git rebase master

on the feature branch will result in a ton of conflicts. If you're absolutely certain that your changes are the ones that need to be retained, then run (*proceed with utmost caution*) ::

  git checkout --theirs <file-with-tonnes-of-conflicts>
  git add <conflict-free-file>
  git rebase --continue

Or, if the changes in master are more relevant than your feature branch (a rare case), then use::

  git checkout --ours <file-with-tonnes-of-conflicts>
 
And continue rebasing

X commits ahead and Y commits behind
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Provided your PR was merged, the following should work (*red alert*)::

  git pull --rebase upstream master
  git push --force-with-lease origin master
