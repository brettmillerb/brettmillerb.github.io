---
title: Tab Completion for Your Azure Subscriptions
summary: No More Copy and Pasting Subscription Names

excerpt_separator: <!--more-->

tags:
    - PowerShell
    - Az
    - Azure
---

If you work in Azure then there's a good chance that you will have multiple subscriptions to manage, whether this is for various companies, various products or production & test environments.

If you do alot of interactive work in the shell for these subscriptions you'll quickly find out how annoying it is to have to find the subscription names so that you can change between your subscriptions.

### Lots of Subscriptions -eq Lots of waiting

Running `Get-AzSubscription` takes a while when you have lots of subscriptions

```powershell
(Get-AzSubscription).count
32
```

```powershell
 Measure-Command -Expression { Get-AzSubscription } | select Milliseconds, seconds

Milliseconds Seconds
------------ -------
         408       4

```

<!--more-->

Now 4 seconds doesn't seem like a lot of time to wait but if your subscription names aren't easy to type you tend to type `Get-AzSubscription` every time you need to switch between contexts/subscriptions.

This got me thinking about how to make this quicker and a nicer experience.

### PowerShell Profile

#### Option 1 - Bung it in your profile
The simple thing to do would be to define a variable in my profile and run `Get-AzSubscription` when I load PowerShell and I could have a list to hand in each session.

Profile.ps1:
```powershell
$subscriptions = Get-AzSubscription | Select-Object -ExpandProperty Name
```

Profile Load Time:
```powershell
Loading personal and system profiles took 8276ms.
[16:46:58] PowerShell\7-preview>
```

8 Seconds! 😲

#### Option 2 - Bung it in your profile as a job
As it turns out the `Get-AzSubscription` cmdlet has an `-AsJob` parameter.

It's almost as if Microsoft know it takes a while to retrieve them by adding the ability to run it as a job

Profile.ps1:
```powershell
$subscriptions = Get-AzSubscription -AsJob |
    Wait-Job | Receive-Job |
        Select-Object -ExpandProperty Name
```

Profile Load Time:
```powershell
Loading personal and system profiles took 4629ms.
[16:58:11] PowerShell\7-preview>
```

4.6 seconds....better but still not ideal 😕

#### Option 3 - Bung it in your profile as a job but only receive it at runtime
So I can utilise the job to execute in the background and since I don't need to do anything with the results straight away I will just retrieve the results of the job when I need them.

Profile.ps1:
```powershell
Get-AzSubscription -AsJob | Out-Null
```

Profile Load Time:
```powershell
Loading personal and system profiles took 2565ms.
[16:58:11] PowerShell\7-preview>
```

2.5 seconds. Much Better but I don't want to have to remember to retrieve the results from the variable or I'll just end up going straight for `Get-AzSubscription` rendering this whole blog post pointless. 🤷‍♂️

### PowerShell Profile & Argument Completer

Since I didn't want to manually retrieve the results to be used in another command anyway I wanted to use an argument completer so I can tab complete through my subscriptions. Normally you would retrieve the results within the scriptblock of the argument completer but waiting 8 seconds for the results to return would be painstaking.

```powershell
Register-ArgumentCompleter -CommandName Set-AzContext -ParameterName Subscription -ScriptBlock {
    Get-AzSubscription | Select-Object -ExpandProperty Name | foreach-object {
        [System.Management.Automation.CompletionResult]::new(
            $_
        )
    }
}
```

If you want to read up more on Argument Completers [Joel Sallow (Vexx32)](https://twitter.com/vexx32) has a good blog post on the available options: https://vexx32.github.io/2018/11/29/Dynamic-ValidateSet/

### Solution
I decided to use both the profile job and the argument completer but since the argument completer scriptblock is not invoked until the command it's being used for is run it won't affect the profile load time.

Profile.ps1:
```powershell
# Get all azure subscriptions as a background job
Get-AzSubscription -AsJob | Out-Null

Register-ArgumentCompleter -CommandName Set-AzContext -ParameterName Subscription -ScriptBlock {
    # If the job exists from the command Get-AzSubscription, receive the results & remove the job
    if ($job = Get-job -Command Get-AzSubscription | Wait-Job) {
        $global:azSubscriptions = Receive-Job -Id $job.Id | Select-Object -ExpandProperty name
        Remove-Job -Id $job.Id
    }
    # Add the completion results for the parameter
    $global:azSubscriptions | foreach-object {
        [System.Management.Automation.CompletionResult]::new(
            $_
        )
    }
}
```

Profile Load Time:
```powershell
Loading personal and system profiles took 2565ms.
[16:58:11] PowerShell\7-preview>
```

This didn't affect the profile load time at all as it uses a background job to retrieve the results and it doesn't have a perfomance impact on loading the completion result as they are already defined and only invoked when you use the cmdlet the completer is for.

No more copying and pasting subscription names to switch between Azure subscriptions 🎉