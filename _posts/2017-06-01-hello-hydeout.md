---
layout: post
title: Test Post
excerpt_separator:  <!--more-->
---

Adding my first Test Post from the original to save having to create a new file as I am being glared at by the wife.

### Customization

Hydeout replaces Hyde's class-based theming with the use
of the following SASS variables:

```powershell

function New-PassPhrase {
    <#
    .SYNOPSIS
    Generate PassPhrase for account logins
    
    .DESCRIPTION
    Generate a PassPhrase from a pre-defined list of words instead of using random character passwords
    
    .PARAMETER Length
    Length of PassPhrase to be generated
    
    .PARAMETER Delimiter
    The Delimiter to be used when outputting the PassPhrase. If no delimiter is specified then a hyphen is used '-'
    
    .EXAMPLE
    New-PassPhrase -Length 25

    .EXAMPLE
    New-PassPhrase -Length 25 -Delimiter ';'
    
    .NOTES
    NCSC UK Guidance on Secure Passwords
    https://www.ncsc.gov.uk/guidance/password-guidance-simplifying-your-approach
    #>
    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
            Position = 1)]
        [int]
        $PassPhraseLength,

        [Parameter(Position = 2)]
        [char]
        $Delimiter = '-'
    )
    
    begin {
        $wordlist = Get-Content -Path $PSScriptRoot\configuration\pnemonicwordlist.txt
    }
    
    process {
        $phrasearr = @()
        while ($phrase.length -lt $length) {
            $phrasearr += $wordlist | Get-Random
            $phrase = $phrasearr -join ''
        }
    }
    
    end {
        $phrasearr -join $Delimiter
    }
}
```