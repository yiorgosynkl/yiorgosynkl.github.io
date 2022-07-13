---
layout: post
title: Git cheatsheet
cover-img: ["assets/img/posts/pexels-git.png"]
---

<!-- # Git Cheatsheet -->

## Essential commands git tutorial

```bash
# see last commits
git log --oneline 

# delete uncommited changes, go to last commit (you can use the id of a commit to go to that)
git reset --hard HEAD

# push local branch to remote
git checkout -b <branch>
git push -u origin <branch>

# sync local, origin (my forked repo) and upstream (original repo) 
git checkout master
git remote -v   # see links of origin and upstream 
git remote add upstream git@bbgithub.dev.bloomberg.com:fxax/<name-of-repo>.git 
git fetch upstream       # git pull is git fecth followed by git merge
git merge upstream/master   # local master becomes same with upstream master
git push                    # origin master becomes same with local master
# if you wanna change it
git remote set-url origin git@bbgithub.dev.bloomberg.com:<name-of-org>/<name-of-repo>.git

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
```

If you want to swicth to a new branch quickly, use `git stash` and `git stash pop`.

## More resources

* [Updating fork according to original](
https://levelup.gitconnected.com/how-to-update-fork-repo-from-original-repo-b853387dd471)
