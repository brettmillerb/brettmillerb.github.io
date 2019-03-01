---
title: Setting up VsCode For Productivity
summary: My VsCode Setup and Customisations

excerpt_separator: <!--more-->

tags:
    - Powershell
    - Visual Studio Code
    - Productivity
---
### WARNING! - LONG POST ALERT
{: .no_toc}

Visual Studio code has quickly become the tool of choice amongst developers and system admins due to it's flexibility of dealing with most scripting/programming languages you can throw at it. The fact that it is cross platform and is now being actively developed in preference to Powershell ISE means that it's hard to ignore such a brilliant editor.

I have posted a few things on Twitter in the past showing some tips or techniques and they always seem to be very well received so I thought I would outline some of the more detailed things I use which benefit me when writing Powershell.

<!--more-->

* TOC
{:toc}

## Profile
I personally set `$Profile.CurrentUserAllHosts` rather than the usual `$Profile` as this means I can use the same profile across my normal Windows Powershell Console Host as well as Visual Studio Code. This doesn't however let me use the same profile across Windows Powershell and Pwsh as they reside in different folders.

```powershell
# Windows PowerShell Profile
C:\Users\brett.miller\OneDrive - Company Ltd\Documents\WindowsPowerShell\profile.ps1

# PowerShell Core Profile
C:\Users\brett.miller\OneDrive - Company Ltd\Documents\PowerShell\profile.ps1
```

#### My profile (Broken Down)
I define some regularly visited folders as functions then alias them so that I can quickly navigate to the folders I use most without having to use `cd`

```powershell
function Open-Here { explorer $pwd }
function Set-SupportPath { Set-Location C:\support }
function Set-DocsPath { Set-Location $env:OneDrive\documents }
function Set-HomePath { Set-Location $home }
function Set-GitPath { Set-Location C:\support\git }

New-Alias -Name op -value Open-Here
New-Alias -Name support -Value Set-SupportPath
New-Alias -Name docs -value Set-DocsPath
New-Alias -Name home -Value Set-HomePath
New-Alias -Name GitPath -Value Set-GitPath
```
#### Functions (mainly used interactively)
`Syntax` is basically a wrapper for Get-Command with the `-Syntax` parameter but can print the syntax down the screen.

It's quicker to type than `Get-Command Get-ChildItem -Syntax` and easier to read and determine mandatory parameters in specific parameter sets when printed vertically.

```powershell
function Syntax {
    [CmdletBinding()]
    param (
        $Command,

        [switch]
        $PrettySyntax
    )

    $check = Get-Command -Name $Command

    $params = @{
        Name =  if ($check.CommandType -eq 'Alias') {
                    Get-Command -Name $check.Definition
                }
                else {
                    $Command
                }
        Syntax = $true
    }
    if ($PrettySyntax) {
        (Get-Command @params) -replace '(\s(?=\[)|\s(?=-))', "`r`n "
    }
    else {
        Get-Command @params
    }
}
```
```powershell
c:\support> Syntax Get-ChildItem -Pretty Syntax

Get-ChildItem
 [[-Path] <string[]>]
 [[-Filter] <string>]
 [-Include <string[]>]
 [-Exclude <string[]>]
 [-Recurse]
 [-Depth <uint>]
 [-Force]
 [-Name]
 [-Attributes <FlagsExpression[FileAttributes]>]
 [-FollowSymlink]
 [-Directory]
 [-File]
 [-Hidden]
 [-ReadOnly]
 [-System]
 [<CommonParameters>]

Get-ChildItem
 [[-Filter] <string>]
 -LiteralPath <string[]>
 [-Include <string[]>]
 [-Exclude <string[]>]
 [-Recurse]
 [-Depth <uint>]
 [-Force]
 [-Name]
 [-Attributes <FlagsExpression[FileAttributes]>]
 [-FollowSymlink]
 [-Directory]
 [-File]
 [-Hidden]
 [-ReadOnly]
 [-System]
 [<CommonParameters>]
```

`Git-Whoami` is a quick check to see if my git config is being applied correctly so I don't commit as the wrong user when working in different Github projects. This has mostly been replaced by my gitconfig which I will cover further down this post.
```powershell
function Git-Whoami {
    $author = git config user.name
    $email = git config user.email
    
    [pscustomobject]@{
        Author = $author
        Email = $email
    }
}
```
```powershell
c:\support> Git-Whoami

Author       Email
------       -----
Brett Miller brett@millerb.co.uk
```

`Sort-Reverse` I stole from [@iisresetme](https://twitter.com/iisresetme)
```powershell
function Sort-Reverse {
    $rank = [int]::MaxValue
    $input | Sort-Object {(--(Get-Variable rank -Scope 1).Value)}
}
```
You can see it in action in his tweet

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">This incidentally made me think about the simplest pipeline-friendly reverse-sort in <a href="https://twitter.com/hashtag/PowerShell?src=hash&amp;ref_src=twsrc%5Etfw">#PowerShell</a> without resorting to [array]::Reverse() or similar:<br><br>function Sort-Reverse {<br>  <a href="https://twitter.com/search?q=%24rank&amp;src=ctag&amp;ref_src=twsrc%5Etfw">$rank</a> = [int]::MaxValue<br>  <a href="https://twitter.com/search?q=%24input&amp;src=ctag&amp;ref_src=twsrc%5Etfw">$input</a> |Sort-Object {(--(Get-Variable rank -Scope 1).Value)}<br>} <a href="https://t.co/ve9Hm3Hsif">pic.twitter.com/ve9Hm3Hsif</a></p>&mdash; Mathias Jessen (@IISResetMe) <a href="https://twitter.com/IISResetMe/status/973927887232491520?ref_src=twsrc%5Etfw">March 14, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

#### My Powershell Prompt
I like quite a clean prompt so don't bother with [Posh-Git](https://www.powershellgallery.com/packages/posh-git/0.7.1) or [PowerLine](https://www.powershellgallery.com/packages/PowerLine/3.0.3) so I tend to find I use `git status` alot more than if I used the prompts. I may revist this again soon as it gets annoying having to constantly check which branch you're on or if there are changes to commit/push.

My Prompt basically:
- Gives me the time the command was run
- Abbreviates the `$pwd` to the `Parent\Current`

```powershell
# Custom prompt function
function global:prompt {
    $global:promptDateTime = [datetime]::Now
    $Global:promptDate = $global:promptDateTime.ToString("yyyy/MM/dd")
    $Global:promptTime = $global:promptDateTime.ToLongTimeString()
    $global:promptPath = $pwd.ToString().split('\')[-2..-1] -join '\'

    Write-Host -Object ("[{0} {1}]" -f $global:promptDate, $global:PromptTime) -ForegroundColor Green -NoNewline
    (" {0}> " -f $global:promptPath)
}
```
![PowershellPrompt](/assets/img/powershellprompt.png)

#### Argument Completer(s)
I host our own Package Repository internally so to allow tab completion on the repositories I retreive the current repositories as an argument completer to save me having to type them out.
```powershell
# Register Argument completer for Internal PSGallery tab completion
Register-ArgumentCompleter -CommandName Find-Module -ParameterName Repository -ScriptBlock {
    Get-PSRepository | Select-Object -ExpandProperty Name | foreach-object {
        [System.Management.Automation.CompletionResult]::new(
            $_
        )
    }
}
```

#### $PsDefaultParameterValues
As an extension to the above I always want to use our internal PSGallery so that required packages are cached to save having to go out to the internet every time I want to install something.
```powershell

$PSDefaultParameterValues = @{
    "Find-Module:Repository" = "PsGallery Internal"
}
```
You can see the `$profile` in it's entirety [as a gist here](https://gist.github.com/brettmillerb/e04778bb46e79217ad508d7c2272658a)

## Snippets

Snippets are really handy and have been about for years but surprising how many people don't know about them.

#### Powershell ISE
Press `Ctrl + J` to see the snippets.

![Powershell ISE Snippets](/assets/img/powershell_ise_kJM55YevPQ.png)

#### Visual Studio Code

Type `F1` or `Ctrl+Shift+P` to open the command palette followed by `snippets` and it will open the snippets for the language of the currently open file in the editor.

![VsCode Snippets](/assets/img/Code_ISn7J9k2TX.png)

Alternatively you can just start typing the name of one of the snippets and intellisense will show you the available snippets which you can select and it will insert the snippet for you to use.

![VsCode Snippets2](/assets/img/CmlAdOFwaf.gif)

But by far the best bit about the snippets in both ISE and VsCode are that they are extenisble and you can create your own snippets for things that you type or use regularly saving you a ton of time. You can use tab stops so that you can quickly fill out the sections you require and tab to the next section saving time on clicking or cursoring.

I won't post all of my snippets here as they are largely unreadable to humans but they are basically JSON which is interpreted by VsCode.

#### Calculated Property

![Calculated Property Snippets](/assets/img/0iM8VmZU15.gif)

#### Basic Parameter

![Basic Param Snippets](/assets/img/xbYscFqqlO.gif)

#### Pester Snippets

![Pester Snippets](/assets/img/my9x2m8eyI.gif)

You get the idea with the examples above and and there is a [community snippets project on Github](https://github.com/PowerShell/vscode-powershell/blob/master/docs/community_snippets.md) which allows people to share and upload there own snippets that they use which others may find useful.

## Keyboard Shortcuts

There are literally hundreds of keyboard shortcuts in VsCode and learning them all is a near impossibility but in order to allow myself to become really productive I have learned alot of the really useful keyboard shortcuts to ensure I don't have to constantly focus shift to using my mouse when writing code. You can get at the keyboard shortcuts from the command pallette via `F1` or `Ctrl+Shift+P` then typing keyboard.

When you add custom keybindings they are stored in the `keybindings.json`.

#### Switch Editor/Terminal Focus
This allows me to quickly jump between the PowerShell Integrated Console and the Currently open Editor. Coupled with the `Ctrl+PageUp/Down` and `Ctrl+1/2/3/4` to switch between editor panes and tabs within focused editors can save a load of time.

```json
{
        "key": "ctrl+i",
        "command": "workbench.action.terminal.focus",
        "when": "editorFocus"
    },
    {
        "key": "ctrl+i",
        "command": "workbench.action.focusActiveEditorGroup",
        "when": "terminalFocus"
    }
```

#### Toggle Panel/Terminal
As I generally work from an external keyboard I have configured shortcuts using the numpad to quickly toggle the PowerShell Integrated Terminal in various ways. Handy when you've got large amounts of output or multiple editors open/tiled.

```json
{
        "key": "ctrl+numpad1",
        "command": "workbench.action.togglePanel"
    },
    {
        "key": "ctrl+numpad2",
        "command": "workbench.action.togglePanelPosition"
    },
    {
        "key": "ctrl+numpad3",
        "command": "workbench.action.toggleMaximizedPanel"
    },
    {
        "key": "ctrl+numpad4",
        "command": "workbench.action.toggleCenteredLayout"
    },
```
![Panel Shortcuts](/assets/img/KEuANth1IF.gif)

## Extensions
I won't go into a load of detail on the Extensions as they are pretty well known but they make the coding experience in Visual Studio Code so easy.

- [Bracket Pair Colorizer 2](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)
    - Colour code your opening/closing brackets to quickly spot potential issues.
- [Live HTML Previewer](https://marketplace.visualstudio.com/items?itemName=hdg.live-html-previewer)
    - Preview your html code in a preview pane, live editing too which is awesome.
- [PowerShell Preview](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell-Preview)
    - The preview version of the normal extension. Comes with PSReadLine so a must have for anyone!
- [VS Live Share](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare)
    - Share your workspace with colleagues/strangers on the internet and code together.
- [Indent Rainbow](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)
    - Highlights the gutter of your code so you can quickly see indentation descrepencies.
- [VSCode-Icons](https://marketplace.visualstudio.com/items?itemName=robertohuertasm.vscode-icons)
    - Nicer icons than the standard ones so easier to distinguish files/folders.
- [Settings Sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)
    - Sync your settings across devices stable/insiders, uses a github gist to store your settings.
- [Lorem Ipsum](https://marketplace.visualstudio.com/items?itemName=Tyriar.lorem-ipsum)
    - Lorum Ipsum generator for quickly filling blank space for throwing together POC demos.

## Essential Modules
#### EditorServicesCommandSuite
EditorServicesCommandSuite is a Powershell Module which plugs into VsCode and offers additional powershell commands when writing scripts. Written by [Patrick Meinecke (@SeeminglyScienc)](https://twitter.com/SeeminglyScienc)

One of the best functions in the module is `ConvertTo-SplatExpression` which takes a really long command and parameters and converts it to a splat for easier reading and most importantly no bloody backticks.

See it in action in my tweet and this Twitter thread:

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">The main thing that would cause longer lines is commands with lots of parameters where I&#39;d encourage splatting. And with <a href="https://twitter.com/SeeminglyScienc?ref_src=twsrc%5Etfw">@SeeminglyScienc</a> EditorServicesCommandSuite module for <a href="https://twitter.com/code?ref_src=twsrc%5Etfw">@code</a> there&#39;s no reason not to convert to splatting. <a href="https://t.co/qO5zzs6JdJ">pic.twitter.com/qO5zzs6JdJ</a></p>&mdash; Brett Miller (@BrettMiller_IT) <a href="https://twitter.com/BrettMiller_IT/status/1094233946697723906?ref_src=twsrc%5Etfw">February 9, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

This entire module is gold so make sure you check it out.

#### Indented.Net.IP
Chris Dent's module on doing Networking related maths. It's sublime for quickly working out CIDR notations, Subnet ranges and literally everything else you could possibly need in your console.

Available on the Powershell Gallery `Find-Module Indented.Net.Ip | Install-Module -Scope CurrentUser`

There are other modules that I consider essential but aren't as generic as these two. I may expand on this in the future.

## Quick Shortcuts
I thought I would highlight a few other things that I use/do to make my life easier when sitting writing PowerShell as not everyone is fully aware of them.

#### Quickly open a file in your Editor
`PsEdit c:\support\script.ps1` - Quickly open files in Editor

#### Transform selection to upper/lower case
![transform Case](/assets/img/vg6tadTAnb.gif)

## Git

#### Git Profiles
Like many others I have a personal Github profile but we also use github internally at work with a separate organisation account so you're constantly paranoid you're going to commit to a repository with the wrong git username defined.

I was discussing the best way to handle this as I ended up having to re-write a commit after forgetting to set my git config on a personal repo.

[Nate Ferrell(@scrthq)](https://twitter.com/scrthq) came to the rescue with a conditional workaround for your git config and I'm surprised this isn't more common because I had already searched for conditional logic in git config but turned up nothing.


Create a copy of your `.gitconfig` file generally in `$home` and call it something like `.gitconfig-personal`

```powershell
    Directory: C:\Users\brett.miller

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        29/01/2019    11:21            405 .gitconfig
-a----        28/01/2019    10:49            325 .gitconfig-personal
```
Open your normal `.gitconfig` file in VsCode with `PSedit .gitconfig` and add the following

```bash
[includeIf "gitdir:C:/support/git/gitpersonal/"]
	path = ~/.gitconfig-personal
```

This tells git that if you're directory is in the path defined in `gitdir` then to use the `.gitconfig-personal` file instead of the usual config file.

In your `.gitconfig-personal` folder add your personal details.
```bash
[user]
    name = Brett Miller
    email = brett@millerb.co.uk
```
Then all you have to do is make sure that your personal git projects are in the `gitpersonal` folder and you will always use your personal details in commits.

#### Git Aliases
I haven't really ventured into git aliases very much but the one I do use makes me want to use them a little bit more as some commands can be quite tedious.

```bash
git config --global alias.logline "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```
So perrrty!

![git logline](/assets/img/gitlogline.png)

## Other Things
#### Firefox Keyword Bookmarks
This is such a handy tip if you use specific sites to search for information quite frequently.

Create a new bookmark for a site you wish to search and give it a meaningful name, the location to the website you wish to search but replace the search item with `%s` and a keyword which is what you will prefix your address bar search with

```yaml
Name:       [Keyword] Powershell Gists
Location:   "https://gist.github.com/search?utf8=%E2%9C%93&q=language%3APowerShell+%s"
Keyword:    gist
```

This then allows you to search the site specified in the location with the search term defined at runtime.

`gist msteams` will take you to [https://gist.github.com/search?utf8=%E2%9C%93&q=language%3APowerShell+msteams](https://gist.github.com/search?utf8=%E2%9C%93&q=language%3APowerShell+msteams)


`gh msteams` will take you to [https://github.com/search?q=language%3Apowershell+msteams](https://github.com/search?q=language%3Apowershell+msteams)