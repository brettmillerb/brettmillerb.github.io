---
title: Azure CLI vs Azure PowerShell - Why not both?
summary: Becoming a hybrid Azure CLI & Az PowerShell ninja

excerpt_separator: <!--more-->

tags:
    - Azure
    - PowerShell
    - Azure CLI
---

I see quite a bit of conversation about whether people prefer Azure CLI or PowerShell for managing their Azure resources and if like me, you have come from managing on-premises workloads before moving over to managing cloud magic, you will likely want to use the tools that are familiar to you. So naturally I tend to lean towards PowerShell but I have been using the CLI more frequently so I wanted to highlight that it doesn't have to be one or the other.

The Azure PowerShell module is now on it's 3rd iteration. You had the original `Azure` module followed by `AzureRM` and now you have the cross platform `Az` module.

The Azure CLI, if you're used to powershell, is not very familiar syntax wise, it has very limited tab completion and the more in depth you go you have to start referencing the docs for things like [JMESPath](http://jmespath.org/) query language.

<!--more-->

As engineers I think it is natural to look at how we can manage cloud workloads with with the skills and tools that we already have but what if we're missing out and what if you have no choice but to adopt something else?

Learning a new CLI can take time and when frustration sets in it's easy to think about going back to your PowerShell comfort blanket.

**What if these tools are easier to use?**

**What if they are better?** 😱

Personally I find Az CLI really performant and the built-in help is really concise. It shows mandatory parameters as well as examples and it covers a good 98% of what I need when I am working at the command line in the console.

## Azure CLI 'vs' PowerShell  🥊

Naturally after working with PowerShell for quite some time you become accustomed to it's Object output, however when you first start playing with Azure CLI you suddenly start getting raw JSON, which if you're returning more than one resource is going to fill your screen and be difficult to read.

Azure CLI does however offer different output formats ([Output Docs](https://docs.microsoft.com/en-us/cli/azure/format-output-azure-cli?view=azure-cli-latest))

output | Description
:----: | :---------:
json   | JSON string. This setting is the default.
jsonc  | Colorized JSON.
yaml   | YAML, a machine-readable alternative to JSON.
table  | ASCII table with keys as column headings.
tsv    | Tab-separated values, with no keys

The downside is when you start looking at wanting anything other than the default properties when using `table` or `tsv` output then you have to starting googling the JMESPath syntax or digging through examples to get what you want. This is where the frustation sets in as you already know how to do this with PowerShell.

Lets have a look at the different ways to retrieve some results using Azure Cli and PowerShell.

### Az CLI Query
Let's list all of the FunctionApp's available in the current account. We want to change the resourceGroup property to resource because I needed a contrived example and we also want the name and defaultHostName properties.

```
az functionapp list --query "[].{resource:resourceGroup, name:name, defaultHostName:defaultHostName}" -o table

Resource    Name          DefaultHostName
----------  ---------     ---------------------------
poshbotio   poshbotio     poshbotio.azurewebsites.net
Myfunction  MyFunction    myfunction.azurewebsites.net
Functest    HttpFunction  httpfunction.azurewebsites.net
```

✅ Azure CLI is available X-Plat including in Cloud Shell.

❌ JMESPAth is unfamiliar syntax, all of the braces are not very nice to type.

## Native PowerShell
Using the PowerShell cmdlets to retrieve exactly the same. We have to use a calculated property to change the resource property. Get all of the results and select the properties you want with `Select-Object`

```
Get-AzFunctionApp | Select-Object @{n='resource';e={$_.resourceGroup}}, name, defaultHostName

Resource    Name          DefaultHostName
----------  ---------     ---------------------------
poshbotio   poshbotio     poshbotio.azurewebsites.net
Myfunction  MyFunction    myfunction.azurewebsites.net
Functest    HttpFunction  httpfunction.azurewebsites.net
```

✅ Available X-Plat including in Cloud Shell.

✅ Familiar PowerShell syntax, Calculated Properties are familiar but still a bit meh.

❌ FunctionApp cmdlets are only available in the preview Az.Functions module to date.

## Hybrid Az Cli & PowerShell
Using a combination of the two. Retreive the results with Azure CLI in raw JSON, then convert it to a powerShell object with `ConvertFrom-Json` and use the familar `Select-Object` syntax. Performant and familiar.

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

✅ Basic Az CLI knowledge required so good discoverability.

✅ Familiar syntax for consuming and manipulating the output with PowerShell.

## Deciding which to use
I don't really have a preference between Azure CLI and PowerShell but if you look at the output above you get exactly the same result using all three methods.

I do tend to lean towards the hybrid of the two as it is familiar to me without having to learn the nuances of JMESPath query filters but also gives me the flexibility of being able to pipeline that object into other PowerShell command or a `Export-*` cmdlet.

### Azure CLI Built-In Help
I always brag about how amazing the PowerShell help system is for people learning PowerShell but I also think that the team who built the Azure CLI have done an amazing job on the builtin help.

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

## Az Find for Examples and Discoverability

I didn't know this existed until recently but it's an awesome tool for discoverability at the command line when using Az CLI.

Here is what the help says about the command

```
Command
    az find : I'm an AI robot, my advice is based on our Azure documentation as well as the usage patterns of Azure CLI and Azure ARM users. Using me improves Azure products and documentation.
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

Try not to build something just for the sake of building something. The PowerShell community is definitely guilty of building a tool just because it suits our needs. Take a look at what is already out there and maybe think about contributing to that instead, that way an entire ecosystem benefits from your take on it.

The more that I use the Azure CLI, the more I am inclined to just dive in and use it, it is available in both bash and PowerShell on my Mac & in cloud shell in browser so I can just fire it up without having to think about it.