---
title: Generating Passphrases Instead Of Passwords

tags:
  - Identity Management
  - Password
  - Powershell
  - Security
  - Active Directory
---

### Security vs Memorability
If you are involved with identity management you'll have encountered the password length and duration scenario, you will most probably have encountered and discussed the password vs passphrase at great length and embarked on the "Well passwords of `x` length have `x` entropy because....".

I'm not going to go into that here as it has already been discussed elsewhere in great length that I couldn't do it justice but a few links.

- [Stack Exchange comment](https://security.stackexchange.com/questions/6095/xkcd-936-short-complex-password-or-long-dictionary-passphrase/6096#6096) on the [classic XKCD comic - Password Strength](https://xkcd.com/936/).
- [NCSC Password Guidelines](https://www.ncsc.gov.uk/guidance/password-guidance-simplifying-your-approach) which suggest using passphrases for memorability.

![XKCD - Password Strength](https://imgs.xkcd.com/comics/password_strength.png)

### Solution
Use Passphrases that are both easy to remember but difficult to guess.

I have had this function in mind for well over a year and had a list of words to use but never actually got around to writing anything to accommodate creating passphrases. I had an hour spare whilst late night feeding new baba so figured I'd use it productively.

I created a function which generates passphrases from a list of words

### Usage

`New-PassPhrase -PassPhraseLength 35`

### Output
```powershell
future-right-beauty-slow-carlo-point-oregano
```
### Code
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
        [char[]]
        $Delimiter = '-'
    )
    
    begin {
        $wordlist = Get-Content -Path $PSScriptRoot\configuration\wordlist.txt
    }
    
    process {
        $phrasearr = @()
        while ($phrase.length -lt $PassPhraseLength) {
            $phrasearr += $wordlist | Get-Random
            $phrase = $phrasearr -join ''
        }
    }
    
    end {
        $phrasearr -join $Delimiter
    }
}
```