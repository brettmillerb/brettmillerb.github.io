---
title: GitOps - Git For Ops People
summary: Start using Git, it's guaranteed to save your bacon!

excerpt_separator: <!--more-->

tags:
    - Git
    - GitOps
    - PowerShell
---

* TOC
{:toc}

I want to show you that even as someone who works solely in Ops or Support, embracing Git as a tool will save you time, effort and it doesn't have to be as complicated as it first seems.

## If you answer yes to any of the following then this blog post is for you:
> Git seems daunting, why do I need to learn new software when what I currently do works?

> I'm not a developer so why do I need developer tools?

> I'm the only one that uses these scripts, so is there any point?

> My scripts are only 20 lines long, why do I need source/version control?

## The Journey to Becoming a Git User and Advocate

I am from an Ops background, I started at 1st line helpdesk, 2nd line support, help desk team lead then moved into a SysAdmin role where I started to learn PowerShell and write some scripts to help me administer Active Directory.

It was apparent that a lot my scripting process was trial and error, mashing the keyboard and running the scripts to see what happened. I would google some keywords of what I was trying to achieve and tack `PowerShell` on to the end and click through the search results until I found something that resembled what I was trying to achieve. I became quickly aware of Github and [TechNet](https://gallery.technet.microsoft.com) gallery. This whole other world of open source and development that was miles apart from my little script to check if an Active Directory user account was enabled.

I had to learn about loops, pipelines, functions and usually error handling. My scripts evolved from one-liners, to multiple one-liners in a script, to full-blown scripts that did lots of things with varying degrees of accuracy. Suddenly I wasn't a million miles away from the stuff on Technet. However this still involved copying and pasting from blogs, Stack Overflow and various other google search results. Inherently this also meant there was a lot of of saving and testing followed by `Ctrl + Z` to undo changes, saving and testing again.

## Saving Scripts as a New Version After Every Change - Eureka-ka-ka-ka!!

When I was happy a script **mostly** worked, I would save it incrementing the version number in the file name `File > Save As > MyAdScript-v0.3.ps1`

<!--more-->

Then my folder structure looked something similar to this:

```powershell
Directory: C:\Scripts\AdScripts

Name
----
MyAdScript-v0.2.ps1 
MyAdScript-v0.3.ps1
MyAdScript-v0.4.ps1
MyAdScript-v1.0.ps1
MyAdScript-v1.1.ps1
```
What would happen if you needed to remember what you had changed?! Add a changelog of course.

```powershell
##############################
# AD Script For Doing Stuff
# Verifies that x,y,z is correct
# V0.2 is the first acceptable version
# V0.3 Added some functionality - Brett Miller
# V0.4 Didn't take into account human stupidity - Fixed
##############################

$restOfScriptHere
```
Et Voilà we have a place to store our scripts, we also have a change log so we know what has been changed and by who, we can store these scripts on a management server and it'll be great!

## What Happens If......
* Someone changes something but doesn't update the changelog/version number?
> It's fine, we'll send an email reminder out so it doesn't happen again

* Someone makes changes to the script which inadvertently breaks something and they don't save it as a new version?
> It's fine, we'll back them up somewhere daily/hourly so now we have two places where scripts are stored.
> We can always do a filesystem restore if things get lost in the process.

* People get access to them who shouldn't need access.
> It's fine, we'll set up a strict ACL on that folder and only give us access.
> We all know what we're doing right?

* Another team wants you to write them a script that they can run
> Sure, no problem. We'll store the master script in our folder then give it to them to store somewhere else, which they will then have full rights over to change and modify and expect us to support it.

#### What if I told you that there was a way to do all of the above, without all of the overhead?



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