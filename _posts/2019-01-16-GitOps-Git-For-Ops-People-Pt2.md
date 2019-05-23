---
title: GitOps - The Actual Eureka Moment
summary: Getting started with git and the benefits
category:
    - GitOps - Git For Ops People

excerpt_separator: <!--more-->

tags:
    - tag1
    - tag2
    - tag3
---

## Git - The actual Eureka moment
>Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.
>
>Git is easy to learn and has a tiny footprint with lightning fast performance. It outclasses SCM tools like Subversion, CVS, Perforce, and ClearCase with features like cheap local branching, convenient staging areas, and multiple workflows.

Yeah, the above statement made me raise my eyebrows, close my browser tab and crawl back under my rock too. Git is scary, it's what software developers use and you need to learn things to use it.

You may think that Git has to be used with Github or GitLab, this is usually **how** it is used but doesn't have to be. I used git for a while with just a few basic commands. I came up with a process that was easy for me to follow and meant that I still had my code in version control without having multiple files of different versions in the same folder.

[Thomas Rayner](https://twitter.com/MrThomasRayner) covers the basic commands to get you started in his recent blog post [The Six Commands You NEED To Know To Work With Git](https://thomasrayner.ca/first-git-commands/)

I'm going to show you how to use Git as part of yor everyday script writing process without over complicating it. Hopefully this will then be your gateway to reaping further benefits of Git.

## Making Git work for you

Normally when writing a script I would fire open ISE or VSCode then start mashing away until i had something that vaguely did what I wanted it to do. I would recommend that even if you're just hacking something together that you use Git. You never know where that hacked together script is going to end up.

Lets say you're working on a script to do something with user accounts in Azure Active Directory.

You need to create a new folder where your script will live.

```powershell
New-Item -Name AdScripts -ItemType Directory

    Directory: C:\Scripts


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       18/11/2018     14:12                AdScripts
```

Normally this is where you'd start hacking away and getting something written, I propose that you start by making this directory a git repository.

```powershell
[14:18:52] Scripts\AdScripts> git init
Initialized empty Git repository in C:/Scripts/AdScripts/.git/
```
Running `git init` is the only thing you need to do which defines your directory as a git repository.
>Executing git init creates a .git subdirectory in the current working directory, which contains all of the necessary Git metadata for the new repository.

You can confirm that this has worked by running `git status`

```powershell
[14:19:42] Scripts\AdScripts> git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

Now to create your script so you can start writing your script.

```powershell
[14:20:21] Scripts\AdScripts> New-Item -Name MyAzureAdScript.ps1 -ItemType File

    Directory: C:\Scripts\AdScripts


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       18/11/2018     14:24              0 MyAzureAdScript.ps1
```

Open your file in your editor of choice.

```powershell
code .\MyAzureAdScript.ps1
```

Now that you have added your script to the directory if you run `git status` again it will show you changes which have or haven't been staged and which files are being tracked by git.

```powershell
[14:36:31] Scripts\AdScripts> git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	MyAzureAdScript.ps1

nothing added to commit but untracked files present (use "git add" to track)

```

You want git to track your script so you need to run `git add .\MyAzureAdScript.ps1`. You won't get any output from this command and if you're only working on one file you can use `git add .` to add all files in the directory.

If you run `git status` again you will see that your file has been staged bt not yet committed. Staging files only to commit them seems unnecessary when only working on one script file but as your scripts/projects become more complicated the advantages of this becomes more apparent.

```powershell
[14:37:23] Scripts\AdScripts> git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   MyAzureAdScript.ps1
```

To commit your files use `git commit -m "Meaningful but brief commit message"` this is a good habit to get into and you will thank your past self for actually doing this.

```powershell
[14:38:31] Scripts\AdScripts> git commit -m "Adding Initial script file"
[master (root-commit) 978e8b4] Adding Initial script file
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 MyAzureAdScript.ps1
```