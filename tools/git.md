

# Git-Cheat-Sheet


## How git work?

 + git does not store repeated files during commits. 
 + if a file does not change during time git just store a link of that to identical file.

## Concepts
checksum = hash
tracked file = previously commited or staged
Fast-forward: when your local brnach is ahead of the remote branch

## state of files

1. untracked
2. staged(Added)    ==> new files in the index file
3. commited  ==> safely stored in the local db
4. modified ==> you modified a commited file but not staged again
5. index modified ===> you modified a commited file and add it to staging area but not commited again
6. index deleted ===> you deleted a commited file ==> git log
7. index renamed ===> you rename a commited file then add it to staging area ==> git log

| Code                                      | Meaning             |
| ----------------------------------------- | ------------------- |
| `??` or `U`                               | Untracked file      | 
| `M`                                       | Modified            |
| `A`                                       | Added (staged)      |
| `D`                                       | Deleted             |
| `R`                                       | Renamed             |
| `C`                                       | Copied              |
| `U`                                       | Unmerged / Conflict |
| Left char = staged, Right char = unstaged |                     |
| `!`                                       | Conflict |

## Git workflow

1. working directory  
2. staging area (index or cache): for selecting specific changes for the next commit
3. local repo         
4. remote repo

```
WD                           SA                      L                           R
-----git add   ------------>
<----git reset  ------------ 
-----git rm / clean  ------->
<----git restore ------------ 
                            -------git commit------->
<---------------git reset --soft --------------------
                                                       -----------git push-------->
                                                        <----------git fetch--------
<----------------------------git pull/clone------------------------------------- 
```

## Help

```bash
git status --help           # opens a local html file in the browser
git status -h                # inside termial
```

## Configuration

git configuration level:
    1. system   ==> all users + all repositoreis
    2. global  ==> computer of current user
    3. local  ==> current directory
 
+ these overwite eachother from bottom to up

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
git clone ssh://user@domain.com/repo.git                    # with ssh
git clone http://domain.com/user/repo.git                  # with http
git clone http://domain.com/user/repo.git my_directory     # clone to directory 
```

## SSH

1. open git gui and click on help to create public key
2. github.com -> settings -> ssh and gpg keys -> new ssh key button -> paste your public key
3. git remote set-url origin <url>         # Add remote repository with ssh

## Remote

+ `remote repository`: where your project is hosted
+ `origin`: origin is the default name Git gives to the remote repository
+ `Upstream`: default remote branch that your local branch tracks for push/pull operations

```bash
git remote -v                           # List remote repositories
git remote add origin <url>              # Add remote repository with http
git remote set-url origin <url>         # Add remote repository with ssh
git remote show origin                  # Show remote repository details
git remote add upstream <url>           # Add remote upstream repository
git push origin main	                 # means: ush to main branch on origin
```

------------------------------------------------------------------------

## List of things

```bash
git branch
git log
git tag
git ls-files
git stash list
git diff --name-only
git show ej4sd              # show changes of specifc commit
```


## git status

structure:
 1. branch info
 2. Changes to be committed
 3. Changes not staged for commit
 4. Untracked files

```bash
git status -s                             # short like a list   # -s or --short
git status -b                              # status with branch
git status --untracked-files=no            # hide untracked files
```


## Staging

+ `git restore` is for restoring deleted files but it can do other things
+ `git restore --staged` = `git reset`
+ `git reset` moves the `HEAD pointer` (current branch reference) to a specified commit
+ `git clean` used for untracked files and `git rm` used for commited files

```bash
git add 1.js                        # Stage the file 
git add -p 1.js            # Stage some but not all changes in a file   # -p or --patch is for 
git restore                       #  Errro: fatal: you must specify path(s) to restore
git restore 1.js                      # discard changes of modified file before staging and make it back to its last commited state
git restore del.js                      # restore a deleted commited file
git restore --staged 1.js             # unstage file (both modified and indexed files but not commited files)
git restore --source=abc123 1.js       # restore a file from a specific commit ????
git restore --source=main 1.js       # restore a file from main branch     ?????
git reset                             # unstage all files but keep changes  # equivalent to "git reset --mixed"
git reset 1.js                       # unstage specific file
git reset --mixed 1.js                     # --mixed is default  
git mv 1.js 2.js                       # Move/rename file 
git clean 1.js                         # not works, we should specify a flag
git clean -f 1.js                     # delete untracked file
git clean -f                           # delete all possible files (-d is for possible folders)
git clean -n                          # posible files to delete (untracked files) # -n or --dry-run is for preview
git clean -d -n                        # possible files and folders to delete
git claen -i                           # delete interactive ==> opens a menu with items
git rm 1.js                               # Not works, we should specify a flag
git rm -f 1.js                 # Unstage and delete a commited file ( not work for untracked files)
git rm --cached 2.js                # Unstage in local and delete in in the remote and keep it locally (good for gitignore files in remote)
git rm -r -f bin\               # Unstage and delete directory
```

## Undo

+ when you edit an untracked file or delete a file --> use `Local History extenstion`

```bash
git restore 1.js                        # unstage the deleted file after "git rm -f 1.js" ==> useful before commit the deleted file
git commit --amend                      # Change the last commit that hasn't been pushed yet
git checkout HEAD 1.js                  # Discard changes in specific file
git checkout efj3d 1.js                  # Checkout old version for one file
git reset --hard HEAD                   # Discard all local changes
git reset --hard upstream/master         # Reset to remote branch state
git reset --hard head~2                 # git branch newname e54en ==> you can find commitId in reflog or git log -g
```

## Commits

+  `Don’t amend` pushed commits ---> (rewrites history)

```bash
git commit                                 # git commit witout -m flag opens editor for writing your message. (abort if you dont write)
git commit -m "message"                    # commit all tracked files
git commit -am "message"                   # new commit for modified files without need to "git add"
git commit --amend                          # change the last commit message in editor (Don't amend published commits)
git commit --amend -m "message"             # Change the last commit message that hasn't been pushed yet 
git add newfile && git commit --amend --no-edit               # Change the last commit changes without changing the message   
git commit --date="2023-01-01" -m "message"            # Specify the time of the commit
git commit --allow-empty -m "Trigger CI/CD"             # Create an empty commitg
git commit --fixup 5720fdf -m "message"                # Merge into the specified commit 
```


### 1. split commits

```
WD                         SA                      L                           R
<--------- git reset ---------
<--------------------- git reset --soft --------------
<--------------------- git reset --hard --------------
```

```bash
# 1. unstage
git reset                            # unstage all files but keeps changes in your working directory   
git reset 1.js                        # unstage a specific file
git reset --mixed                      # same "git reset"
git reset --mixed 1.js                 # same "git reset 1.js"

# 2. uncommit 
git reset --mixed Head~1                # Moves HEAD back 1 commit and Unstages changes from that commit    # check with "git log"
git reset --soft Head~1                # delete the last commit  and keep the changes in your working directory
git reset --hard Head~1                # delete the last commit and delete the changes in your working directory

# 3. undo after git reset
git reflog                           #  identify the commit hash
git reset --hard def5678             # reset to commit hash

# 4. uncommit multiple nodes
git reset --mixed e5b2h4                       # Split nodes from this commit but keep the changes in the working directory
git reset --soft e5b2h4                       # Split nodes from this commit but keep the changes in the staging area
git reset --hard e5b2h4                       # Split nodes from this commit and delete the changes
git reset Head~3                              # we can use HEAD like commitID
```

### 2. undo commits

+ `git revert` is a safe, non-destructive Git command
+ `git revert` undos every chanes in every files in a commit
+ `git revert` creates a new commit in git graph
+ unlike `git reset`(which rewrites history), git revert preserves the original commit in hostory and adds a new commit for undoing
+ in summary `git revert` undos a commit by creating  a new commit

```bash
git revert 57cbe4a          # Create a new commit and delete all the changes of that specfic commit 
git reset 57cbe4a              # split all commits and rewrite history
git show 5720fdf                        # see changes of specfic commit (github has a nice dashboard UI)
```

### 3. edit commits

+ `git rebase -i` is a command that rewrites commit history before pushing and sharing with others
+ interactive means there is some steps interactively that we should do

+ interactive rebase allows you to:
    1. Reorder commits
    2. Squash multiple commits
    3. Edit commit messages
    4. Remove or fix commits
    5. Split commits (manually)
    6. if you want to add a commit between older ones, first careate a new one and then change the order

+ Key Actions in Interactive Mode you see in the editor: 
    1. pick	    p	Keep commit as it is
    2. reword	r	Keep commit but edit its message
    3. edit	    e	Pause rebase to let you edit files and `git add` for changes in that commit
        + by this way you can add some changes in an older commit  (modify the commit)
        + run `git commit --amend` after your changes
        + run `git abort --continue` after amend
    4. squash	s	Combine commit with previous one
    5. fixup	f	Like squash but discard commit message (uses first commit message)
    6. drop	    d	Remove commit entirely

```bash
# steps:
# 1. git rebase -i  opens an editor
git rebase -i                 # Rebase local commit's on top of origin/main
git rebase -i origin/main      # we can specify a base bransh or we can specify a hash commit
git rebase -i Head~3                       # Rebase 3 last commits   # -i or --interactive   
git rebase -i 5720fdf                     # Rebase to a particular commit 
git rebase -i --root 57e2df     # Rebase to the root commit 
# 2. re-order commits or delete commits or choose a key action in editor
# 3. finish the process
git rebase --continue                       # Continue an interactive rebase 
git rebase --edit-todo                       # Continue an interactive editor
git rebase --abort                          # Cancel an interactive rebase 
# 4. undo
git reflog                             # we have 3 commits 1. rebase end 2. rebase start
git reset --hard ghi9012               # Reset to the pre-rebase commit (before start)
```

### 4. cherry-pick

+ `git cherry-pick` is a command that copies a specific commit changes from one branch and applies it to current branch
+ by defult cherry-pick creates a new commitId with the same "commit message" since it copy the commit.
```
Source Branch:      A → B → C
                      ↑
Current Branch:     X → Y → B' (copied)
```

```bash
git cherry-pick 57s2f                     # Copy the specific commit to the current branch
git chrry-pick 12dj1 5sk21 15ds2             # chrry-pick multiple commits
git chrry-pick 12hj1 -e                     #  -e or --edit for creating a new commit message
git chrry-pick 12hj1 -x                     #  -x create the same commitId for the new one (by default creates a new)
git chrry-pick 12hj1 -n                     #  cherry-pick without commiting and just staging
git chrry-pick --continue                   # continue the cherry-pick after resolving conflict
git cherry-pick --abort                        # abort the cherry-pick
```


### log

```bash
git reflog                               #  tracks all changes in all branches made to the HEAD [commits, rebases, resets, checkouts, and merges] 
git reflog --since='1.hour'               # day [stores for 90 days]
git log                                 # See commit list of currnet branch
git log -g                               # git log with list of reflog
git log --all                           # See commit list of all branches
git log mybranch                       # See commit list of mybranch
git log -2                              # See just 2 commits
git log  --oneline                         # list of all commits like a short list
git log --patch                         # See commit list and line changes (not beatiful)
git log --graph                         # See commit visualization for branchs
git log --graph --oneline                         # short list visualization
git log --graph --oneline --decorate    # online does not show the full hash (beatiful)
git log --graph --oneline --decorate --all    # for all branches
git log --grep foo                      #See commits with foo in the message
git log --grep="foo"                   # another syntax
git show                             # see changes of current commit # same "git show Head"
git show 5720fdf                        # see changes of hash commit
git show main                           # Show the last commit in a branch
git show HEAD                        # Show the changes of current commit
git show HEAD~1                       # Show the previous commit
git show HEAD~2                       # Show the commit going back two commits
git show HEAD^                       # same "git show Head~1"
git show HEAD^^                      # same "git show Head~2"
git show --stat eh6d7s                 # with a compact view
git blame file.txt                      # See who changed each line and when
```

#### Git Graph

+ A Git graph is a visual representation of a repository's `commit history`
    1. Commits (nodes)
    2. Branches (diverging/converging lines)
    3. Merges (join points)
    4. Tags (milestones)

```bash
git log --graph --oneline --all
git log --graph --pretty=format:'%h -%d %s (%cr) <%an>' --abbrev-commit --date=relative
```


### Commit Best Practices

+  fixing two different bugs should produce two separate commits
+  Don't Commit Half-Done Work




### .gitmessage template

## Branches

```bash
git branch                               # List all branches in local
git branch -v                            # list branches with last commit message on them
git branch -r                           # List all branches in remote
git branch -a                           # List all branches in both local and remote
git branch foo                          # Create a new branch 
git branch -d foo                       # Deletes a branch in local (after deleting they stay in git graph)
git push origin --delete foo            # Delete a branch from remote
git branch -m newName                         # rename the branch
git branch -m master main               # Rename a branch 
git switch foo                          # Switch to a branch (untracked and staged files will stay during swtich)
git switch -                            # switch to master
git switch -c foo                          # -c or --creatte for create and switch to a branch  
git checkout 1.js                     # Undo all changes on the 1.js file   # equivalent to: "git restore 1.js"
git checkout foo                        # Use `git switch` instead bcz checkout also is used for reverse modification
git checkout -b foo                     # Use `git switch -c` instead 
git checkout  1ba4d                      # Switch to a commit (you are in 'detached HEAD' state now. it means you are not on any branch and you should not do any commit in this state bcz after commit when you switch to another branch that commit will get lost. so just use it for debugging)
git push -u origin master               # Push current branch to remote
git push --all                          # Push all branches to remote
```

## Merge

+ `merge types` refer to the different ways branches can be merged: 
    1. fast-forward merge (Default)
    2. Recursive Merge (3-Way Merge)
    3. squash merge
    4. No Fast-Forward Merge (--no-ff)
    5. Octopus Merge
    6. rebase merge

```bash
git log --merges                       # See all merge commits
git merge b1                           # Merge branch b1 into current branch    # message commit automatically: "Merge branch 'b1"
git merge b1 -m "message"             # merge with our custome commit message
git merge b1 b2 b3                     # Order is not important, it merges all branches into current (Octopus merge)
git merge git --ff-only b1              # only merge if it can be fast-forwarded otherwise gets error
git merge --no-ff b1                    # Forces Git to always create a merge commit, even if fast-forward is possible
git merge --squash b1                     # does not create a merge commit but sqashes all the branch commits into singel staged chage 
git merge --abort                      # Cancel merge if there is conflict
git rebase b1                              # Rebase branch b1 into current branch
git rebase -i HEAD~3                        #  in an interactive rebase we can squash commit nodes of one branch
```

### ASCII diagrams:

```
1. Fast-Forward Merge 
------------------------------------------

main:    A──B──C
                ↓                             # the only merge type that does not create a new commit
feature:       D──E                            # git merge feature
                                            # when the current branch(main) is directly behind the target branch
main:    A──B──C──D──E                     # makes history linear



1. No Fast-Forward Merge
------------------------------------------

main:    A──B──C                              # Forces Git to always create a merge commit, even if fast-forward is possible.
                ↓                             # git merge --no-ff feature
feature:       D──E
                                               # new commit node with a commit message will be create automatically 
main:    A──B──C─────K
                |   /
feature:        D──E


1. Recursive Merge (3-Way Merge)
------------------------------------------

main:      A────B────C────D────E
                      \
feature:              F────G────H────I────J
                                                            # when branches have diverged
                                                            # git merge feature
main:      A────B────C────────────────D────E────K          # git merge creates a new merge commit (K)
                      \                       /            # the commit message automatically created 
feature:                F────G────H────I────J



1. squash
------------------------------------------

main:    A──B──C───D 
               |    
feature:       E──F──G──H

                                                            # Combines all commits from the source branch into a single commit on the target branch
main:    A──B──C───D───(S) (squashed commit E+F+G+H)         # git merge --squash feature
               |                                            # squash discards source branch history with no merge relationship 
feature:       E──F──G──H                                   # after squash you need to commit 



1. Octopus Merge
-------------------------------------------

main:    A──B──C───D                              # Used to merge more than two branches at once 
               |                                   # git merge b1 b2 b3
feature:       E────F                             # # Only works if there are no conflicts.
                    |──G──H
                        |──I──J

main:    A──B──C───D────────────────K                # Octopus wil create a new commit automatically
               |                    / 
b1:             E────F             /  
b2:                  |──G──H      /        
b3:                      |──I──J─/


1. rebase
-------------------------------------------

main:      A────B────C────D────E                                  # git rebase feature
                      \                                          # make two branches into one line 
feature:                F────G────H────I────J                   # change the base of currnet branch by the new one
                                                                # makes history linear
main:                           A────B────C────D────E
                                /
feature:    F'───G'───H'───I'───J'
```


##### Merge Strategies

+ `merge strategy` is the algorithm Git uses to combine the changes from different branches when you run a git merge

```bash
git merge b1                      # 
git merge -s recursive b1                      # recursive is default
git merge -s ours b1                            # resolves all conflicts by ignoring incoming changes 
git merge -s recursive -X theirs feature          #  resolves all conflicts by preferring the incoming
```


## Conflict

1. When Branch A chanes a file and merges into main then Branch B changes the same file and wants to merge with main.
2. When Branch A changes a file and wants to get the changes from Branch B.
+ In conflicts you get this error: Automatic merge failed; fix conflicts and then commit the result.

```bash
git status                  # see files in conflict
git add 3.js                  # Mark file as resolved
git rm 2.js                   # Remove resolved file
```

```bash
# when you have conflict, this notation injects in your conflicted files
<<<<<<    # new branch -----> current file
=====    # border between two branchs
>>>>>>    # basic branch  -----> Incoming change
# after conflict you need to commit and push your code
```


## Pulling

pull request = merge request
```bash
git pull --rebase --prune               # Get latest, rebase any changes not checked in and delete branches that no longer exist | 
```

## Push Pull Fetch

#### 1. git fetch

+ personally I dont use `git fetch`
+ `git fetch` downloads all changes from the remote repository, but does NOT merge or change your local branches
+ `git fetch` creates a new branch (origin/main) and you need to merge this branch to "main"

```bash
# 1. fetch
git fetch                                 # Fetches all branches from the default remote (origin)
git fetch origin                          # fetch
git fetch upstream                      # Fetch all remote branches
git fetch --all                            # Fetches updates from all remotes
git fetch origin mybrnach               # Fetch a specific branch 
# 2. check
git status
git checkout origin/master                  # by default changes does not apply in the files, check git graph
git diff main origin/main                  # check changes in termianl 
# 3. merge
git merge                               # it does not merge all branches
```
#### 2. git pull

+ `git pull` = `git fetch` + `git merge`
+ if you have commits in the local and then use `git pull` it will create a new commit with a default commti message automatically.
+ always pull before push

```bash
git pull                                #  does not merge all branches, download info for all branches but just merge for currnet branch
git pull origin mybranch              # Pull a specific branch and merge with my local current branch
git pull --rebase                      # git fetch + git rebase
git pull --ff-only                      # It will only update your branch if it can do so without creating a merge commit
git pull --no-commit                   # something like "git fetch" which creates a remote branch and does not merge actually
git pull --autostash
```

#### pull Strategies

1. merge pull (git pull = git fetch + git merge)
2. Rebase Pull (git pull --rebase = git fetch + git rebase)
3. Fast-Forward Only (git pull --ff-only)

#### 3. git push

```bash
git push                                # 	Only works if the branch is tracking a remote branch
git push origin main                     # Push a specific branch to the remote (origin)
git push origin -u main           # -u or --set-upstram  sets up a tracking relationship between your local branch and the remote branch (you can push with "git push" next time)
git push --all origin                  # push all local branches
git push origin b1 b2 b3               # push multiple branches
git push --tags                            # Push all tags
git push --delete origin b1                 # delete a branch
git push --force                        # Push any changes while overwriting any remote changes 
git push --force-with-lease             # Push any changes but stop if there are any remote changes
git rebase upstream/main                # Refresh main branch from upstream  ????
```
## Pull request = Merge request

+ A collaboration feature used to propose changes of your branch/ your fork to a repository
+ by this you request a review and discuss with a team member and ask the owner to pull your code and merge them with master
+ In public repositories, a person should fork the repository and then create a pull request
+ In private repositories, as a collaborator we create an `Issue`, then for each Issue we create a branch, then send `comparision and pull request` and finally the owner comes and merge. After merging we delete the branch.



## Compare

```bash
git diff                                # Shows unstaged changes (working vs. index)
git diff --staged                       # Shows staged changes (index vs. last commit)
git diff HEAD                            # Shows all changes (working + staged vs. last commit)
git diff commit1 commit2                 # Changes between two commits
git diff branch1 branch2                 # changes between two branches
git diff HEAD HEAD~2                    # changes between two branches
git diff origin/main main                 # changes between main in local and main in remote
git diff 1.js                           # unstaged changes in a specfic file
git diff --stat 1.js                     # stat for a specific file
git diff --stat                          # stat of all files that have unstaged changes
git diff --name-only                    # name of all files that have unstaged changes
```

## Stash

+ `git stash` lets you temporarily save your uncommitted changes (both staged and unstaged) without committing them
+ so you can work on something else (like switching branches)

```bash
git stash                              # Stash working area
git stash list                          # List stashes
git stash -a                             # all even .gitignore files
git stash --include-untracked \ -u           # Stash working area and staged files
git stash push -m "Message"             # Stash staged files
git stash --staged                      # Stash staged files
git stash apply                         # Moved last stash to working area
git stash apply 0                       # Moved named stash to working area
git stash pop                           # Pop last stash and remove it from stash list
git stash branch foo                    # Create a new branch from stash (default first stash)
git stash drop 0                        # Remove named stash
git stash clear                         # Clear all stash list
```


## Tags

+ `git tag` is a label you attach to a specific commit to mark it as important
+ typically used to mark releases like: `v1.0`, `v2.1.3`

```bash
git tag                                              # List all tags
git tag -a 0.0.1 -m "Message"                       # Create an annotated tag -a or --annotate    # -m or --message
git tag -d 0.0.1                                      # -d or --delete for delete a tag
git push --tags                                      # Push tags to remote repository
git checkout v1.0                                     # checkout to a tag (You're now in detached HEAD state)
```

## Search

```bash
git grep "foo"                                 # Search for text in all files
git grep "foo" v2.5                              # Search in specific version
git log -S 'foo'                               # find commits with 'foo' in their commit messages
git log -S 'foo' --pickaxe-regex               # seacrh with regex
```

## Shortcuts

```bash
git config --global alias.lg "log --oneline --graph --decorate --all"            # lg ----> Pretty commit graph
git config --global alias.st status                                              # st ---> git status
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


## GUI Tools

1. GitKraken: Interactive graph with drag-and-drop.
2. VS Code GitLens: Sidebar graph visualization.
3. Sourcetree: Visual branch/merge history.

## Github flow

## git-flow-avh

## git-extra

## git public registry address

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
















