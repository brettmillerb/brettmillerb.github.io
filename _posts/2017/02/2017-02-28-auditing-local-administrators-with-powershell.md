---
title: Auditing Local Administrators with Powershell
date: 2017-02-28 15:57:21

tags:
  - Active Directory
  - Auditing
  - Powershell
---
Previously I covered how we administer workstation local admin access within our environment.

[Workstation Local Admins and Auditing via Active Directory](http://millerb.co.uk/workstation-local-admins-and-auditing-via-active-directory/)

I briefly touched on damage limitation and being able to audit who has local admin permissions over their workstations at any given time.

I recently got a request through to check a list of computers for local administrator access. Due to the secure nature of the project, we weren't allowed any devices that had local admin on their workstations.

The list wasn't too lengthy but I figured to cater for requests like this that I needed a quick way to check for these groups so decided a Powershell function would be the easiest way to check for their existence.

### Powershell to the rescue

There was no direct way of converting the $Computername that I was provided into the group name without having to filter for the group name:

`Get-ADGroup -Filter { name -like '*Computername*' }`

As I didn't want to filter for every group due to the time it takes, even by specifying `-searchbase` parameter, I decided to utilise the standard formatting of the group name and construct the group name in the `foreach` loop.

```powershell
foreach ($computer in $computers) {
    $Groupname = "OU ac-$computer-LocalAdmin"
    get-adgroup $Groupname
}
```

With the 14 test computer accounts, this works a treat and confirms that Local Admin groups exist for the computer accounts in question.

The output of this isn't exactly tidy and just gives me a big long list but this also doesn't tell me if it failed to find a group so I need to expand my script to give me more relevant information.

I've kept the AD group check but added the try/catch and created a `pscustomobject` to grab some relevant details of the Computername, the group name if it exists and a simple confirmation that the group exists with a Yes or No.

```powershell
foreach ($computer in $computers) {
    $Groupname = "OU ac-$computer-LocalAdmin"
    try {
        $group = get-adgroup $Groupname -Properties members -ErrorAction Stop
        $properties = [ordered]@{
            Computername = $Computer
            Groupname = $group.name
            GroupExists = 'Yes'
        }
    }
    catch {
        $properties = [ordered]@{
            Computername = $Computer
            Groupname = $null
            GroupExists = 'No'
        }
    }
}
```

The output of this automatically formats into a table in the console but looks to be exactly what we want and tells me if a group exists or not.

![LocalAdminOutput](/_screenshots/LocalAdminOutput3.png)

What this doesn't tell me however is if the group has any members who are inheriting their local admin rights via this group. So a group can exist but it doesn't necessarily mean that there is anyone in it, so lets revisit the `pscustomobject` to see if we can get more granular information which will tell us this.

By adding the members attribute and basing the value on an if else statement counting the number of people who are a member of the group we can make the output a lot easier to read

```powershell
foreach ($computer in $computers) {
    $Groupname = "OU ac-$computer-LocalAdmin"
    try {
        $group = get-adgroup $Groupname -Properties members
        $properties = [ordered]@{
            Computername = $Computer
            Groupname = $group.name
            GroupExists = 'Yes'
            Members = if (($group.members).count -lt 1) {
                        '   No Members'
                        }
                        else {
                            $group.members
                        }
        }
    }
    catch {
        $properties = [ordered]@{
            Computername = $Computer
            Groupname = $null
            GroupExists = 'No'
            Members = 'No Members'
        }
    }
}
```

Now we're getting somewhere with all of the relevant information so we can provide an audit of the Local Admin groups as requested. It also enables us to identify which groups have no members so we can tidy these up so we only have groups in existence which are being used. Tidy site is a happy site.

What I did to take this further is turned this into a function so that I can use this on the pipeline and provide a list of computer names and it returns me output of whether they have corresponding local admin groups and if they in turn have members who are inheriting permissions.

```powershell
<#
.Synopsis
   Get AD Local Admin group for corresponding computer account
.DESCRIPTION
   Local Admin rights are granted via GPP using "FUJ ac-%computername%-LocalAdmin"
   AD accounts are created in Resource OU in AD and user added to those groups.
.EXAMPLE
   Get-ADLocalAdminGroup L9018210
   Get-ADLocalAdminGroup $machines
   $machines | Get-ADLocalAdminGroup
.EXAMPLE
   $machines | Get-LocalAdminGroup
#>

function Get-ADLocalAdminGroup
{
    [CmdletBinding()]
    Param (
        [Parameter(Mandatory=$true, 
                   ValueFromPipeline=$true)]
        [string[]]
        $Computername
    )

    Begin {
    }
    Process {
        foreach ($computer in $computername) {
            $Groupname = "OU ac-$computer-LocalAdmin"
            try {
                $group = get-adgroup $Groupname -Properties members
                $properties = [ordered]@{
                    Computername = $Computer
                    Groupname = $group.name
                    GroupExists = 'Yes'
                    Members = if (($group.members).count -lt 1) {
                                'No Members'
                              }
                              else {
                                $group.members
                              }
                }
            }
            catch {
                $properties = [ordered]@{
                    Computername = $Computer
                    Groupname = $null
                    GroupExists = 'No'
                    Members = 'No Members'
                }
            }
            finally {
                $obj = New-Object -TypeName psobject -Property $properties
                Write-Output $obj
            }
        }
    }
    End {}
}
```

![LocalAdminOutput2](/assets/img/LocalAdminOutput5.png)

This effectively means that I can check against any computer account to see if if has a Local Admin group and who the members of each group are. You can see under the members that some show as an array indicating that there are multiple members of the group so we can simply use `Select-Object`  Members and it will show us the users.

As always, feel free to provide feedback or guidance on my Powershell, code, dialect or anything else you want to point out. Hopefully this is useful for someone.