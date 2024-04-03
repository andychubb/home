+++
title = 'Git Bits'
date = 2022-02-16T17:18:01Z
draft = true
tags = ['Git', 'Notes']
+++

Just a few git commands that I'm always forgetting...

#### Deleting local branches

```bash
git branch -d <branchname> // If merged
git branch -D <branchname> // If not merged
```

#### Deleting remote branches

```bash
git push origin --delete <branchname>
```

#### Show all the branches that have been merged into the current branch

```bash
git branch --merged
```

#### Remove any remote branches from local that are no longer being tracked

```bash
git remote prune origin
```

#### Sync your branch with main / master from Github

The `synced` command will get your local branch up to date with your main branch (main or master) at origin (ie Github).

```bash
git synced
```

Under the hood, this fetches main or master from origin and performs a [rebase](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase), replaying your commits on top of the tip of main or master.

#### Update your branch with Github’s copy of your branch

If your local branch gets out of date with origin’s version of your branch, then do an update. This would happen if you’re collaborating on the branch with a colleague and need to get up to date.

```bash
git update
```

Under the hood, this fetches origin/your-branch and performs a [rebase](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase).

#### Squash commits

Prior to merging, you likely want to shrink your branch’s many noisy commits to one or two meaningful ones.

```bash
git squash
```