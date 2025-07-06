

# Git-Cheat-Sheet


# How git work?
git does not store repeated files during commits. if a file does not change during time git just store a link of that to identical file.

## Concepts
checksum = hash

# state of files
1. untracked
2. staged(Added)    ==> new files in the index file
3. commited  ==> safely stored in the local db
4. modified ==> you modified a commited file but not staged again
5. index modified ===> you modified a commited file and add it to staging area but not commited again
6. index deleted ===> you deleted a commited file ==> git log
7. index renamed ===> you rename a commited file then add it to staging area ==> git log

# Git workflow
1. working directory  
2. staging area       
3. local repo         
4. remote repo


WD                           SA                      L                           R
-----git add------->
<----git reset --soft ------ =git restore --staged 
                            -------git commit------->
<---------------git reset --mixed--------------------
                                                       -----------git push-------->
                                                        <----------git fetch--------
<----------------------------git pull/clone------------------------------ 

## Help
```bash
git status --help           # opens a local html file in the browser
git status -h                # inside termial
```

## Configuration
git configuration level:
1. system   ==> all users + all repositoreis
2. gloabl  ==> computer of current user
3. local  ==> current directory
these overwite eachother from bottom to up

```bash
git config --global user.name "foo"               # Set user name
git config --global user.email "foo@example.com"  # Set user email
git config user.name                              # show user.name
git config --list                                 # show current config
git config --local --list                         # show repo config in <repo>/.git/config
git config --global --list                         # show system config in ~/.gitconfig
git config --system --list                        # in /etc/gitconfig
git config --global color.ui auto
git config --global core.editor vi
```
## Git clone
```bash
git clone ssh://user@domain.com/repo.git            # with ssh
git clone http://domain.com/user/repo.git           # with http
```
## SSH
1. open git gui and click on help to create public key
2. github.com -> settings -> ssh and gpg keys -> new ssh key button -> paste your public key
3. git remote set-url origin <url>         # Add remote repository with ssh

## Remote
```bash
git remote -v                           # List remote repositories
git remote add origin <url>              # Add remote repository with http
git remote set-url origin <url>         # Add remote repository with ssh
git remote show origin                  # Show remote repository details
git remote add upstream <url>           # Add remote upstream repository
```
## Log

```bash
git status -s                             # short like list
git log  --oneline                         # like list
git reflog                               #  tracks all changes made to the HEAD [commits, rebases, resets, checkouts, and merges] [stores for 90 days]
git reflog --since='1.hour'               # day
git log -g                               # git log with list of reflog
git log                                 #See commit list of currnet branch
git log --all                           #See commit list of all branches
git log mybranch                       #See commit list of mybranch
git log -2                              #See just 2 commits
git log --patch                         #See commit list and line changes (not beatiful)
git log --graph                         #See commit visualization for branchs
git log --decorate --graph --oneline    # online does not show the full hash (beatiful)
git log --grep foo                      #See commits with foo in the message
git show HEAD   or git show                        #Show the current commit
git show HEAD~1 or  git show HEAD^     #Show the previous commit
git show HEAD~2 or git show HEAD^^    #Show the commit going back two commits
git show main                           #Show the last commit in a branch
git show 5720fdf                        #Show named commit
git blame file.txt                      #See who changed each line and when
```
## Staged Changes
```bash
git add 1.js                        # Stage the file 
git add -p\--patch 1.js            # Stage some but not all changes in a file 
git restore 1.js                      # discard changes of modified file before staging [opposite of git add]
git restore --staged 1.js             # unstage file (both modified and indexed files)
git reset                             # unstage files(not work for modified files) [--mixed is default]
git reset 1.js                       # unstage specific file
git mv 1.js 2.js              # Move/rename file 
git clean -f 1.js                     # delete file (clean used for untracked files and rm used for commited files)
git clean -f                           # delete all possible files (-d is for possible folders)
git clean -d --dry-run\ -n             # possible files to delete. -d is for possible folders
git clean -f\--force -d                # Recursively remove untracked files from the working tree 
git clean -f\--force -d -x             # Recursively remove untracked and ignored files from the workingtree
git claen -i                           # delete interactive ==> opens a menu with items
git rm --cached 2.js                # Unstage file (opposite of git add)
git rm --force 1.js                 # Unstage and delete a commited file( not work for untracked files)
git rm 1.js                               # Unstage and delete a commited file( not work for untracked files)
git rm -r --force bin\               # Unstage and delete directory
```

## Recover file
```bash
git reset 1.js                       # in your editor go to timeline or install local hostory extension(not useful for deleted files)
delete a file                        # in vscode command pallet search for "Local History: Find Entry to Restore"
git rm -f 1.js                        # git restore 1.js ==> unstage the deleted file ==> useful before commit the deleted file
git reset --hard head~2                 # git branch newname e54en ==> you can find commitId in reflog or git log -g
```

## Commits
```bash
git commit       # git commit witout -m flag opens editor for writing your message, and if you do not write the message it will abort the commit
git commit -a    # new commit for changes in all tracked files without needing to staging(ignore new files)
git commit --amend # change the last commit (Don't amend published commits)
git commit --amend -m "New message"         # Change the last commit message that hasn't been pushed yet 
git commit --amend --no-edit               # Change the last commit changes without changing the message
git commit --fixup 5720fdf -m "New message" # Merge into the specified commit 
git reset --mixed e5b2h4                       # Split nodes from this commit but keep the changes in the working directory
git reset --soft e5b2h4                       # Split nodes from this commit but keep the changes in the staging area
git reset --hard e5b2h4                       # Split nodes from this commit and delete the changes
git reset Head~1                          # we can use HEAD like commitID
git revert 57cbe4a                             # Create a new commit and delete all the changes of that specfic commit (not split all commits like reset) 
git rebase --interactive [origin/main]      # Rebase a PR (`git pull` first) 
git rebase --interactive 5720fdf            # Rebase to a particular commit 
git rebase --interactive --root 57e2df     # Rebase to the root commit 
git rebase --continue                       # Continue an interactive rebase 
git rebase --edit-todo                       # Continue an interactive rebase
git rebase --abort                          # Cancel an interactive rebase 
git cherry-pick 57s2f                     # Copy the specific commit to the current branch
git chrry-pick 12dj1 5sk21 15ds2             # chrry-pick multiple commits
git chrry-pick 12hj1 -x                     # by defult cherry-pick creates a new commitId since it copy the commit. -x create the same commitId for the new one 
git chrry-pick --continue                   # continue the cherry-pick after resolving conflict
```
## Edit pervious commit messages
you need to do some stpes
```bash
1. git rebase -i HEAD~N                   # How many commits you wnat back? repalce with N
2. replace "pick" with "edit" in the interactive rebase editor       # this command delete some changes and files
3. git add .                              # you can do some changes also in working directory
4. git commit --amend                     # this command open the rebase editor for writing your message
5. git rebase --continue                  # this command recover deleted files and changes
6. git push --force origin                # if the wrong commit is in the remote repository you need to push with force
```
## Branches
github -> insights -> network
```bash
git branch foo                          # Create a new branch 
git branch -d foo                       # Deletes a branch in local (after deleting they stay in git graph)
git push origin --delete foo            # Delete a branch from remote
git branch                               # List all branches in local
git branch -r                           # List all branches in remote
git branch -a                           # List all branches in both local and remote
git branch -m 2.js                         # rename the branch
git switch foo                          # Switch to a branch (untracked and staged files will stay during swtich)
git switch -                            # switch to master
git switch --create foo             # Create and switch to a branch 
git restore foo.js                      # Undo all changes on the foo.js file 
git checkout foo.js                     # Undo all changes on the foo.js file 
git checkout foo                        # Use `git switch` instead bcz checkout also is used for reverse modification
git checkout -b foo                     # Use `git switch -c` instead 
git checkout  1ba4d                      # Switch to a commit (you are in 'detached HEAD' state now. it means you are not on any branch and you should not do any commit in this state bcz after commit when you switch to another branch that commit will get lost. so just use it for debugging)
git push -u origin master               # Push current branch to remote
git push --all                          # Push all branches to remote
```
## Merge
rebase = make two branches into one line (change the base of currnet branch by the new one)
merge --squash = make new branche into one node then merge
rebase -i HEAD~3 = squash 3 last commits of currnet branch into one node
```bash
git merge foo                           # Merge branch foo into current branch 
git merge bar foo                     # Order is not important, it merges another branch into current
git merge --squash bar                  # Merge branch bar into current branch but squash all commits of the branch into one commit (you need git commit after this command)
git rebase -i HEAD~3                   # squash 3 last commits of currnet branch into one node 
git log --merges                       # See all merge commits
git merge --abort                      # Cancel merge
git rebase foo                              # Rebase branch foo into current branch
```
## Conflict

1. When Branch A chanes a file and merges into main then Branch B changes the same file and wants to merge with main.
2. When Branch A changes a file and wants to get the changes from Branch B.
+ In conflicts you get this error: Automatic merge failed; fix conflicts and then commit the result.
```bash
# when you have conflict, this notation injects in your conflicted files
<<<<<<    # new branch
=====    # border between two branchs
>>>>>>    # basic branch
# afte conflict you need to commit and push your code
```
## Pulling
pull request = merge request
```bash
git pull --rebase --prune               # Get latest, rebase any changes not checked in and delete branches that no longer exist | 
```

## Push Pull Fetch
+ always pull before push
+ Fast-forward: when your local brnach is ahead of the remote branch.
```bash
git push origin -u master               # connect my local master branch with remote master (you can push with "git push" next time)
git fetch upstream                      # Fetch all remote branches
git fetch                               # fetch the changes of all branches from remote. you can use it for check that is there anything new?[you need to use 'git merge' after that but it does not merge all branches] ["git diff origin/master" and "git staus" ] [you can checkout to new origin branches]
git fetch origin mybrnach               # Fetch a specific branch 
git pull                                # git pull = git fetch + git merge [does not merge all branches, download info for all branches but just merge for currnet branch]
git pull origin mybranch              # Pull a specific branch and merge with my local current branch
git pull --rebase                      # git fetch + git rebase
git rebase upstream/main                # Refresh main branch from upstream
git push --force                        # Push any changes while overwriting any remote changes 
git push --force-with-lease             # Push any changes but stop if there are any remote changes
```
## Pull request = Merge request
+ A collaboration feature used to propose changes of your branch/ your fork to a repository and request a review and discuss with a team member and ask the owner to pull your code and merge them with master.
+ In publick repositories, a person should fork the repository and then create a pull request.
+ In private repositories, as a collaborator we create an `Issue`, then for each Issue we create a branch, then send `comparision and pull request` and finally the owner comes and merge. After merging we delete the branch.



## Compare
```bash
git diff                                # See difference between working area and current branch 
git diff 1.js
git diff HEAD HEAD~2                    # See difference between te current commit and two previous commits 
git diff main other                     # See difference between two branches 
```
## Stash

```bash
git stash                              # Stash working area
git stash push -m "Message"             # Stash staged files
git stash -a                             # all even .gitignore files
git stash --include-untracked \ -u           # Stash working area and staged files
git stash --staged                      # Stash staged files
git stash list                          # List stashes
git stash apply                         # Moved last stash to working area
git stash apply 0                       # Moved named stash to working area
git stash pop                           # Pop last stash and remove it from stash list
git stash branch foo                    # Create a new branch from stash (default first stash)
git stash drop 0                        # Remove named stash
git stash clear                         # Clear all stash list
```
## Tags
```bash
git tag`                                              # List all tags
git tag -a\--annotate 0.0.1 -m\--message "Message"` # Create a tag
git tag -d\--delete 0.0.1`                           # Delete a tag
git push --tags`                                      # Push tags to remote repository
```
## Submodules
```bash
git submodule status`                    # Check status of all submodules 

- Pull submodules
  1. `git submodule sync`
  2. `git submodule init`
  3. `git submodule update`
- Change branch
  1. `cd /submodule`
  2. `git fetch origin <branch-name>`
  3. `git checkout <branch-name>`
  4. `cd /`
```

  ## Git flow
1. main  ===> ready for production + being tagged to version
2. develope
3. hotfix ==> bugfixing needs to merger immediately into main + develope
4. feature
5. support  ==> developing for pervoiuse versions
6. release ==> for testing pre-production

## Github flow

## git-extra

## windows essential commands
```bash
echo "" > new.js         # create a new file
type new.js              # show the content of the file
```
## Vscode
Offset circle ===> commits from merging
regular circle ===> regular commits
share icon ===> brnach
fully center icon ===> base

## Happy git rules
1. Always create branches from master, not other peoples branches.
2. Force update and  change commit history only on your branches.
3. Use --force-with-lease instead of --force to avoid merge conflicts.
4. Always rebase on the origin master brnach before creating a pull request.

