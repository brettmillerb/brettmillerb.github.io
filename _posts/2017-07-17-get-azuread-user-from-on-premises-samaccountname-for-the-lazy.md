---
title: 'Get AzureAD User from On-premises sAMAccountname &#8211; For the lazy'
date: 2017-07-17T11:40:45+00:00

tags:
  - Active Directory
  - Azure
  - O365
  - Powershell
---
Since starting to use the AzureAD module for managing Azure or Office 365 users you'll probably notice that the syntax of some of the cmdlets aren't as simple to use as the ActiveDirectory module and that you actually have to type or autocomplete the parameters or you get an error that it doesn't know what you're talking about.

So when looking up a user from on-premises Active Directory you can generally just omit the parameters such as

`Get-ADUser brett.miller`

Omitting the -Identity parameter and it still finds the user you want so long as it's a valid user. However if we try this with the AzureAD cmdlet we get an error!

`Get-AzureADUser : A positional parameter cannot be found that accepts argument 'brett.miller@domain.com'`

So in my usual fashion I quickly got bored with having to auto-complete the parameter -ObjectID to find the user but on top of this you have to use the UPN to find the user, which to great annoyance is the same as the sAMAccountname and primary SMTP due to the work you put in with the IDFix tool to ensure you had no dirsync errors.

I can now just use this function to save me having to type out full UPN's to find users in AzureAD

```powershell
function Get-ADUserAzure {
    <#
    .SYNOPSIS
    Gets the AzureAD account from the sAMAccountname of on-premises user
    
    .DESCRIPTION
    Looks up the on-premises sAMAccountname and queries AzureAD using the UPN from the on-premises account.
        
    .PARAMETER username
    sAMAccountname of on-premises user account
    
    .EXAMPLE
    Get-ADUserAzure brett.miller
    
    .NOTES
    Saves having to type out the full UPN of a user to look them up in AzureAD
    #>
    [cmdletbinding()]
    Param (
        [Parameter(Mandatory=$true,
                   Position=0,
                   ValueFromPipeline=$true,
                   ValueFromPipelineByPropertyName=$true)]
        [ValidateNotNull()]
        [ValidateNotNullOrEmpty()]
         [ValidateScript({
                try {
                    (Get-aduser -identity $_ -ErrorAction Stop)
                    $true
                }
                catch {
                    throw "User does not exist"
                }
        })] 
        [string[]]$username
    )
    process {
        foreach ($user in $username) {
            get-azureaduser -objectid (get-aduser -Identity $user).userprincipalname
        }
    }
}
```