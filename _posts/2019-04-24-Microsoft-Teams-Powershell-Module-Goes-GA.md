---
title: Microsoft Teams PowerShell Module is now Generally Available
summary: Teams PowerShell module at v1.0.0

excerpt_separator: <!--more-->

tags:
    - PowerShell
    - Microsoft Teams
    - Office 365
---

I have been playing with the Microsoft Teams module for quite some time and undeniably, even though it wasn't GA, it was disappointing and often required lots of wrapper functions for what I would have expected to be native functionality.

### Welcome Changes in the Release Notes

```yaml
- Connect-MicrosoftTeams allows you to specify a Teams Government Environment (-TeamsEnvironmentName) that your organization is homed in. 

- Get-Team allows you to specify new filter and selection criteria to identify specific teams based off of new criteria, including the Visibility or Archived state of the teams. 

The following beta cmdlets will not be available in future module releases, as the same functionality these cmdlets provided has been integrated into the Get-Team and Set-Team cmdlets: 

- Get-TeamFunSettings 
- Get-TeamGuestSettings 
- Get-TeamMemberSettings 
- Get-TeamMessagingSettings 
- Set-TeamFunSettings 
- Set-TeamGuestSettings 
- Set-TeamMemberSettings 
- Set-TeamMessagingSettings
```

So this looks interesting and maybe this also means that there is now pipeline support for the `Get-Team` and `Set-Team` cmdlets.

### Update to the latest version of the module.

```powershell
Update-Module MicrosoftTeams
```

You should then see any older and the GA version of the modules.

```powershell
PS C:\> Get-Module  -Name microsoftteams -ListAvailable

ModuleType Version    Name                                ExportedCommands                                          
---------- -------    ----                                ----------------                                          
Binary     1.0.0      MicrosoftTeams                      {Add-TeamUser, Get-Team, Get-TeamChannel, Get-TeamHelp...}
Binary     0.9.6      MicrosoftTeams                      {Add-TeamUser, Get-Team, Get-TeamChannel, Get-TeamHelp...}
```

```powershell
PS C:\> Import-Module -Name microsoftteams

PS C:\> Get-Command -Module microsoftteams

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Add-TeamUser                                       1.0.0      microsoftteams
Cmdlet          Connect-MicrosoftTeams                             1.0.0      microsoftteams
Cmdlet          Disconnect-MicrosoftTeams                          1.0.0      microsoftteams
Cmdlet          Get-Team                                           1.0.0      microsoftteams
Cmdlet          Get-TeamChannel                                    1.0.0      microsoftteams
Cmdlet          Get-TeamHelp                                       1.0.0      microsoftteams
Cmdlet          Get-TeamUser                                       1.0.0      microsoftteams
Cmdlet          New-Team                                           1.0.0      microsoftteams
Cmdlet          New-TeamChannel                                    1.0.0      microsoftteams
Cmdlet          Remove-Team                                        1.0.0      microsoftteams
Cmdlet          Remove-TeamChannel                                 1.0.0      microsoftteams
Cmdlet          Remove-TeamUser                                    1.0.0      microsoftteams
Cmdlet          Set-Team                                           1.0.0      microsoftteams
Cmdlet          Set-TeamChannel                                    1.0.0      microsoftteams
```

### Let's connect and have a play

```powershell
PS C:\> Connect-MicrosoftTeams
```

Use your passwordless sign-in method because passwords are for fools! 😉

Now when you run Get-Team you get all of the fun, guest and member settings that previously had their own cmdlets.
```powershell
PS C:\> Get-Team | Format-List

GroupId                           : ad52f8e5-1ed9-40c4-b1a0-0c5f6d62675e
DisplayName                       : Demo Team
Description                       : My team for testing things
Visibility                        : Public
MailNickName                      : DemoTeam
Classification                    :
Archived                          : False
AllowGiphy                        : True
GiphyContentRating                : strict
AllowStickersAndMemes             : True
AllowCustomMemes                  : False
AllowGuestCreateUpdateChannels    : True
AllowGuestDeleteChannels          : True
AllowCreateUpdateChannels         : True
AllowDeleteChannels               : True
AllowAddRemoveApps                : True
AllowCreateUpdateRemoveTabs       : True
AllowCreateUpdateRemoveConnectors : True
AllowUserEditMessages             : True
AllowUserDeleteMessages           : True
AllowOwnerDeleteMessages          : True
AllowTeamMentions                 : True
AllowChannelMentions              : True
```

`Get-Team` now has two parameter sets with some optional parameters for filtering teams

```powershell
Get-Team
 -GroupId <string>
 [-User <string>]
 [-Archived <bool>]
 [-Visibility <string>]
 [-DisplayName <string>]
 [-MailNickName <string>]
 [<CommonParameters>]

Get-Team
 [-User <string>]
 [-Archived <bool>]
 [-Visibility <string>]
 [-DisplayName <string>]
 [-MailNickName <string>]
 [<CommonParameters>]
```

There still doesn't appear to be a way to filter based on team name which if you have a lot of teams you're still going to have to find the GroupID or return all and use `Where-Object` and filter on the name.

Another nuance I discovered whilst playing is that `-DisplayName` appears to be case sensitive which isn't so much an issue unless you're working with the commands interactively but one to watch out for.

```powershell
PS C:\> Get-Team -DisplayName 'demo team'
PS C:\> Get-Team -DisplayName 'Demo Team'

GroupId                              DisplayName        Visibility  Archived  MailNickName       Description
-------                              -----------        ----------  --------  ------------       -----------
ad52f8e5-1ed9-40c4-b1a0-0c5f6d62675e Demo Team          Public      False     DemoTeam           My team for tes...
```

### Pipeline Support now works 🎉
It would appear that they have now added pipeline support from the getter to the setter, which for me was the biggest pain point in the previous iteration of the module and resulted in me writing my own wrapper functions.

```powershell
PS C:\> Get-Team -DisplayName 'Team Excellent'

GroupId                              DisplayName        Visibility  Archived  MailNickName       Description
-------                              -----------        ----------  --------  ------------       -----------
711108ee-5809-42da-a804-a020a735ef70 Team Excellent     Private     False     msteams_245c14

PS C:\> Get-Team -DisplayName 'Team Excellent' | Set-Team -Description '2nd test Team'

GroupId                              DisplayName        Visibility  Archived  MailNickName       Description
-------                              -----------        ----------  --------  ------------       -----------
711108ee-5809-42da-a804-a020a735ef70 Team Excellent     Private     False     msteams_245c14     2nd test Team

PS C:\> Get-Team -DisplayName 'Team Excellent' | Set-Team -AllowStickersAndMemes $false
```

Unfortunately the filtering for channels still only allows the use of a GroupID which means more cumbersome syntax
```powershell
PS C:\> get-Syntax Get-TeamChannel

Get-TeamChannel
 -GroupId <string>
 [<CommonParameters>]

PS C:\> Get-TeamChannel -GroupId (Get-Team -DisplayName 'Team Excellent').groupid

Id                                               DisplayName Description
--                                               ----------- -----------
19:90f4a123456784ceb1d831303ae5c39e@thread.skype General     2nd test Team
```

This is a vast improvement with the pipeline support and more discoverability with settings now residing in the `Get-Team` cmdlet.

### Still no Powershell Core Support 😢
```powershell
PS C:\> Import-Module MicrosoftTeams

Import-Module : Could not load file or assembly 'System.Windows.Forms, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'. The system cannot find the file specified.
At line:1 char:1
+ Import-Module MicrosoftTeams
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Import-Module], FileNotFoundException
+ FullyQualifiedErrorId : System.IO.FileNotFoundException,Microsoft.PowerShell.Commands.ImportModuleCommand
```