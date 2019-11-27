---
title: Developing PowerShell Azure Functions Locally in a Container
summary: Managing Dependencies for Azure Functions Effectively

excerpt_separator: <!--more-->

tags:
    - Azure Functions
    - PowerShell
    - Docker
    - Remote Development Extension
    - Serverless
---

I have recently just switched work machines from a Windows laptop to a Macbook. I've not had much exposure to anything other than Windows but I live on the command line most of the time and `'mac' -eq 'linux'` anyway right?

## Keeping My Machine Tidy

Part of this switch I made it my mission to try and keep my machine as tidy as possible and with that I didn't want to immediately go and install a load of stuff that I was then going to have to manage and update.....especially when i only needed some of it for specific projects.

I have been spending a considerable amount of my down time watching people do live coding on twitch and in particular [Michael Jolley (Twitter)](https://twitter.com/michaeljolley) and I was particularly intrigued by him showing how he develops inside of docker containers so I decided to give it a try.

<!--more-->

## Pre-Requisites

- Visual Studio Code
- [Docker Desktop](https://www.docker.com/products/docker-desktop)
- [Remote Development Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)

The installation requirements for Azure Functions are on [docs.microsoft.com](https://docs.microsoft.com/en-gb/azure/azure-functions/functions-run-local#install-the-azure-functions-core-tools).

On both Mac and Windows this requires the Azure Functions Extension Bundle or the .NET SDK and on Windows you'll hae to install npm and node.js so not having to install anything locally is good.

## Setting up your Workspace

Create a folder on your machine that you want to contain your project.

```powershell
PS > New-Item -ItemType Directory -Name MyFirstFunction

    Directory: /Users/brett.miller/git/gitpersonal

UnixMode   User           Group       LastWriteTime   Size Name
--------   ----           -----       -------------   ---- ----
drwxr-xr-x brett.miller            27/11/2019 16:03     64 MyFirstFunction
```

Open that folder in VSCode

```powershell
PS > cd MyFirstFunction

PS > code .
```

Once you're inside Visual Studio Code you can open your folder in a container by using the command pallette.

`F1` > `Remote-Containers: Open Folder In Container`

![Remote Container - Open In Folder](https://user-images.githubusercontent.com/24279339/69739952-74914000-1130-11ea-87d0-7078eea0d46c.png)

Select the folder you want to open. You will then be presented with the list of available containers from within the extension.

What you will notice however is that there are options for Azure Functions with C#, Java, Node & Python but there is no container for PowerShell Azure Functions. WTF?!

Well I have fixed that by opening a Pull Request and adding a container definition which has all of the dependencies required. This is pending release at the time of writing.

To get access to these prior to release you can grab what you need directly from the [Github Repo](https://github.com/microsoft/vscode-dev-containers/tree/master/containers/azure-functions-pwsh-6/.devcontainer).

Open your folder in VSCode and create another folder called `.devcontainer` then create two files, `devcontainer.json` & `Dockerfile`.

Copy the contents of the two files from github to their corresponding files on your machine.

![Local Folder with devcontainer setup](https://user-images.githubusercontent.com/24279339/69741312-c044e900-1132-11ea-8aaf-4d1dfd91fc88.png)

Now you can reopen the folder in your container with `Remote-Containers: Reopen in Container`.

This may take a while as docker now needs to download the necessary image and work through the docker file installing dependencies so that all tools are available. Subsequent runs should not take as long as it will reuse the image it creates.

![Reopen in Container](https://user-images.githubusercontent.com/24279339/69741529-1e71cc00-1133-11ea-836f-7d2660e88654.png)

Once it has finished building the docker file it will open a terminal defaulting to PowerShell.

You can confirm this with `$PSVersionTable`

```powershell
PS /> $PSVersionTable

Name                           Value
----                           -----
PSVersion                      6.2.3
PSEdition                      Core
GitCommitId                    6.2.3
OS                             Linux 4.9.184-linuxkit #1 SMP Tue Jul 2 22:58:16 UTC 2019
Platform                       Unix
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
WSManStackVersion              3.0
```

Now that you have your folder opened in a container with all of the necessary tools you can start initialising your Function Project.

To see how to do that you can follow this post I created [PowerShell & Azure Functions - Part 1](https://millerb.co.uk/2019/11/27/Getting-Started-Pwsh-Az-Functions-Part-1.html)