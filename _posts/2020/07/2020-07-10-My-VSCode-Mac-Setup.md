---
title: Making The Switch to MacOS
summary: An upated post on my previous VSCode productivity post.

excerpt_separator: <!--more-->

tags:
    - PowerShell
    - MacOs
    - VSCode
    - Productivity
---

### WARNING! - Long Post Alert.....Again.
{: .no_toc}

I previously wrote a post on this which was very popular as I tried to document all of my productivity hacks and shortcuts that I use on a daily basis to assist in writing PowerShell. That post is [My VSCode Setup](https://millerb.co.uk/2019/02/28/My-Vscode-Setup.html).

I have since moved from a Windows laptop to MacOS so I wanted to make a note of customisations and changes so I had a place to track all of them but other people may also find some of them handy.

Some stuff won't have changed too much because, well.....there was no need but my way of working has shifted slightly and I spend more time with a split terminal between bash and PowerShell.

<!--more-->

I thought the switch to MacOS was going to be more difficult than it initially was but once I sorted my muscle memory out (mainly for copy and paste) it was a pretty seamless transition. The multiple desktops and the trackpad/gestures is an awesome experience.

Unfortunately I do have to keep an Azure VM handy for the **very** odd ocassion that I may need something Windows specific.

* TOC
{:toc}

## Homebrew
Switching from Windows where I used Chocolatey for my software installation needs, I had heard about homebrew and was keen to make a start with using a decent package manager from the start.

You can install homebrew from the command line too!

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

Now that you have Homebrew installed you can go about installing the rest of your applications.

For reference:

```bash
❯❯ brew list -1                     ❯❯ brew cask list -1
azure-cli                           azure-data-studio
gdbm                                discord
ncurses                             firefox
openssl                             insomnia
openssl@1.1                         iterm2
pcre                                keepassxc
python                              microsoft-azure-storage-explorer
python@3.8                          microsoft-edge-beta
readline                            microsoft-remote-desktop-beta
sqlite                              obs
xz                                  powershell-preview
zsh                                 sizeup
zsh-completions                     slack
                                    spotify
                                    spotmenu
                                    visual-studio-code-insiders
                                    zoomus
```

## Terminal -lt iterm2
So rather than use the standard built in terminal that comes pre-installed with MacOS I have opted to use iterm2 (mainly because I heard other people say that it was awesome and no other reason) and I love it.

It supports pretty much anything you can throw at it in terms of fonts, customisations and themes.

### Setting zsh as the default shell

```bash
chsh -s /bin/zsh
```

## Customisations/Tweaks
I seen a gist from [@markwragg](https://twitter.com/markwragg) which I bookmarked with the anticipation that I may one day switch to MacOS then unknowingly I made that switch my self. He documented some of the changes that he made when he switched to MacOS. That [gist is here](https://gist.github.com/markwragg/d98645aa9ffb74623342cf09cf0c25e6) and has some handy stuff within.

I chose not to make as many changes as Mark but the ones I blatantly stole are:

### Make the dock appear faster when using auto hide
I have my notification bar and dock hidden on my mac for maximum screen real estate so this helps when quickly trying to access the dock.

```bash
defaults write com.apple.dock autohide-delay -float 0
killall dock
```

### Enable Case Insensitve Autocomplete
Case insensitive auto-complete is a total life saver coming from the windows world and I don't know how many hours I have lost on linux machines debugging my own stupidity.

```
echo "set completion-ignore-case On" >> ~/.inputrc
```

### iterm2 Keyboard Shortcuts
Pressing ESC to clear the whole line in the terminal which is familiar behaviour for me and one of the muscle memory options that I want to keep. The rest of my keyboard shortcuts are listed below (that I can remember adding).

iTerm2 > Preferences > Profiles > Keys

| Key Combination | Action | Wassat? |
| :--: | :--: | :--: |
| ESC | Send Hex Codes 0x15 | Clears the current line |
| ⌥ Backspace | Send Hex Codes: 0x17 | BackwardKillWord |
| ⌥ <- | Send Escape Sequence b | BackwardWord |
| ⌥ -> | Send Escape Sequence f | ForwardWord |
| ⌥ Shift <- | Move Start of Selection Back -> Move by Word | Select BackwardWord |
| ⌥ Shift -> | Move End of Selection Forward -> Move by Word | Select ForwardWord |

There are probably more but these are the ones I can remember adding.

## Oh My Zsh
> Oh My Zsh is a delightful, open source, community-driven framework for managing your Zsh configuration. It comes bundled with thousands of helpful functions, helpers, plugins & themes.

## Profile
This has changed slightly as I am now on MacOS in the fact that I am now running two different profiles for bash/zsh and one for PowerShell.

### Bash / zshrc profile
I am using PowerLevel9k for my theme which offers some nice customisations. I have not really played around with this beyond my initial setup which I am quite happy with.

I am now finding that I am using zsh as a primary shell over PowerShell for day to day interaction especially when interacting with git. I also tend to stick to bash for Azure CLI too for the tab completion and speed it offers.

![iterm2 bash profile](https://user-images.githubusercontent.com/24279339/91507315-6d1fe280-e8cc-11ea-9df9-cd2df562693b.png)

I have added this [as a gist](https://gist.github.com/brettmillerb/5f9197ce3568b230f52c9641d92f99bb).

There are a few things that I have had to add to make my life easier when switching between bash/zsh and PowerShell so that I have consistent commands available to me.

`cls` is too engrained and quicker to type so that has to go in there. The other two aliases are pretty self-explanatory.

```bash
# aliases
alias cls='clear'
alias code='code-insiders'
alias pwsh='pwsh-preview'
```

The other thing that I really like is the tab completion for Azure CLI which doesn't appear to work with zsh out of the box. Adding this to your zshrc profile enables that.

```bash
# Azure CLI tab completion
autoload bashcompinit && bashcompinit
source $ZSH/oh-my-zsh.sh
source /usr/local/etc/bash_completion.d/az
```

### Powershell Profile

**Update** After a thread on twitter by [Dave Carroll](https://twitter.com/thedavecarroll/status/1295379473697832967?s=20) I decided to take a little time and fix up my pwsh profile to something a bit nicer.

This has changed a little since I moved. Mainly to accommodate differences between bash and PowerShell on Mac so that they work in a consistent manner.

My Toolbox module that I import in my profile can be found [here](https://github.com/brettmillerb/Toolbox) in my github repo. This has the following functions which are imported into my session.
```powershell
# Module with some helper functions
Import-Module -Name Toolbox
```

```powershell
Backup-Profile.ps1                 # Backs up my profile to github
Export-AzKeyVaultCertificate.ps1   # Export Azure Key Vault PFX Certificate
Get-CommandInfo.ps1                # Stolen from Chris Dent 😃
Get-Gituser.ps1                    # Shows the current folders git user from gitconfig
Get-MultiPass.ps1                  # Windows only CliXml Command to import Credentials object
Get-Syntax.ps1                     # Reformatting of Get-Command $Command -Syntax to print vertically
New-MultiPass.ps1                  # Windows only CliXml Command to create Credenials object
```

I like being able to traverse the directory structures quickly so these shortcuts allow me to jump back one or two directories quickly and are also available in zsh so it's consistent behaviour across shells.

```powershell
# functions to be aliased to make traversing directories consistent with zsh shortcuts
function BackOne {
    Set-Location ..
}
function BackTwo {
    Set-Location ../..
}
```

zsh has some handy default parameters for `ls` so I have added these to my PowerShell prompt as I am too used to `ls` to stop using it so may as well make it consistent right?!

```powershell
function Get-NativeChildItem {
    & (Get-Command ls -CommandType Application) -lhG
}
function Get-NativeChildItemG {
    & (Get-Command ls -CommandType Application) -G
}
function Get-NativeChildItemA {
    & (Get-Command ls -CommandType Application) -lAhG
}
```
Alias all of the things
```powershell
New-Alias -Name code -Value 'code-insiders'
New-Alias -Name '..' -Value 'BackOne'
New-Alias -Name '...' -Value 'BackTwo'
New-Alias -Name ll -Value Get-NativeChildItem
New-Alias -Name ls -Value Get-NativeChildItemG
New-Alias -Name la -Value Get-NativeChildItemA
# clip on windows is clip.exe and I use this a lot.
New-Alias -Name clip -Value Set-Clipboard
```

PSReadline is awesome and it's made even more awesome on MacOS with the [PSUnixUtilCompleters](https://www.powershellgallery.com/packages/Microsoft.PowerShell.UnixCompleters/0.1.1) PowerShell module by [@rjmholt](https://twitter.com/rjmholt) which gives you parameter completers for native linux and MacOS comands.

Enable this in your profile with the below:
```powershell
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward

# Menu Complete for tab completion is really nice and gives you parameter information during selection.
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete
Set-PSReadLineOption -HistorySearchCursorMovesToEnd:$true
```

After seeing [Tyler Leonhardt (@TylerLeonhardt)](https://twitter.com/TylerLeonhardt) example in the twitter thread I decided to give `Posh-Git` a go with `Oh-My-Posh` and I am very pleased with the result.

- Git auto-completion
- VCS status in my prompt
- Easy customisation
- Consistent Powerlevel theme between zsh and PS

```powershell
Import-Module posh-git
Import-Module oh-my-posh
$gitpromptsettings.DefaultPromptAbbreviateHomeDirectory = $true
$GitPromptSettings.DefaultPromptSuffix = $('`n❯❯ ' * ($nestedPromptLevel + 1))

# This is to prevent an issue where iterm2 throws an error every time the prompt is run
if ($env:LC_TERMINAL -eq "iTerm2") {
    $ThemeSettings.Options.ConsoleTitle = $false
}

Set-Theme Powerlevel10k-Lean
$ThemeSettings.CurrentUser = $null
$ThemeSettings.PromptSymbols['promptindicator'] = ("`n{0} " -f ([char]::ConvertFromUtf32(0x276F) * 2))
```

One other cool thing that I added to my profile recently was enabling the ability to traverse folders without having to use `Set-Location` or `cd` which is consistent behaviour with zsh.

I found an old blog post on devblogs where [Jason Shirk(@lzybkr)](https://twitter.com/lzybkr) had written how to do this so I modified it slightly for my needs so that any command you type is evaluated and if it's a path it will traverse into that directory. This is yet more consistency between the two shells and saves on the typing. Coupled with the `..` & `...` aliases it makes navigation so pleasant.

```powershell
# Taken from here: https://devblogs.microsoft.com/scripting/whats-in-your-powershell-profile-powershell-team-favorites/
# Changing directories without having to type cd to replicate zsh
$ExecutionContext.InvokeCommand.CommandNotFoundAction = {
    param([string]$commandName,
        [System.Management.Automation.CommandLookupEventArgs]$eventArgs
    )
    # Remove the ‘get-‘ prefix that confuses Test-Path after we produce
    # something that is path like after the get-.

    if ($commandName.StartsWith(‘get-‘)) {
        $commandName = $commandName.Substring(4)
    }  

    # If the command looks like a location, just switch to that directory
    if (Test-Path -Path $commandName) {
        $eventArgs.CommandScriptBlock = { Set-Location -LiteralPath $commandName }.GetNewClosure()
        return
    }
}
```

I have updated the gist of my profile [here](https://gist.github.com/brettmillerb/467e4ee2d7f8e97897f106a5534f884b).

![Pwsh prompt MacOS](https://user-images.githubusercontent.com/24279339/91505604-df41f880-e8c7-11ea-9dcd-cd46e06c4923.png)

## Remote Development Extension
I wrote about [Developing Pwsh Azure Functions inside a Container](https://millerb.co.uk/2019/11/27/Developing-Pwsh-Az-Functions-In-Docker-And-VsCode.html) some time ago and it was really my my first foray into using docker as a daily tool but this is now how I work every day for most of the repositories and code that I write.

The above post covers pretty much everything you need to get started with that but using docker as a development enviroment has been a game changer.

I use Azure functions a lot, both PowerShell and C# and being able to develop these in a container without having to have the Az Functions Core tools or npm or any of the extensions installed locally is a dream. You need to check it out if you haven't already.

## JQ
So I have written already about [how I use AzureCLI & PowerShell together](https://millerb.co.uk/2019/12/07/Az-CLI-vs-Az-PowerShell.html) but I was also made aware of [jq](https://stedolan.github.io/jq/) which I suppose is similar to how I would normally use `ConvertFrom-Json` or `ConvertFrom-Json`.

I have been trying to get better with this instead of just defaulting to PowerShell but the data manipulation and objects that Pwsh provides it's a tough ask.

## Other Things
My [original post](https://millerb.co.uk/2019/02/28/My-Vscode-Setup.html) is still very much valid and most of the shortcuts I have copied over and work just fine.

- [git profiles](https://millerb.co.uk/2019/02/28/My-Vscode-Setup.html#git-profiles)
- [git aliases](https://millerb.co.uk/2019/02/28/My-Vscode-Setup.html#git-aliases)

Some of the items are now available by default in VSCode or in the PowerShell Extension but some of them I will never be able to live without.

I will attempt to add to this post when I make additions or substractions to my setup but this is a good start
