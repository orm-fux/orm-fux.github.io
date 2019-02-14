---
layout: post
title:  Git Basic Command Cheatsheet
date:   2019-02-14 14:15:00
tags: git cheatsheet
excerpt: A small overview of basic Git commands.
mathjax: false
---

## File Actions

Action | Command 
-------|--------
Add file to stage | `git add <files>`
Mark file as deleted | `git rm <files>`
Rename file | `git mv <source> <target>`
Commit stage to local repo | `git commit <files>`
Revert non-staged file | `git checkout -- <files>`
Remove from stage, keep changes | `git reset [--mixed] <files>`
Fully revert staged file | `git reset --hard <files>`
Fully revert working copy | `git reset --hard; git clean –fd;`

## Branches

Action | Command 
-------|--------
Show all local branches | `git branch`
Show all remote branches | `git branch –r`
Show current branch | `git log -1 --decorate --oneline`
Create branch | `git branch <branchName>`
Switch to branch | `git checkout <branchName>`
Create & switch to branch | `git checkout –b <branchName>`
Create branch from tag | `git checkout –b <tagName>`
Delete branch | `git branch –d <branchName>`
Merge other into current branch | `git merge --no-commit --no-ff <branchName>`

## Tags

Action | Command
-------|--------
List tags | `git tag`
Search for tag | `git tag –l "<tagName>"`
Show tag info | `git show <tagName>`
Checkout tag | `git checkout <tagName>`
Create local tag on HEAD | `git tag –a <tagName>`
Create local tag on specific commit | `git tag –a <tagName> <commitChecksum>`

## Remote Actions 

### Without workspace change

Action | Command
-------|--------
Retrieve for current branch | `git fetch <remote>`
Retrieve for all branches | `git fetch --all`
Transfer to remote | `git push <remote> <branch>`
Transfer tag to remote | `git push <remote> <tagName>`
Add remote | `git remote add <remote> <url>`
Remove remote | `git remote remove <remote>`

### With workspace change

Action | Command
-------|--------
Retrieve for current branch | `git pull <remote>` or `git fetch <remote> && git merge`
Retrieve for all branches | `git pull --all`
Retrieve new remote branch | `git checkout --track <remote>/<branchName>`

## Repository Creation

Action | Command
-------|--------
New Git repository | `git init`
Initial clone of remote repository | `git clone <repoUrl>`
Git repo for server deployment | `git clone --bare --no-hardlinks <source> <target>`

## History

Action | Command
-------|--------
Show un-staged changes | `git diff <file>`
Show staged changes | `git diff --staged <file>`
General workspace state | `git status -s`
Show last "n" commits | `git log –n`
Pretty format commit history | `git log --pretty=format:"%h - %an, %ar : %s" --graph`