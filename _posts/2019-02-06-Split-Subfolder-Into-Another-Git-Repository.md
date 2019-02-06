---
title: Split a SubFolder into a New Repository
summary: Copy a project subfolder to a new git repository

excerpt_separator: <!--more-->

tags:
    - Git
    - GitOps
    - Powershell
    - Source Control
---

So I recently encountered an issue where I needed to extract a number of subfolders out of a Git repository so I could move them into their own repository/project.

I am no git expert and easily and quite frequently end up in a git mess so I took to the [Powershell Slack](https://j.mp/psslack) to ask the more knowledgable folk there to see if anyone had already done it. I figured it was possible as most things are possible with git but easily achievable is another thing

<!--more-->

### git filter-branch

Chris Gardner ([@HalbaradKenafin](https://twitter.com/HalbaradKenafin)) suggested using `git filter-branch` so off I went looking for some examples online whilst trying to avoid the official git documentation which is [here](https://git-scm.com/docs/git-filter-branch) for the masochists.

Chris linked me to a [github article](https://help.github.com/articles/splitting-a-subfolder-out-into-a-new-repository/) which shows how you can do this and coming from github themselves I was filled with confidence that it was easier than I initially anticipated.

I seemed to get strange results when using Powershell Integrated Console in Visual Studio Code so I tried in Powershell Core console with similar horrible results. Variations of nothing happening at all, to the folder ended up totally empty or errors like below.

```bash
Cannot create a new backup.
A previous backup already exists in refs/original/
Force overwriting the backup with -f
```

Realising this was an exercise in futility I took to Bing _(I know)_ to find another solution.

### git subtree

After a little while longer scouring for a solution I stumbled upon [this blog post](https://w3guy.com/split-subfolder-repository-git-subtree/) which shows you how to achieve this with git subtree.

So I set about trying to do this with my module.

### Create a new clone of the repository
I am well aware of how easy it is to mess up with git so to test this I created a new clone of the remote repository in a scratch area on my laptop

```bash
$ cd c:\temp
$ git clone https://github.com/{username}/{repository}
$ cd {repository}
```

You can then run git subtree
```bash
$ git subtree split --prefix {folder}/{subfolder}

# It will enumerate the repository with a visible counter
184/533 (183)

# then it will return a SHA1 hash
85dfee8248b5b68b8fc2a45d3bcf55841ded6eaa
```

### Create a new branch at this location

```bash
$ git checkout -b {branchname} 85dfee8248b5b68b8fc2a45d3bcf55841ded6eaa
```

This branch is now available for you to work on. You can verify it worked by running `ls` and the directory should only contain the contents of the subfolder.

Git history should persist so running `git log -1` should show you the last commit message.

```bash
$ git log -1
commit 85dfee8248b5b68b8fc2a45d3bcf55841ded6eaa (HEAD -> {branchname})
Author: Brett Miller <brett@millerb.co.uk>
Date:   Sun Feb 3 10:48:52 2019 +0000

    Updated Manifest Tags (#10)

    * Updated Readme with new functionality
```

### Push this folder to another repository

#### List remote repositories
```bash
$ git remote -v
origin  https://github.com/brettmillerb/{repository}.git (fetch)
origin  https://github.com/brettmillerb/{repository}.git (push)
```
#### Add a new remote repository
```bash
$ git remote add neworigin https://github.com/brettmillerb/{newrepository}.git
```
#### List remote repositories
```bash
$ git remote -v
origin  https://github.com/brettmillerb/{repository}.git (fetch)
origin  https://github.com/brettmillerb/{repository}.git (push)
neworigin https://github.com/brettmillerb/{newrepository}.git (fetch)
neworigin https://github.com/brettmillerb/{newrepository}.git (push)
```
#### Push to the new remote repository from your branch to master
```bash
$ git push -u neworigin {branchname}:master
```

You can then go to your usual git location and clone the repository from master to ensure you have a clean local branch for further development on this repository.