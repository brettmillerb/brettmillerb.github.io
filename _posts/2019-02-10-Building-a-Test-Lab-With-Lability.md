---
title: Building a Test Lab with Lability
summary: No test lab? No problem. Build one in minutes.

excerpt_separator: <!--more-->

tags:
    - PowerShell
    - DSC
    - Lability
---

Any system administrator that has dabbled with PowerShell will have likely started by running commands against a production Active Directory domain. This is generally because the production AD domain is all that is available to them. I have worked places where a replica test AD forest was configured but it was laborious to maintain and in most cases was more peace of mind than actually replicating production.

So you tell yourself:
> You're fine just running `Get` commands as you can't break anything

<!--more-->

Which turns into fully fledged scripts running against your production AD and as your confidence in PowerShell grows, so do your scripts.

I always reiterate that using PowerShell doesn't give you any more rights than you had before but using PowerShell allows you to break things faster than you would in the GUI.

Initial fail safes you will use are always running as a non-elevated user, or using `-WhatIf` but this relies on the commands you're running to honour both `-Credential` parameters and `ShouldProcess` which, as we all know, come back to bite you if they're not correctly implemented.

### Testing in Production - Failing Hard

We comically laugh about this in a DevOps scenario but I can pretty much guarantee that as PowerShell users we've had unanticipated results from a script in a production environment.

Mine was trying to delete disabled computer accounts from a specific OU in AD during a meeting. I ran my query:

```powershell
Get-ADComputer -filter * -SearchBase "CN=Computers,DC=millerb,DC=co,DC=uk" |
    Where-Object {$_.enabled -eq "$false"} | Remove-ADComputer
```
Imagine my surprise when I checked the OU the and all of the Enabled machines had disappeared!!!! **Ruh Oh!**

There's an apparent difference to the way boolean values are determined when you wrap them in quotes.

```powershell
PS C:\> $false -eq "$false"
False
PS C:\> "$false" -eq $true
False
PS C:\> $true -eq "$false"
True
```

### Building a Lab Quickly with Lability
Lability is a PowerShell module which allows you to build a test lab in Hyper-V by leveraging DSC and configuration files to quickly build a development virtual machine on your laptop.

#### To install:
**Lability has a requirement to be running as Administrator for configuring Hyper-V**
```powershell
Find-Module -Name Lability | Install-Module
Import-Module -Name Lability
```
#### Prepare Default Directories
```powershell
$LabHostDefaults = @{
    ConfigurationPath = 'C:\Lability\Configurations'
    HotfixPath        = 'C:\Lability\Hotfixes'
    IsoPath           = 'C:\Lability\ISOs'
}

Set-LabHostDefault @LabHostDefaults
```
Once they are defined you need to do some pre-checks to ensure it is able to build your test lab. Run with the `-Verbose` switch to see what it is doing as part of the process.
```powershell
Start-LabHostConfiguration -Verbose
```
#### Building your lab environment
When you Start your lab configuration it will attempt to download any ISO's that are required in order to do that. You can make sure that the media is available and download this in advance.
#### Show the list of available media
```powershell
Get-LabMedia

Id                               Arch Media Description
--                               ---- ----- -----------
2019_x64_Standard_EN_Eval         x64   ISO Windows Server 2019 Standard 64bit English Evaluation with Des...
2019_x64_Standard_EN_Core_Eval    x64   ISO Windows Server 2019 Standard 64bit English Evaluation
2019_x64_Datacenter_EN_Eval       x64   ISO Windows Server 2019 Datacenter 64bit English Evaluation with D...
2019_x64_Datacenter_EN_Core_Eval  x64   ISO Windows Server 2019 Datacenter Evaluation in Core mode
2016_x64_Standard_EN_Eval         x64   ISO Windows Server 2016 Standard 64bit English Evaluation
2016_x64_Standard_Core_EN_Eval    x64   ISO Windows Server 2016 Standard Core 64bit English Evaluation
```
#### Download the media required
This part takes the longest but once you have downloaded the media you can use the same media every time you start a configuration.
```powershell
Invoke-LabResourceDownload -MediaID '2019_x64_Standard_EN_Eval'
```

Once the media has finished downloading you can then start thinking about building your lab environment.

#### DSC Configurations
You will need to create a DSC Configuration defining how you want your lab environment to look. Mine is pretty basic in the below configuration and only has one server which is a Domain Controller with the necessary ADDS roles enabled with RSAT tools.

You will have two files. Your configuration file `LabBuild.ps1` and a configuration data file `configdata.psd1`. Save these files to your `C:\lability\configurations` folder you created earlier.

<script src="https://gist.github.com/brettmillerb/46888f41eedc5814d82101e539f1579c.js"></script>

If you want to build something with some additional member servers there are examples in the [Lability Github](https://github.com/VirtualEngine/Lability/tree/dev/Examples) which show you how you can define the additional information required.

```powershell
# set your location to the configurations directory
cd c:\lability\configurations

# dot source your configuration file to pull it into your current scope
. .\LabBuild.ps1

# Generate the MOF files by running the configuration, passing it the configdata
LabBuild -ConfigurationData $configdata -OutputPath C:\lability\configurations
```
You will then be asked to provide a password which will be used in your configuration for your domain Administrator account.

Now that you have generated your MOF files defining how you want your lab environment to look you just need begin building the lab environment.

```powershell
Start-LabConfiguration -ConfigurationData .\configdata.psd1
```
This will run through and provision your Hyper-V Virtual machine with the settings you defined in your configuration. Adding the relevant server roles defined and rebooting if required to finish the installation.

_Somtimes when I have logged on to the VM it is still pending a reboot to apply the changes so make sure that has done before trying to go and manually add ADDS roles in server manager._

You should then be able to Start and connect to your server

```powershell
Start-VM -Name DC01
```

You can then open Hyper-V Manager from the Start Menu and see your freshly built VM.

### Lability is as extensible as you want it to be
I have demonstrated a **very** basic lab environment that can get you up and running quickly but as Lability utilises DSC to configure your lab environment you can make it as complex as you require.

The DSC Resources you can define users, subnets, AD Sites, OU's so you could effectively have this defined in your configuration so you had some sample data when your lab started.

### No More Unanticipated Results in Production AD
Hopefully this gave you a glimpse of how easy it is to build a test Active Directory environment. This means if you don't have a test AD environment at your place of work you can rest assured that the first run isn't against real users. Hopefully this should also help you think about how your scripts can be generic so they can run anywhere, improving your scripts in the process.