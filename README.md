## Git Tips


### To Revert a File



* git checkout &lt;file>
    * this will overwrite your local copy with the file from your branch


### How to Commit files to the repo

* git add of all modified files to be checked in (e.g. git add -u)_
* git commit
    * This will bring up the editor so you can enter the commit message.  First line is up to 50 characters.  Then put in a blacnk line. Next lines are detailed description.
* git push origin &lt;branch>
    * This pushes our local branch out to the repo

### To get the latest code into our branch via merge

* git checkout &lt;branch>
    * switch branch to the one you want to work on
* git checkout HEAD
    * set our pointer to the top of the branch
* git fetch
    * shows you what is about to happen and sets up git internal stuff
* git merge origin/&lt;branch>
    * actually brings the remote code down to your local copy


### Rebase your branch to master

* git branch - Make sure you’re on the right branch
* git status - Make sure you don’t have anything changed
* git fetch - Get the latest code from remote branches
* git rebase origin/master - Make sure to cross all fingers and toes before running this so there won’t be any conflicts
* git status - If successful, should be empty
* git push -f origin schedule_reports - Need to use the -f since we’ll be in a diverged state.  The warning will say: “Your branch and 'origin/schedule_reports' have diverged and have 60 and 10 different commits each, respectively.   (use "git pull" to merge the remote branch into yours).  Whatever you do, don’t do a git pull!!!


### How to see your changes

* git diff
    * show the changes to modified unstaged files
* git diff --cached
    * show the changes to modified staged files, _i.e. _ones that you already did git add on.
* git diff &lt;sha1> or git show &lt;sha1>
    * show the changes to an already commited change, _e.g. **git diff 5b1822d337945228b474c06f76544051631e45e1**_

### How to merge updates from master to my branch

* git checkout master
* git pull origin
* git checkout &lt;branch>
* git fetch
* git merge origin/&lt;branch>


### How to create a new branch from master

* git checkout -b &lt;new branch> master
* git checkout -b &lt;new branch> &lt;some other branch>
    * this would create a new branch off of another branch that’s not master


### Merge changes from my branch into the parent branch that’s not master

Let’s say my branch is fubar-cli and it’s parent branch is fubar-support.  I have made a bunch of changes in fubar-cli and now I want to push them over into fubar-support.

* git checkout fubar-cli
    * start in my branch
* git rebase origin/fubar-support
* git checkout fubar-support
    * switch to the other branch
* git merge fubar-cli
    * this will bring up the editor to enter a commit description
* git push

### How to get the latest code from master

* `git pull origin master` will get the latest code from the remote master.  `git pull` means `git fetch` followed by `git merge`. It fetches the content from the remote, then merges it into your current branch. But `origin/master` is a local branch (tracking a remote branch). If you want to merge it, you don't need to fetch anything. It's misleading to say `git pull origin/master` when you're not actually fetching from a remote.


### Show the files in a previous commit

* `git show --pretty="" --name-only`

### Get Fred's latest changes from his branch (this is very dangerous, may overwrite your local changes)

* Git reset HEAD --hard origin/fred-branch


### Show commits on my branch not in master

*  git cherry -v master &lt;my_branch_name>

### Who changed a line in a file

* git log -L 1377,1377:./cloud_ui/app/controllers/api/v1/cameras_controller.rb or
* git blame -L 48,+2 dmn/testapp/src/PowerFailTestUtils.cpp

### Compare files from two different branches:

To compare my current file to master:

```
git diff ..master path/to/file
```

The double-dot prefix means "from the current working directory to". You can also say:

* `master..`, i.e. the reverse of above. This is the same as `master`.
* `mybranch..master`, explicitly referencing a state other than the current working tree.
* `v2.0.1..master`, i.e. referencing a tag.
* `[refspec]..[refspec]`, basically anything identifiable as a code state to git.
    * git clone git@github.com:repo1/repo-something.git
    * git fetch git@github.com:repo2/repo-something-else.git
    * git diff master:repo-something/foo-bar/ FETCH_HEAD:repo-something-else/bar-foo/


### Show tagged commits:

git log --no-walk --tags --pretty="%h %d %s" --decorate=full


### To show most recent tag:

git describe --tags


### To reset head to a specific tag:

git reset --hard sprint_19-11-gdafd2aa


### Fix detached head in submodule

You just need to checkout the branch that you’re on again:  


    git checkout next


### Get a file from a different branch

You can use git show for this:


    git show <branch>:file, e.g.


    git show csd/nxrpc_nlm:protocols/nfs/src/Protocols/NLM/nlm_nxrpc.c

If you want to save the file, then you can just redirect the output of git show above into a file


### Edit All Changed files in your branch

You can do this easily using the _--name-only _option to _git diff _by creating an alias like this using so-called backticks:


    alias vimgit='vim `git diff --name-only`'


### Removing an entire commit

From: [https://sethrobertson.github.io/GitFixUm/fixup.html#unpushed](https://sethrobertson.github.io/GitFixUm/fixup.html#unpushed)

I call this operation "cherry-pit" since it is the inverse of a "cherry-pick". You must first identify the SHA of the commit you wish to remove. You can do this using gitk --date-order or using git log --graph --decorate --oneline You are looking for the 40 character SHA-1 hash ID (or the 7 character abbreviation). Yes, if you know the "^" or "~" shortcuts you may use those.

git rebase -p --onto SHA^ SHA

Obviously replace "SHA" with the reference you want to get rid of. The "^" in that command is literal.

However, please be warned. If some of the commits between SHA and the tip of your branch are merge commits, it is possible that git rebase -p will be unable to properly recreate them. Please inspect the resulting merge topology gitk --date-order HEAD ORIG_HEAD and contents to ensure that git did what you wanted. If it did not, there is not really any automated recourse. You can reset back to the commit before the SHA you want to get rid of, and then cherry-pick the normal commits and manually re-merge the "bad" merges. Or you can just suffer with the inappropriate topology (perhaps creating fake merges git merge --ours otherbranch so that subsequent development work on those branches will be properly merged in with the correct merge-base).


### Edit all the files in some previous commit

You can use the _git diff-tree _command to get the list of files from the commit and pass into vim like this:


    _vim `git diff-tree --no-commit-id --name-only -r 1005868ea5e4a769f0fdb18f96580f804966d198`_


### Only show the tracked files in status

For this scenario you want to use the --untracked-files=no option which can be abbreviated as uno like this:

* git status -uno


### Get a single file from a stash

Do _git stash list _to get the list of stashes. And then extract the file you want using _get checkout _like this:

* git checkout stash@{0} --  internal/db/sqlite/sqlstring.go
