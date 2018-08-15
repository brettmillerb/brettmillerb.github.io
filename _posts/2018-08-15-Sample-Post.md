---
layout: post
title: Test Post
excerpt_separator:  <!--more-->
---

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