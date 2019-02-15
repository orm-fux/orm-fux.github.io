---
layout: post
title:  Git Commit History and Resolving Conflicts
date:   2019-02-15 12:21:00
tags: git
mathjax: false
---

So, when working with Git you will inevitably run into the topic of "conflicts" at some point. You'll find information of how to resolve them all over the place [[1](https://stackoverflow.com/q/161813), [2](https://gist.github.com/karenyyng/f19ff75c60f18b4b8149)]. However, I want to quickly point out two different ways for the conflict resolution under a "special" aspect: Commit history. Do you and your co-developers want to see in your commit history that there was a conflict that needed resolution? `git merge` will show it; and `git rebase` will hide it [[3](https://stackoverflow.com/a/44476803)].

## Resolving Conflicts

Let's say we have the following commit history, where commit `D` is the latest remote commit and `E` your latest local commit (with a conflict between those two):

```text
A-B-C-D <-- origin/master
    \
     -E <-- master
```

The command sequence for handling the conflict with `merge` would be:

```bash
git fetch origin master;
//will "fail", because of conflict
git merge master;
//resolve conflicts in the files (mergetool or simple file editing)
vi conflicted_file;
git add conflicted_file;
git merge --continue;
```

And with `rebase` this is very similar:
```bash
git fetch origin master;
//will "fail", because of conflict
git rebase master;
//resolve conflicts in the files (mergetool or simple file editing)
vi conflicted_file;
git add conflicted_file;
git rebase --continue;
```

The real difference is what is happening behind the scenes.

## The `merge` History

Because we cannot do a fast forward of our commit pointer we end up with `merge` with an explicit _merge commit_. So the commit history will look like this after the conflict:
```text
A-B-C-D <-- origin/master
    \ \
     \ ---F <-- master
      \   /     
       -E-
```

The commit `F` makes it clear that we had some deviation in your development, which we needed to bring together afterwards. Once the changes are pushed to the remote the conflict resolution will be visible for everybody.

## The `rebase` History

Now, with `rebase` the commit that we treat as base of your changes is switched. So, there will be this commit history instead:
```
A-B-C-D <-- origin/master
      \
       - E <-- master
```

The conflict is completely hidden from the history. It seems as if the change was always based (Well, that's what `rebase` is supposed to do!) on the remote commit that resulted in a conflict.

## Which to use?

Which method is used depends on you and your colleges. Do you generally want to see or hide your conflicts? Or do you want to work on a "per case" basis - i.e. don't hide complex conflicts and hide the trivial ones?
