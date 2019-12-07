---
title: Azure CLI vs Azure PowerShell - Why not both?
summary: Utilising both Azure CLI & Az PowerShell effectively

excerpt_separator: <!--more-->

tags:
    - Azure
    - PowerShell
    - Azure CLI
---

If like me, you have come from PowerShell background where you started managing on-premises workloads over to managing cloud magic, irrespective of which flavour cloud you're working with you will want to use the tools that are familiar to you. So naturally I tend to lean towards PowerShell.

The Azure PowerShell module is now on it's 3rd iteration. You had the original `Azure` module followed by `AzureRM` and now you have the cross platform `Az` module.

You also have the Azure CLI which I am using more and more but if you're used to powershell it is not very familiar syntax wise, it has very limited tab completion and the more you do with it you have to start looking at [JMESPath](http://jmespath.org/) query language.

<!--more-->

I think when, we as engineers, see a new tool and we have to assess how it fits with our current knowledge and workflow. At first we will naturally look to how we can do what we want with the knowledge we have as learning an entirely new CLI or tool can take time and be frustrating but if the tools exist that already do what I need then why not leverage those instead?

Personally I find Az CLI really performant and the built-in help is really concise. It shows mandatory parameters as well as examples and it covers a good 98% of what I need when I am writing something in the console.

## Objects vs JSON

Naturally after working with PowerShell for quite some time you become accustomed to it's Object output, however when you first start playing with Azure CLI you suddenly start getting raw JSON, which if you're returning more than one resource is going to fill your screen and be difficult to read.

Azure CLI does however offer different output formats ([Output Docs](https://docs.microsoft.com/en-us/cli/azure/format-output-azure-cli?view=azure-cli-latest))

output | Description
:----: | :---------:
json   | JSON string. This setting is the default.
jsonc  | Colorized JSON.
yaml   | YAML, a machine-readable alternative to JSON.
table  | ASCII table with keys as column headings.
tsv    | Tab-separated values, with no keys

When you start looking at wanting anything other than the default output properties when using `table` or `tsv` output that you have to starting googling the JMESPath syntax or digging through examples.

## Az CLI 'vs' PowerShell 🥊

Lets have a look at the different ways to retrieve some results using Azure Cli and PowerShell.

### Az CLI Query
```
az functionapp list --query "[].{resource:resourceGroup, name:name, defaultHostName:defaultHostName}" -o table

Resource    Name          DefaultHostName
----------  ---------     ---------------------------
poshbotio   poshbotio     poshbotio.azurewebsites.net
Myfunction  MyFunction    myfunction.azurewebsites.net
Functest    HttpFunction  httpfunction.azurewebsites.net
```

✅ Available Cross Platform including in Cloud Shell.

❌ Unfamiliar syntax, not very nice to type.

## Native PowerShell
```
Get-AzFunctionApp | Select-Object @{n='resource';e={$_.resourceGroup}}, name, defaultHostName

Resource    Name          DefaultHostName
----------  ---------     ---------------------------
poshbotio   poshbotio     poshbotio.azurewebsites.net
Myfunction  MyFunction    myfunction.azurewebsites.net
Functest    HttpFunction  httpfunction.azurewebsites.net
```

✅ Available Cross Platform including in Cloud Shell.

✅ Familiar PowerShell syntax, Calculated Properties aren't the nicest but familiar.

❌ FunctionApp cmdlets are only available in the preview Az.Functions module.

## Hybrid Az Cli & PowerShell
```
az functionapp list | ConvertFrom-Json | select-Object @{n='resource';e={$_.resourceGroup}}, name, defaultHostName

Resource    Name          DefaultHostName
----------  ---------     ---------------------------
poshbotio   poshbotio     poshbotio.azurewebsites.net
Myfunction  MyFunction    myfunction.azurewebsites.net
Functest    HttpFunction  httpfunction.azurewebsites.net
```
✅ Available Cross Platform including in Cloud Shell.

✅ No need to install extra PowerShell modules.

✅ Basic Az CLI knowledge required so quicker discoverability.

✅ Familiar syntax for consuming and manipulating the output with PowerShell.

## Deciding which to use
I don't really have a preference between Azure CLI and PowerShell but if you look at the output above you get exactly the same result using all three methods.

What I end up using in reality is Az CLI to retrieve the results then `ConvertFrom-Json` to parse the results into a powershell Object. This is familiar to me without having to learn the nuances of JMESPath query filters but also gives me the flexibility of being able to pipeline that object into other PowerShell command.

### Az CLI Built-In Help
I always brag about how amazing the PowerShell help system is to people learning PowerShell but I also think that the team who built the Azure CLI have done an amazing job on the builtin help

Typing `az -h` will show you the extensions available to be used.
```
❯❯ az -h

Group
    az

Subgroups:
    account               : Manage Azure subscription information.
    acr                   : Manage private registries with Azure Container Registries.
    ad                    : Manage Azure Active Directory Graph entities needed for Role Based
                            Access Control.
    advisor               : Manage Azure Advisor.
    aks                   : Manage Azure Kubernetes Services.
    ams         [Preview] : Manage Azure Media Services resources.
    apim        [Preview] : Manage Azure API Management services.
    appconfig   [Preview] : Manage App Configurations.
    appservice            : Manage App Service plans.
    backup      [Preview] : Manage Azure Backups.
:
```

To find out more about the usage of these extensions you can use `az {subGroup} -h`

```
az functionapp -h
```

Drilling down further

```
az functionpp list -h
```

It lists examples with each use of the help switch and lists the available sub-groups, commands and examples

## Az Find for examples and discoverability

I didn't know this existed until recently but it's an awesome tool for discoverability at the command line when using Az CLI.

Here is what the help says about the command

```
Command
    az find : I'm an AI robot, my advice is based on our Azure documentation as well as the usage
    patterns of Azure CLI and Azure ARM users. Using me improves Azure products and documentation.
```

And here is what it says about the examples we have been using. This can be really helpful when you're getting used to the Azure CLI but also if you're just unsure about how a specific command should work

```
❯❯ az find "az functionapp list"
Finding examples...

Here are the most common ways to use [az functionapp list]:

List default host name and state for all function apps.
az functionapp list --query ".{hostName: defaultHostName, state: state}"

List all running function apps.
az functionapp list --query ""

List available locations for running function apps.
az functionapp list-consumption-locations
```

### Summary
Find what works for you. The cloud is a big magical place where people are doing amazing things, with this comes a variety of new tools for managing all aspects of the cloud.

I've found a hybrid pattern that works for me when I am using the console for discoverability or information gathering. It gives me the speed of the Azure CLI with the familiar syntax of PowerShell so I can be productive without having to learn an entire new command line tool.

Try not to build something just for the sake of building something. The PowerShell community is definitely guilty of building a tool just because it suits our needs. Take a look at what is already out there and maybe think about contributing to that instead, that way an entire ecosystem benefits from your input/angle.

The more that I use the Azure CLI, the more I am inclined to just dive in and use it, it is available in both bash and PowerShell on my Mac & in cloud shell in browser so I can just fire it up without having to think about it.