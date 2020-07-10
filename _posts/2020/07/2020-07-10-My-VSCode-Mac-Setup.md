---
title: My MacOS VSCode Setup
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

I am now finding that I am using zsh as a primary shell over PowerShell for day to day interaction and primarily interacting with git. I also tend to stick to bash for Azure CLI too for the tab completion and speed it offers.

![iterm2 bash profile](https://user-images.githubusercontent.com/24279339/87206659-29642000-c302-11ea-81f1-4eecd6df2da2.png)

I have added this [as a gist](https://gist.github.com/brettmillerb/5f9197ce3568b230f52c9641d92f99bb) so I don't make this post longer than it needs to be.

### Powershell Profile

This has changed a little since I moved. Mainly to accommodate differences between bash and PowerShell on Mac so that they work in a consistent manner.

My Toolbox module that I import in my profile can be found [here](https://github.com/brettmillerb/Toolbox) in my github repos.

```powershell
# Module with some helper functions
Import-Module -Name Toolbox

# functions to be aliased to make traversing directories consistent with zsh shortcuts
function BackOne {
    Set-Location ..
}
function BackTwo {
    Set-Location ../..
}

New-Alias -Name code -Value 'code-insiders'
New-Alias -Name '..' -Value 'BackOne'
New-Alias -Name '...' -Value 'BackTwo'
# clip on windows is clip.exe and I use this a lot.
New-Alias -Name clip -Value Set-Clipboard

Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward

# Menu Complete for tab completion is really nice and gives you parameter information during selection.
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete
Set-PSReadLineOption -HistorySearchCursorMovesToEnd:$true
```

I have added a gist of my entire profile [here](https://gist.github.com/brettmillerb/467e4ee2d7f8e97897f106a5534f884b) so this post doesn't get too long.

![Pwsh prompt MacOS](https://user-images.githubusercontent.com/24279339/87207759-a55f6780-c304-11ea-8c24-abdba079b18c.png)

## Remove Development Extension
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
