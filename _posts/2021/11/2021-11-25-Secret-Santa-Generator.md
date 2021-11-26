---
title: Secret Santa Generator
summary: PowerShell - The Gift That Keeps on Giving

excerpt_separator: <!--more-->

tags:
    - PowerShell
    - SecretSanta
---

This year I was designated to organise secret santa for the family 🎅

Naturally I opted to be a total nerd about it and use PowerShell to randomly generate who would be gifting presents.

### Ho Ho Ho
I started out knowing the list of people taking part, even if they didn't know they were participating at the time and I would just randomly assign them someone to buy a present for......simple right?!

<!--more-->

![image](https://user-images.githubusercontent.com/24279339/143514112-9c1ce449-4b7d-4f12-8498-af2c163eb493.png)

### Define a list of names
```powershell
# Names changed to protect the innocent
$names = @(
    'Jeff',
    'Maria',
    'Veronica',
    'Wolfgang',
    'Francisco',
    'Persephone',
    'Meryl',
    'Jerry'
)
```

Loop over the names and assign a santa and who they are buying for

```powershell
foreach ($person in $names) {
    # Looping through the list of names and make sure a person can't be their own secret santa
    $buyingFor = $names | Where-Object { $_ -ne $person } | Get-Random -Count 1
    
    # Print out the name of the person and the name of the person they are buying for
    '{0} is buying for {1}' -f $person, $buyingFor

    # Remove the person from the list so they can't be chosen again
    $names = $names | Where-Object { $_ -ne $buyingFor }
}
```

The downside to this is that every now and then there was an issue with the last person in the loop would sometimes not have anyone to buy for. I wonder if I could fix it so that I was last in the list 🤔

```powershell
Jeff is buying for Wolfgang
Maria is buying for Francisco
Veronica is buying for Persephone
Wolfgang is buying for Meryl
Francisco is buying for Maria
Persephone is buying for Jeff
Meryl is buying for Veronica
Jerry is buying for 🙃
```

### To Google
This wasn't as easy as my 'Quick PowerShell script' idea initially planned so naturally I did what any engineer would do!

I googled `powershell secret santa`

The first result was from Mike F. Robbins ([@mikefrobbins](https://twitter.com/mikefrobbins)) naturally and it looked perfect for what I was trying to do.

**Mike's blog post:** [https://mikefrobbins.com/2017/12/21/generate-a-secret-santa-list-with-powershell/](https://mikefrobbins.com/2017/12/21/generate-a-secret-santa-list-with-powershell/)

Interestingly he encountered the same issue I did with the basic loop sometimes leaving a santa without someone to gift a preset to. I decided to steal Mike's code to make my life easier.

### Mike's Secret Santa Function
*I've removed the comment based help for brevity.*
```powershell
function Get-MrSecretSantaList {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [ValidateCount(2,32768)]
        [string[]]$Name
    )
    if ($Name.Length % 2 -ne 0) {
        Throw 'An even number of Names must be specified in order for matching to occur.'
    }
    do {
        $Giftee = $Name | Sort-Object {Get-Random}
    }
    while (
        $(for ($i = 0; $i -lt ($Name.Length); $i += 1) {
            if ($Name[$i] -eq $Giftee[$i]) {
                Write-Verbose -Message "A duplicate has occured in loop $i Name: $($Name[$i]) cannot match Giftee: $($Giftee[$i])"
                $true
                break
            }
        })
    )
    for ($i = 0; $i -lt ($Name.Length); $i += 1) {
        [pscustomobject]@{
            Gifter = $Name[$i]
            Giftee = $Giftee[$i]
        }
    }
}
```

This works great but with only one problem! I am in the secret santa so I don't want to know who other people are buying for, including me! So i will have to change it up a little.

### Modified solution

The main changes are in the latter part of the function which generates the output. I don't want to print to the screen and spoil the surprise so I decided I would write the results to a file so that I could send the file to people without opening it and revealing who the recipients of the gift were.

```powershell
for ($i = 0; $i -lt ($Name.Length); $i += 1) {
    # Create a file name of the Secret Santa
    $fileName = '{0}.txt' -f $Name[$i]

    # Generate a file name of the Secret Santa with the persons name.
    # GetUnresolvedProviderPathFromPsPath will add the files to the $PWD
    $outputPath = Join-Path -Path $PSCmdlet.GetUnresolvedProviderPathFromPSPath('.') -ChildPath $fileName

    # Add a line of text to tell Santa who they are gifting the present to.
    $fileValue = 'Your buying for {0}' -f $Giftee[$i]
    Set-Content -Path $outputPath -Value $fileValue
}
```

### Outcome
This was the quickest way I could think of that would generate a secret santa for everyone without me, the organiser, knowing who was gifting presents to whom. I then sent the individual files to each participant so that only they knew who they were gifting a present to.

![OutputFile](https://user-images.githubusercontent.com/24279339/143514584-194e5f63-d7b6-4ac7-a83d-71d150b5ffb5.png)