---
title: PowerShell & Azure Functions - Part 1
summary: Getting Started

excerpt_separator: <!--more-->

tags:
    - PowerShell
    - Azure Functions
    - Serverless
---


## What are Azure Functions
{: .no_toc}
Simply put they are a serverless solution in Azure which allow you to run code without having to worry about where it is running or maintaining the underlying infrastructure.

You can read a whole lot more about Azure Functions in the [documentation](https://docs.microsoft.com/en-gb/azure/azure-functions/functions-overview) and it can explain a lot better than I can.

* TOC
{:toc}

## Writing Your First Function
I will go over how to write an Azure Function using Visual Studio Code as that is by the best development experience

### Pre-Requisites
You can find the pre-requisites and installation instructions in the [Microsoft Docs](https://docs.microsoft.com/en-gb/azure/azure-functions/functions-run-local#install-the-azure-functions-core-tools).

- PowerShell 6+
- Visual Studio Code (obviously)
- Node.js which includes npm
- Azure Functions Core Tools
- .NET Core SDK - _I have this installed but you can avoid that with the extension bundles_ 

You can also use dev containers to run and debug your functions but there is no official docker image for PowerShell Azure functions **yet**

### Create A Local Function Project
- Create a folder where your Function will be contained

    ```powershell
    New-Item -ItemType Directory -Name MyFirstFunction

    cd MyFirstFunction
    ```

- Now that you're in your project folder initialise your function. Select PowerShell with your cursor

    ```powershell
    PS > func init MyFirstFunction

    Select a worker runtime:
    dotnet
    node
    python 
    powershell
    ```

- 

## Testing & Debugging Your Function

## Deploying Your Function