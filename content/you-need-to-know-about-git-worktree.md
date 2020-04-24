+++
title = "You Need to Know About Git Worktree"
date = 2020-03-30T20:24:06+02:00
[taxonomies]
tags = ["git", "tips"]
+++

I've been using Git for source control for many years, but there's still lots of things you can do in Git that I haven't even discovered yet. `git-worktree` was one of those things. Thanks to [codingblocks.net](https://www.codingblocks.net/) for mentioning this in one of their episodes.

# The problem

I often find it's useful to explain the problem before describing the solution; so here goes:

If you are working in a team or on an open source project with multiple contributers, you might get the need to check out another branch. This is easily done, but it becomes a bit of a hassle if you have a lot of uncomitted changes. You could put all those changes in a stash, but then you usually end up with a huge pile of stashes over time because you never take the time to clean up. What you really want is the ability to check out two (or maybe more) branches at the same time.

# The solution

**Enter git-worktree!**

With this simple command you'll be able to check out a different branch in your repository, in a seperate directory. It looks like and is used like a regular git repository, but it allows you to have multiple active working trees in the same repository at the same time. Here's how to get going:

```
git worktree add ../another-directory
```

This will create a new worktree in `another-directory`, which is a sibling of the current directory. At the same time it will also create a new branch with the same name as the directory.

If you'd like to specify the branch name, you can use the `-b <branch-name>` option:

```
git worktree add -b new-branch ../another-directory
```

It's also easy to check out an existing branch in the new directory by specifying the name of the branch:

```
git worktree add ../another-directory existing-branch
```

In this case `existing-branch` already exists in the repository. You can actually specify anything _commit-ish_ (as it's called in the documentation) as the last argument, be that a branch name or a commit hash for example.

If you want to remove the worktree you call... eh, remove:

```
git worktree remove ../another-directory
```

All your commited changes will be present in the main repository, as if you had done the work there.

---

For me, this has been a life saver in hassle when checking out branches for code review, or for doing a quick fix on a branch that's been reviewed. Hope it helps you too.

Happy coding!
