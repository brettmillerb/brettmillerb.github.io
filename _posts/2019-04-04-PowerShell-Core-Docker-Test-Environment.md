---
title: PowerShell Core & Docker Test Environment
summary: Easier Than You First Think But More Difficult Than You're Led To Believe

excerpt_separator: <!--more-->

tags:
    - PowerShell
    - Docker
    - PowershellCore
---

So I recently had a requirement to run some scripts in an isolated environment where I didn't want to test on my machine but throwing up an entire pipeline was overkill and frankly not needed for the basic testing that I wanted to do.

I had used docker before but not for a long time and I didn't really grasp it properly the last time I tried and reality did not meet expectations. Now that I predominently live in the console in one way or another I figured why not!

<!--more-->

### What You Need to get you started
- Docker Desktop for Windows
- That's it

This is a pretty thorough guide on how to get started [Docker for Windows](https://docs.docker.com/docker-for-windows/link)

You can install that directly from Docker:

[Docker Desktop for Windows - Installer here](https://docs.docker.com/docker-for-windows/install/link)

or

If you have moved to Chocolatey for your package management needs then:
```powershell 
# Find the package in the choco repository
choco info docker-desktop

# This installs Docker-Cli, Docker Compose & kubectl command line
choco install docker-desktop 
```

Once it's installed you can open `pwsh` and check everything is there

```powershell
PS C:\> docker --version
Docker version 18.09.2, build 6247962
```

I abandoned the guide I linked above because, I mean, why would you start with something simple like Hello-World?

I have known for a while that Microsoft have been publishing their own docker images for PowerShell in various guises so I was going to start with one of those.

### Building yourself a container to play with
So to get started I bookmarked this [5 part docker series](https://dev.to/softchris/5-part-docker-series-beginner-to-master-3m1b) by [Christoffer Noring](https://twitter.com/chris_noringlink) to read then didn't read it and carried on anyway. 

![Clever Guy Meme](/assets/img/clever_guy_meme.png)

I performed a search for the powershell docker images on my favourite search engine and landed at [docker hub](https://hub.docker.com/_/microsoft-powershell) which not only did it give me the images it also told me how to download them

```powershell
docker pull mcr.microsoft.com/powershell
```

This won't take too long and because docker for windows generally starts in linux container mode you will likely end up with an Ubuntu 18.04 image with PowerShell Core. The images have just been updated to PowerShell Core 6.2 at the time of writing.

But what if you want a Windows image? You will need to right click the docker icon in your system tray and click `Switch to Windows Containers` this will restart docker.

You can then download the image for Windows Server Core. It can take a little while as it's around 4.7Gb if my memory serves me right.

```powershell
docker pull mcr.microsoft.com/windows/servercore
```

Once it has downloaded you can launch the docker container interactively

```powershell
docker run -it mcr.microsoft.com/windows/servercore
```

```pwsh
C:\> $psversiontable
'$psversiontable' is not recognized as an internal or external command,
operable program or batch file.
```

### Say what now?!

Maybe it just doesn't startup at first.

```powershell
C:\> pwsh.exe
'pwsh.exe' is not recognized as an internal or external command,
operable program or batch file.
```

### Say what now?!
D'oh!!! Obviously this is just a basic Windows Server Core image.

```powershell
C:\> powershell

Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\> $psversiontable

Name                           Value
----                           -----
PSVersion                      5.1.14393.2636
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.14393.2636
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

This is fine if you want an image which needs Windows PowerShell but I want to make sure I am testing my stuff on PowerShell Core and installing to Windows is more difficult than say `sudo apt-get install -y powershell` on Ubuntu.

### Off to find PowerShell Core
So I set about trying to find the best way to install PowerShell core and ended up back at [PowerShell Docker Hub](https://hub.docker.com/_/microsoft-powershell).

Scrolling down the list you can see a list of images so looking at the tags: `6.2.0-windowsservercore-1809_kb4480116_amd64` looks like as good a shout as any.

Click on the dockerfile link it takes you to the [github repo](https://github.com/PowerShell/PowerShell-Docker/blob/master/release/stable/windowsservercore/docker/Dockerfile) which shows you the dockerfile. If you haven't worked with docker files before (I hadn't) the syntax and format is simple enough to follow.

### How do you build an image using a docker file 🤷
Some quick search engine wizardry later I ended up on the [official docs](https://docs.docker.com/engine/reference/commandline/build/) which call out
```powershell
docker build [OPTIONS] PATH | URL | -
```

You can also use the command line

```powershell
PS C:\> docker build --help

Usage:	docker build [OPTIONS] PATH | URL | -
```

### Building your image
Now that we have pulled an image from the docker hub, we have a docker file from Microsoft's official repository and the command line to build our image we just need to stick it all together.

Copy the dockerfile to a file and save it locally on your machine. You can build it from the URL too but let's not get carried away with ourselves.

```powershell
# Note: This does not have a filetype and is just called Dockerfile
PS C:\> New-Item -Path c:\docker -ItemType Directory -Name servercore

# . being the current directory where the dockerfile is located
PS C:\> Set-Location c:\docker\servercore

# -t is tags so you can tag your image with a friendly name. Docker will give you a random name otherwise.
PS C:\> docker build . -t brett/pwsh
```

![docker build](/assets/img/docker_build.png)

Once this has built you will be able to see the images available to you.

```powershell
PS C:\> docker image ls

REPOSITORY                             TAG         IMAGE ID       CREATED        SIZE
brett/pwsh                             latest      8dc8592b095c   3 days ago     12.1GB
mcr.microsoft.com/windows/servercore   latest      ea9f7aa13d03   2 months ago   11GB
```

Whenever you need to access this as a container you can run an interactive session

```powershell
PS C:\> docker run -it brett/pwsh

PowerShell 6.2.0
Copyright (c) Microsoft Corporation. All rights reserved.

https://aka.ms/pscore6-docs
Type 'help' to get help.

PS C:\> $PSVersionTable

Name                           Value
----                           -----
PSVersion                      6.2.0
PSEdition                      Core
GitCommitId                    6.2.0
OS                             Microsoft Windows 10.0.14393
Platform                       Win32NT
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0?}
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
WSManStackVersion              3.0
```

### Taking it to the next level
This is great except now I have the same issue with PowerShell Core that I had with Windows PowerShell. How do I get stuff installed?

My logical answer was chocolatey.

If you go to the chocolatey website you can find the command line to install Chocolatey with the usual caveats of **Don't run scripts off of the internet without checking them first**.
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Invoke-Expression ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

What If I can add this to my dockerfile so that I don't have to do this every time?!

Inside the Microsoft dockerfile I snagged from Github you can see the section which runs pwsh to configure the module cache
```docker
RUN pwsh `
        -NoLogo `
        -NoProfile `
        -Command " `
          $stopTime = (get-date).AddMinutes(15); `
          $ErrorActionPreference = 'Stop' ; `
          $ProgressPreference = 'SilentlyContinue' ; `
          while(!(Test-Path -Path $env:PSModuleAnalysisCachePath)) {  `
            Write-Host "'Waiting for $env:PSModuleAnalysisCachePath'" ; `
            if((get-date) -gt $stopTime) { throw 'timout expired'} `
            Start-Sleep -Seconds 6 ; `
          }"
```

All I need to do is steal this code and add a bit to install chocolatey then refreshenv.

```docker
RUN pwsh `
        -NoLogo `
        -NoProfile `
        -Command " `
          Set-ExecutionPolicy Bypass -Scope Process -Force; `
          iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))"
  
RUN pwsh `
        -NoLogo `
        -NoProfile `
        -Command " `
          choco install git -y; `
          refreshenv"
```

Then run the build task again.

```powershell
PS C:\> docker build . -t brett/pwsh
```

Now when ever you want a quick docker container to test something in a fresh environment you can spin up a windows container which has Chocolatey and Git installed and you're good to go in a matter of minutes.

The full docker file is here: [Docker File](https://gist.github.com/brettmillerb/fa4c18be41acc69cc19f97ad9899d2c3)