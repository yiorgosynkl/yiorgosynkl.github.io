---
layout: post
title: Git cheatsheet
cover-img: ["assets/img/posts/pexels-git.png"]
---

<!-- # Git Cheatsheet -->

## Essential commands git tutorial

```bash
# basics
### add to the index
git add <file>
### undo add from index
git reset HEAD <file>
### delete from the git version (remove the file in git, not in machine)
git rm --cached <file>
### revert file to version of previous commit
git checkout <commit-hash> -- <file1> <file2>
git checkout c4364d3 -- main.py

# in general we give the following names to the copies of the repos
# local: the one in your machine
# remote (also origin): the fork of the repo (that is under YOUR NAME)
# upstream: the original repo (eventually, your PR will be merged there)

# see last commits
git log --oneline
# delete uncommited changes
git restore <file>
git restore . # or directory

# delete uncommited changes, go to last commit (you can use the id of a commit to go to that)
git reset --hard HEAD

# checkout to a local branch that tracks an existing remote branch
git fetch
git branch -v -a        # you see all the available branches in remote
git switch <branch>     # creates local branch that tracks the remote

# push local branch to remote
git checkout -b <branch>
git push -u origin <branch>

# sync local, origin (my forked repo) and upstream (original repo)
git checkout main
git remote -v   # see links of origin and upstream
git remote add upstream git@bbgithub.dev.bloomberg.com:fxax/<name-of-repo>.git
git fetch upstream       # git pull is git fecth followed by git merge
git merge upstream/main   # local main becomes same with upstream main
git push                    # origin main becomes same with local main
# if you wanna change it
git remote set-url origin git@bbgithub.dev.bloomberg.com:<name-of-org>/<name-of-repo>.git

# git merge explanation
git checkout <feature-branch>
git merge main # in branch feature  (usually)
git merge <source-branch> # in branch <feature-branch>  (general case)
# it brings the changes of source-branch to the feature-branch (where I run the command)
# ideally, everyone works on different stuff, thus after the merge, everything works fine
# however, if two coders changed the exact same piece of code, a conflict occurs
# git will add characters to pinpoint the confilict and a coder resolves it manually
# (the code will not be compilable until the conflict is handled). It looks something like
# <<<<<<< HEAD
#     amSexy = False
# =======
#     amSexy = isSexy("George")
# >>>>>>> main
#     return amSexy
# when coder thinks everything is fine he makes a commit and all are good
# explanatory link: https://superuser.com/questions/224085/git-merge-master-into-a-branch

# update my feature branch (that contains my progress)
# with the updated main
git merge origin/main



# overwrite local cahnges of unstaged file
git checkout -- <file>
git add -p .

# rename branch (local and remote)
git checkout <old_name>
git branch -m <new_name>
git push origin -u <new_name>
git push origin --delete <old_name>

# set a new origin
git remote set-url origin git@bbgithub.dev.bloomberg.com:ggiannakouli/<name-of-repo>.git

# work on remote branch
git branch -v -a  # view all of them
git checkout <branch-name>  # it automatically starts tracking remote

# fast-forward between two branches
# the new-branch contains the changes, the old-branch wants to catchup
git checkout <old-branch>
git merge --ff-only <new-branch>    # only allows merge if it is fast-forward

# merging two branches that both have changes
# if there are confilicts, you have to solve them manually
git merge --ff-only <new-branch>

# update last commit message
git commit --amend -m "blah blah"

# update local copy when renaming master to main
git branch -m master main
git fetch origin
git branch -u origin/main main
```

If you want to swicth to a new branch quickly, use `git stash` and `git stash pop`.

## More resources

* [Updating fork according to original](
https://levelup.gitconnected.com/how-to-update-fork-repo-from-original-repo-b853387dd471)
