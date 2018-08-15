---
layout: post
title: Test Post
excerpt_separator:  <!--more-->
---

### Testing some default settings/content

Adding some additional placeholder text to

1. first
2. second
3. third

## Another heading

[Click This](https://millerb.co.uk)

## Yet Another Heading

Dolor dolore qui commodo amet tempor labore anim pariatur aliqua ad mollit. Ea culpa ut laborum aliqua consectetur labore fugiat. Excepteur dolore occaecat non laborum veniam deserunt ipsum nostrud ea laborum esse ipsum labore velit. Nulla dolore ad aute et veniam enim voluptate commodo cillum.

Aute eu adipisicing aliqua nostrud irure incididunt et aliquip aliquip. Magna aliquip tempor ipsum commodo. Culpa excepteur qui sunt eu incididunt voluptate do cillum. Dolore est cupidatat laborum mollit tempor ipsum minim voluptate.

Aliqua labore non proident excepteur laborum in officia aliqua officia nisi deserunt amet. Culpa amet nostrud voluptate officia reprehenderit laboris pariatur tempor est laboris exercitation occaecat sit cillum. Pariatur deserunt ex culpa sint. Velit do elit duis amet consequat amet anim ullamco ipsum exercitation culpa velit voluptate. Mollit velit proident nostrud sit quis. Commodo eu id fugiat ad cupidatat laboris. Reprehenderit consequat duis consectetur deserunt velit sit pariatur voluptate incididunt.

```powershell
#Add variables for old and new groups
#$OldGroup = "app-PowerProject-Users-3"
#$NewGroup = "app-SCCM SD-AstaPowerproject13.0.01x86"

#Manually enter old and new group names
$OldGroup = Read-Host "Enter Group to Transfer From"
$NewGroup = Read-Host "Enter Group to Transfer To"

#get all users of the $OldGroup & add these to a variable
$users = Get-ADGroupMember -Identity $OldGroup

#Measure how many users are going to transfer to $NewGroup
$Count = ($users).Count
Write-Output ""
Write-Host "$Count Objects will be transferred`n" -ForegroundColor Red

foreach ($user in $users) {
    Add-ADGroupMember -Identity $NewGroup -Member $user -Verbose
    }

Write-Host ""
Write-Host "Completed Successfully - Review any errors above"
```