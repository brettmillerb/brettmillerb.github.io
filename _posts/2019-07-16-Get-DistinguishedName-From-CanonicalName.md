---
title: Get DistinguishedName from CanonicalName
summary: Converting the CanonicalName to DistinguishedName from Active Directory

excerpt_separator: <!--more-->

tags:
    - PowerShell
    - ActiveDirectory
    - tag3
---

There was a topic came up in the [Powershell Slack](https://aka.ms/psslack) earlier this evening to see if there was a way to convert the CanonicalName from Active Directory to a DistinguishedName

I had a quick google figuring that something like that must have been done already but a quick google didn't find anything that seemed to do the job.

There was a [TechNet Script](https://gallery.technet.microsoft.com/scriptcenter/Get-CanonicalName-Convert-a2aa82e5) to go from DistinguishedName to CanonicalName but since that was removing content it was slightly simpler to do with string splitting and regex.

### Crack Open The Regex ❤
I decided about 12 - 18 months ago that I was going to double down and force myself to learn Regex. This is a prime example of something you can play with so that was my first port of call.

```powershell
# My Dataset
$cns = @(
    'MyDomain.co.uk/Corp/Users/User2 Test'
    'OtherDomain.com/Corp/Users/User2 Test'
    'sub.domain.org.co.uk/Corp/Users/User2 Test'
    'sub.sub.domain.org/Corp/Users/User2 Test'
)

# Regex to match the pattern
$cns -match '^(.*\.\w+)(?:\/)(\w+)(?:\/)(\w+)(?:\/)([\w\s]+)$'
```

### Regex Explained
```powershell
# Start of string Anchor
# Capturing Group '()'
# Containing any character '.*' between zero and unlimited times
# Followed by '.' ('\' escapes the '.') and any word character '\w+' one more times
^(.*\.\w+)
# A Non-Capturing Group (?:\/) capturing the '/'
(?:\/)
# Any word character between one and unlimited times '\w+'
(\w+)
# A Non-Capturing Group (?:\/) capturing the '/'
(?:\/)
# Any word character between one and unlimited times '\w+'
(\w+)
# A Non-Capturing Group (?:\/) capturing the '/'
(?:\/)
# A Character class matching any word character '\w' or space character '\s'
([\w\s]+)
# End of Line anchor
$

```

This works but this is terrible by anyones standards

```powershell
function Get-DistinguishedName {
    param (
        [string[]]
        $CanonicalName
    )
    foreach ($cn in $CanonicalName) {
        # Match the entire pattern of the CanonicalName populating $Matches automatic variable
        $cn -match '^(.*\.\w+)(?:\/)(\w+)(?:\/)(\w+)(?:\/)([\w\s]+)$'
        # Prefix the Domain part with 'DC=' after splitting on the '.'
        $dc = $matches[1] -split '\.' -replace '^.*$', 'DC=$0'
        # Prefix the first part with 'CN='
        $cn = $matches[4] -replace '^.*$', 'CN=$0'
        # Prefix the rest with 'OU='
        $ou = $matches[2..3] -replace '^.*$', 'OU=$0'
        # Build an array of those parts and join with a ','
        @($cn, ($ou -join ','), ($dc -join ',')) -join ','
    }
}
```

This will literally only work for the CanonicalNames that I tested with and will die a horrible death if that format changes as I am hardcoding the index positions of the string in order to add the relevant `CN=`, `OU=` or `DC=`

So to make this a little less likely to break I ditched the regex and went with some string split goodness.

```powershell
$cn = 'OtherDomain.com/Corp/Users/User2 Test'
# Split on the '/' creating an array
$arr = $cn -split '/'
# Reverse the order of the array
[array]::reverse($arr)
# Create a new empty array to store the modified values
$output = @()
# Select the first item for CN, prefix value with 'CN='
$output += $arr[0] -replace '^.*$', 'CN=$0'
# Select all other parts of the array apart from the first and last items, prefix with 'OU='
$output += ($arr | select -Skip 1 | select -SkipLast 1) -replace '^.*$', 'OU=$0'
# Filter the array for domain name, split on the '.' and prefix with 'DC='
$output += ($arr | ? { $_ -like '*.*' }) -split '\.' -replace '^.*$', 'DC=$0'
# Join all values together with ','
$output -join ','
```

Stitching that all together with the array of test CN's

```powershell
@(
    'MyDomain.co.uk/Corp/Users/User2 Test'
    'OtherDomain.com/Corp/Users/User2 Test'
    'sub.domain.org.co.uk/Corp/Users/User2 Test'
    'sub.sub.domain.org/Corp/Users/User2 Test'
) | ForEach-Object {
    $arr = $_ -split '/'
    [array]::reverse($arr)
    $output = @()
    $output += $arr[0] -replace '^.*$', 'CN=$0'
    $output += ($arr | select -Skip 1 | select -SkipLast 1) -replace '^.*$', 'OU=$0'
    $output += ($arr | ? { $_ -like '*.*'}) -split '\.' -replace '^.*$', 'DC=$0'
    
    $output -join ','
}
```
**Output**
```powershell
CN=User2 Test,OU=Users,OU=Corp,DC=MyDomain,DC=co,DC=uk
CN=User2 Test,OU=Users,OU=Corp,DC=OtherDomain,DC=com
CN=User2 Test,OU=Users,OU=Corp,DC=sub,DC=domain,DC=org,DC=co,DC=uk
CN=User2 Test,OU=Users,OU=Corp,DC=sub,DC=sub,DC=domain,DC=org
```

### Turning that into a Function
```powershell
function  Get-DistinguishedName {
    param (
        [Parameter(Mandatory,
        ParameterSetName = 'Input')]
        [string[]]
        $CanonicalName,

        [Parameter(Mandatory,
            ValueFromPipeline,
            ParameterSetName = 'Pipeline')]
        [string]
        $InputObject
    )
    process {
        if ($PSCmdlet.ParameterSetName -eq 'Pipeline') {
            $arr = $_ -split '/'
            [array]::reverse($arr)
            $output = @()
            $output += $arr[0] -replace '^.*$', 'CN=$0'
            $output += ($arr | select -Skip 1 | select -SkipLast 1) -replace '^.*$', 'OU=$0'
            $output += ($arr | ? { $_ -like '*.*' }) -split '\.' -replace '^.*$', 'DC=$0'
            $output -join ','
        }
        else {
            foreach ($cn in $CanonicalName) {
                $arr = $cn -split '/'
                [array]::reverse($arr)
                $output = @()
                $output += $arr[0] -replace '^.*$', 'CN=$0'
                $output += ($arr | select -Skip 1 | select -SkipLast 1) -replace '^.*$', 'OU=$0'
                $output += ($arr | ? { $_ -like '*.*' }) -split '\.' -replace '^.*$', 'DC=$0'
                $output -join ','
            }
        }
    }
}
```

```powershell
Get-DistinguishedName -CanonicalName @(
    'MyDomain.co.uk/Corp/Users/User2 Test'
    'OtherDomain.com/Corp/Users/User2 Test'
    'sub.domain.org.co.uk/Corp/Users/User2 Test'
    'sub.sub.domain.org/Corp/Users/User2 Test'
)

@(
    'MyDomain.co.uk/Corp/Users/User2 Test'
    'OtherDomain.com/Corp/Users/User2 Test'
    'sub.domain.org.co.uk/Corp/Users/User2 Test'
    'sub.sub.domain.org/Corp/Users/User2 Test'
) | Get-DistinguishedName
```

```powershell
CN=User2 Test,OU=Users,OU=Corp,DC=MyDomain,DC=co,DC=uk
CN=User2 Test,OU=Users,OU=Corp,DC=OtherDomain,DC=com
CN=User2 Test,OU=Users,OU=Corp,DC=sub,DC=domain,DC=org,DC=co,DC=uk
CN=User2 Test,OU=Users,OU=Corp,DC=sub,DC=sub,DC=domain,DC=org
```

### Summary
What I hoped to demonstrate from this is that you don't need to get a fully working solution to a problem to learn something from it. Also that your first iteration of a script might not be the best but if it works you have something build upon.

Playing with regex on some unfamiliar patterns on a semi-regular basis has taught me loads and I love tackling problems with it now. This has saved me a tonne of work in areas. Don't be afraid to start learning it, it looks scary at first but the more you do something, the easier it becoems.

I'd be interested to see how other people tackle this or similar problems or just get stuck into learning something by doing. 🙂