---
title: GitOps - Git For Ops People
summary: Start using Git, it's guaranteed to save your bacon!

excerpt_separator: <!--more-->

tags:
    - Git
    - GitOps
    - PowerShell
---

I want to show you that even as someone who works solely in Ops or Support, embracing Git as a tool will save you time, effort and it doesn't have to be as complicated as it first seems.

* TOC
{:toc}

## If you answer Yes to any of the following then this blog post is for you:
{: .no_toc}
> Git seems daunting, why do I need to learn new software when what I currently do works?

> I'm not a developer so why do I need developer tools?

> I'm the only one that uses these scripts, so is there any point?

> My scripts are only 20 lines long, why do I need source/version control?

<!--more-->


## The Journey to Becoming a Git User and Advocate

I am from an Ops background, I started at 1st line helpdesk, 2nd line support, help desk team lead then moved into a SysAdmin role where I started to learn PowerShell and write some scripts to help me administer Active Directory.

It was apparent that a lot my scripting process was trial and error, mashing the keyboard and running the scripts to see what happened. I would google some keywords of what I was trying to achieve and tack `PowerShell` on to the end and click through the search results until I found something that resembled what I was trying to achieve. I became quickly aware of Github and [TechNet](https://gallery.technet.microsoft.com) gallery. This whole other world of open source and development that was miles apart from my little script to check if an Active Directory user account was enabled.

I had to learn about loops, pipelines, functions and usually error handling. My scripts evolved from one-liners, to multiple one-liners in a script, to full-blown scripts that did lots of things with varying degrees of accuracy. Suddenly I wasn't a million miles away from the stuff on Technet. However this still involved copying and pasting from blogs, Stack Overflow and various other google search results. Inherently this also meant there was a lot of of saving and testing followed by `Ctrl + Z` to undo changes, saving and testing again.

## Saving Scripts as a New Version After Every Change - Eureka-ka-ka-ka!!

When I was happy a script **mostly** worked, I would save it incrementing the version number in the file name `File > Save As > MyAdScript-v0.3.ps1`

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

What if I told you that there was a way to do all of the above, without all of the overhead?

## Git - The actual Eureka moment
The next post in this series will show you how to get started with Git
