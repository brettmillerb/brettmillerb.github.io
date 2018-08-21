---
title: Store Encrypted Credentials For Easy Recovery
summary: Bored with typing passwords? Use a secure cred store and retrieve them easily.

excerpt_separator: <!--more-->

tags:
    - Powershell
    - Password Management
---

I seen a post a long while back on [Idera, to store multiple credentials](http://community.idera.com/powershell/powertips/b/tips/posts/multipass-securely-storing-multiple-credentials) for easy retrieval and store them in a secure manner.

Yes, there's a thousand different ways to store passwords and the passwords to your passwords but security isn't always convenient and should be secured with MFA so it quickly becomes tiresome to retrieve and store passwords.

Setting security aside for one minute, this is my take on a multi-password store which I can quickly access saving me a tonne of time on a daily basis.

<!--more-->

### New-MultiPass

Using this function you can create a credential file for as many accounts as you like, it then encrypts and stores the credentials in the Path specified.

```powershell
New-MultiPass -Path C:\users\corbyn.dallas\desktop -Name ADAdmin, O365Admin, Other
```

### Get-MultiPass

You can then retrieve the stored passwords from the location for use in Powershell console, scripts all day long.

```powershell
$multipass = Get-MultiPass -Path C:\Support\MultiPass.xml
```
Then access these through dot notation from the variable to pass to cmdlets or scripts.

```powershell
$multipass.leeloo

UserName                          Password
--------                          --------
leeloo.dallas System.Security.SecureString
```

```powershell
$multipass.Korben

UserName                          Password
--------                          --------
korben.dallas System.Security.SecureString
```

I've actually added the retrieval to my Powershell profile so that I don't even have to load it and when I change a password I create a new file.


### Code

```powershell
function New-MultipassFile {
    <#
    .SYNOPSIS
    Creates a MultiPass file to encrypt and store credentials.
    
    .DESCRIPTION
    Creates a MultiPass file to encrypt and store credentials for easy retrieval using Get-MultiPass.
    
    .PARAMETER Path
    Specifies the Path to the location to store the credential XML file.
    
    .PARAMETER Name
    Identifying name of the stored credential which can be referenced during retrieval.
    
    .PARAMETER Force
    Switch to force overwrite a file if it already exists in the Path specified.
    
    .EXAMPLE
    New-MultiPass -Path C:\users\korben.dallas\desktop -Name ADAdmin, O365Admin, Other

    .EXAMPLE
    New-MultiPass -Path C:\users\korben.dallas\desktop -Name ADAdmin, O365Admin, Other -Force
    #>
    [CmdletBinding(SupportsShouldProcess = $true)]
    param (
        [Parameter(Mandatory=$true,
                   Position=0,
                   ValueFromPipeline=$true,
                   ValueFromPipelineByPropertyName=$true)]
        [Alias("PSPath")]
        [ValidateNotNullOrEmpty()]
        [string]$Path,

        [Parameter(Mandatory=$true,
                   Position=1)]
        [ValidateNotNullOrEmpty()]
        [string[]]$Name,

        [Parameter(Mandatory = $false)]
        [switch]$Force

    )
    
    begin {
        $output = @{}
        $outpath = Join-Path -Path $Path -ChildPath 'MultiPass.xml'
    }
    
    process {
        if ($PSCmdlet.ShouldProcess($path, ("Creating Password Object for $Environment"))) {

            foreach ($item in $Name) {
                $output[$item] = Get-Credential -Message "Enter your $item Password"
                }
            }

        "Saving Multipass file to {0}" -f $outpath | Write-Verbose
        [pscustomobject]$output | Export-Clixml -Path $outpath -NoClobber:$(-not $Force)
    }
}

function Get-MultiPass {
    <#
    .SYNOPSIS
    Retrieves a previously created MultiPass file
    
    .DESCRIPTION
    Retrieves a previously created MultiPass file from the specified Path
    
    .PARAMETER Path
    Specifies a path to the location of a credential XML file.
    
    .EXAMPLE
    Get-MultiPass -Path C:\users\korben.dallas\desktop.multipass.xml
    #>
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [ValidateScript({Test-Path -Path $_ -PathType Leaf})]
        [String]$Path
    )

    process {
        Try {
            Import-Clixml -Path $Path -ErrorAction Stop
        }
        Catch {
            throw ("Unable to import Multipass File from {0}" -f $Path)
        }
    }
}

```

