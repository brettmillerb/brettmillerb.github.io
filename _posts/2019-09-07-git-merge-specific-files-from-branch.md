---
title: Git - Reverting git branch changes the easy way
summary: merging specific files/folders from another branch - easier than expected

excerpt_separator: <!--more-->

tags:
    - Git
    - PowerShell
    - Source Control
    - GitOps
---

I was working on a proof of concept with colleagues which uses Azure Functions, AWS Lambda and utilises various REST API's. One colleague had started work on the repo and created the function app, the terraform to deploy it via Azure Pipelines, another colleague had created the lambda and the cloudformation. I cloned the repo, made my own branch and started to add additional Azure Functions, CI ran and deployed the contents of the repository.

<!--more-->

### Oops I broke it

The Azure Functions [deployment Zip pipeline task documentation](https://docs.microsoft.com/en-gb/azure/azure-functions/deployment-zip-push#deployment-zip-file-requirements) told me that I needed to have a specific folder structure so that I could deploy multiple functions in one Function App.

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

So I structured my repository as per the documentation and moved my colleagues pre-existing Az Function into the same folder.

However it turns out that I need each of my functions in a separate function app so they need to be in separate folders.

So I need the pre-existing folder structure from master but the code from my branch and I had already modified my colleagues function folder. 🤔

### Solution(s)

#### Option 1
My initial thought was some copy and paste hacking with the files/folders in explorer. Copy the files to a temporary location, create an entirely new branch from Master and paste the files back in.

This felt horrible and since git got me into this mess it will git me out of it.

#### Option 2
Rename my current `FunctionApp` folder then `git merge master` which will pull in the files I previously removed from master and I can then move files around as required.

This is risky and could overwrite changes in other files/folders.

#### Option 3
This turned out to be the easiest solution and worked a treat.

`git checkout master folderIWant/`

> Checking out an old file/file from another branch does not move the HEAD pointer. It remains on the same branch and same commit, avoiding a 'detached head' state.

`git status` will show you the folder from master is now in your branch. Tidy up the changes you don't want and that's it! 🎉