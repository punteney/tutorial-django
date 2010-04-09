Cheat Sheet
=================

Virtualenv/Virtualenvwrapper
----------------------------------------------------
workon - Lists all the available virtualenvs

workon ENV_NAME - Switches to the virtualenv specified by the ENV_NAME ``workon zoo`` would activate the 'zoo' virtualenv.

deactivate - Deactivates the current virtualenv.

mkvirtualenv --no-site-packages ENV_NAME - Creates a new virtualenv named ENV_NAME, without any of the site wide python packages.

rmvirtualenv ENV_NAME - Removes the virtualenv named ENV_NAME.


Git
----------------------------
gst or git status - Displays the status of the local repository, what files are changed, ready for commit, etc.

ga or git add - Command to add new or changed files to be committed to the repository.

gcm or git commit - Command to commit the currently added changes usually use with the "-m" flag to append a message ``gcm -m "Fixing a broken link"`` to the commit.

gps or git push - Pushes all committed changes on the current branch to the remote repository
gpsa git push --all - Same as git push except it pushes all the branches of the repository instead of just the current one.

gpl or git pull - Pulls changes down from the remote repository and attempts to merge them.

gdf or gdiff or git diff - Shows the diff between the local file and the committed file. This can specify a single file or the whole repository.
