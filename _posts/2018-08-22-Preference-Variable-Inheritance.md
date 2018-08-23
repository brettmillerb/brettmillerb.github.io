---
title: Inheriting Preference Variables into Scriptblocks
summary: Inheriting preference variables from parent scopes

excerpt_separator: <!--Read more-->

tags:
    - Powershell
    - Windows Server
    - Powershell Remoting
---

So I recently created a module to using Powershell sessions to invoke commands on remote machines.

### Preference Variables not inherting from Parent Scope
I hit an issue during testing and was trying to diagnose the issue so I added some `Write-Verbose` commands to assist with this except the verbose preference was not inheriting from the parent scope.

<!--Read more-->

So I've been doing powershell long enough to know that calling a function should inherit the parent scope preference variables e.g.:

```powershell
function Do-Stuff {
    [cmdletbinding()]
    param ()

    "Doing Stuff"
    "Doing Stuff Verbosely" | Write-Verbose
}

function Do-Something {
    [cmdletbinding()]
    param ()

    "Doing Something"
    Do-Stuff
}

Do-Something -Verbose
```
Gives you the output
```powershell
Doing Something
Doing Stuff
VERBOSE: Doing Stuff Verbosely
```

So a bit strange that inheritance wasn't working in a scriptblock being passed to a powershell session.

```powershell
$scriptblock = {
    Param (
        [string]$sbparam
    )

    $sbparam
    "Some other Verbosity" | Write-Verbose
}

Invoke-Command -ScriptBlock $scriptblock -ArgumentList 'testing string' -Verbose
```
This did not trigger the verbose output in the scriptblock as intended.

My initial thought was maybe the scriptblock needs `[cmdletbinding()]` to access the verbose parameters to bind to them but that didn't make sense.

### Solution

Seems that this form of inheritance doesn't work as the scriptblock is being run within the session of the remote machine so [Chris Gardner](https://twitter.com/HalbaradKenafin) gave me the solution that setting the `$VerbosePreference` within the scriptblock would trigger the verbose output from the remote machine.

```powershell
$scriptblock = {
    Param (
        [string]$sbparam
    )

    $VerbosePreference = 'continue'

    $sbparam
    "Some other Verbosity" | Write-Verbose
}

Invoke-Command -ScriptBlock $scriptblock -ArgumentList 'testing string' -Verbose
```
Gives you the output
```powershell
testing string
VERBOSE: Some other Verbosity
```

I think that everyone who picks up powershell has been tripped up with scope issues in the past. I know that I have in the past and more than likely will again in the future but this worked a treat.

It turns out that it wasn't even my code that was the issue. I was attempting to run my commands in the old location of the agent I was interacting with so setting the new location worked and I learned something about scopes in the process.

#### Update
It appears that this was raised by [Chris Bergmeister (MVP)](https://twitter.com/CBergmeister) and was discussed on Github around the default behavior of the preference variables in a scriptblock.

It's brief but outlines what I went through above:  [https://github.com/PowerShell/PowerShell/issues/4040](https://github.com/PowerShell/PowerShell/issues/4040)