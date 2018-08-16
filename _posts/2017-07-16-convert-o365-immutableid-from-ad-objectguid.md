---
title: Convert O365 ImmutableID from AD objectGUID
date: 2017-02-08 00:25:29

tags:
  - Powershell
  - O365 
---
We had an issue with an account recently which meant that we had to hard delete the O365 account and re-synchronise from on-premises without deleting the on-premises AD account.

This lead me down the path of looking at the synchronisation rules in AAD Connect and the attributes objectGUID, ImmutableID and the source anchor used for pairing the two for synchronisation.

I’d previously read about soft and hard matching accounts via SMTP etc but never had to do it.

Initially I found this [IT Groove Article](http://itgroove.net/stellark/2016/03/25/recreate-a-deleted-user-in-ad-and-sync-to-office365/) which showed you how to recreate an on-premises user and link them back to an existing cloud account. It showed you how to get the ImmutableID from the objectGUID attribute of the on-premises account with ldifde.exe:

`ldifde -d "CN=miller, brett,OU=Users,DC=fqdn,DC=domain,DC=com" -f C:\support\user.txt`

It didn’t however explain how to do the same without removing the on-premises AD account.

As it turns out the ImmutableID is the base-64 version of the on-premises objectGUID.

Long story short we ended up deleting the cloud account, removing it from the recycle bin in O365, creating a new cloud account using the `New-AzureADUser` powershell cmdlet and changing the immutableID of the new account to match the on-premises account. Performing a delta sync updated the details of the cloud account to those of the on-premises user’s objectGUID.

Happy days, job done!

But I wouldn’t be me if I didn’t go overboard and look into it further than I needed to so I started looking at the Powershell method of converting the Immutable ID and objectGUID.

First blog post was [Sean Metcalf’s post](https://adsecurity.org/?p=478) (which is an essential blog for anyone that works with AD). Some further assistance from someone on Slack and came up with a way to convert from objectGUID.

The below snippet converts the objectGUID to the ImmutableID.

`[System.Convert]::ToBase64String($User.ObjectGUID.ToByteArray())`

The below snippet converts the ImmutableID to the objectGUID.

`New-Object -TypeName guid (,[System.Convert]::FromBase64String($immutableid))`

And to make this re-usable for future use.

```powershell
function Convert-ImmutableID {
    <#
    .SYNOPSIS
    Converts O365 ImmutableID to ActiveDirectory objectGUID
    
    .DESCRIPTION
    Converts O365 ImmutableID check cloud user against on-premises
    
    .PARAMETER ImmutableID
    The Immutable ID from O365/AzureAD which is a base-64 encoded version of the AD objectGUID
    
    .EXAMPLE
    Convert-ImmutableID 't3sJlM0QekeUJ32kOEe1hg=='
    
    .NOTES
    You can get the ImmutableID running:
    Get-AzureADUser -ObjectId brett.miller@millerb.co.uk | Select-Object immutableid
    #>
    [CmdletBinding()]
    Param (
        [Parameter(Mandatory=$true,
                   ValueFromPipeline=$true,
                   ValueFromPipelineByPropertyName=$true)]
        [ValidateNotNull()]
        [ValidateNotNullOrEmpty()]
        [string]$ImmutableID
    )
     process {
        New-Object -TypeName guid (,[System.Convert]::FromBase64String($immutableid))
    }
}
```