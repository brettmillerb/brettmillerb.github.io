---
title: O365 License info using AzureADPreview PS Module
date: 2017-04-07T11:19:24+00:00

tags:
  - Active Directory
  - Auditing
  - Azure
  - O365
  - Powershell
---
We are still on-premises exchange 2013 with elements of cloud hybrid for Skype for Business as well as some small Office 365 tenancies. We are however fully utilising MS Azure for our Infrastructure and hosting needs as well as Azure Active Directory.

This means that I get the beauty of managing Azure AD synced users and access to the cloud via Active Directory security group delegation for SharePoint Online. Woot!

I've not really had much to do with the Azure platform until now aside from our infrastructure residing there but in all fairness as long as the lights are on and I can get access to them, I'm happy.

We initially bought SharePoint Online licenses, then decided that we would buy E1 licenses, then some E3 and more recently some E5 for good measure and due to the license buying being purely a procurement driven process the service wrap kind of got left behind and the license allocation has been messy to say the least and we have users with SharePoint and E1 licenses and a combination of service plans enabled and disabled with no consistency.

### AzureADPreview Powershell module

I could use the MSOnline module to manage licenses and the users in the cloud but this is being deprecated once the functionality is moved to the AzureAD module.

<https://docs.microsoft.com/en-gb/powershell/azure/install-msonlinev1?view=azureadps-1.0>

If you are running PS v5 then you can install the AzureAD module direct from powershell.

`Install-Module -Name AzureADPreview`

You can then investigate what it is that you have access to by running `Get-Command -Module AzureAdPreview Get*`

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureADUser                                    2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserAppRoleAssignment                   2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserCreatedObject                       2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserDirectReport                        2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserExtension                           2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserLicenseDetail                       2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserManager                             2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserMembership                          2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserOAuth2PermissionGrant               2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserOwnedDevice                         2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserOwnedObject                         2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserRegisteredDevice                    2.0.0.85   AzureADPreview
Cmdlet          Get-AzureADUserThumbnailPhoto                      2.0.0.85   AzureADPreview
```

This is always useful when playing with an unfamiliar module to see what sort of cmdlets are available that you can utilise.

After playing around for some time I was able to get something functional from the module that reports on the number of licenses. This information can be obtained from the Azure and O365 portals but who likes reports anyway.

Here is the script that I came up with to quickly get the license information. The only pre-requisite is that you have the AzureADPreview module installed and that you are connected to AzureAD using the Connect-AzureAD.

```powershell
<#
.SYNOPSIS
    Gets the current O365 license status
.DESCRIPTION
    Connects to O365 tenancy and gets the current license total, used and remaining counts and outputs to console.
.EXAMPLE
    Get-O365Licenses
.EXAMPLE
    Get-O365Licnses | Out-GridView
.OUTPUTS
    Name                         LicensesTotal LicensesUsed LicensesRemaining
    ----                         ------------- ------------ -----------------
    ENTERPRISEPREMIUM                       25            5                20
    POWERAPPS_INDIVIDUAL_USER            10000            2              9998
    YAMMER_ENTERPRISE_STANDALONE             1            0                 1
    ENTERPRISEPACK                          52           41                11
.NOTES
    Must be connected to AzureAD to run this script
    Use the Connect-AzureAD cmdlet to connect
#>
function Get-O365Licenses {
    [CmdletBinding()]
      
    Param (
    )
    
    begin {}
    
    process {
        $skus = Get-AzureADSubscribedSku

        foreach ($sku in $skus) {
            $properties = [ordered]@{
                Name = $sku.skupartnumber
                LicensesTotal = $sku.prepaidunits.enabled
                LicensesUsed = $sku.consumedunits
                LicensesRemaining = ($sku.prepaidunits.enabled) - ($sku.consumedunits)
            }

            $OutputObj = New-Object -TypeName psobject -Property $properties
            Write-Output $OutputObj
        }
            
    }

    end {}
}
```

Any corrections, recommendations or anything else happily welcomed and hopefully have some more content from the AzureAD module soon as I get the time to play some more.