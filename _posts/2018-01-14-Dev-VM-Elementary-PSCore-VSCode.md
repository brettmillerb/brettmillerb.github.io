---
title: Dev Virtual Machine - PsCore, VSCode & ElementaryOS
summary: How to setup a dev/test VM, because why not.

excerpt_separator: <!--more-->

tags:
    - Linux
    - Virtual Machine
    - PowerShell
    - Hyper-V
---

Not a linux expert by any means

### Setting up the Virtual Machine
- Download ElementaryOS
- Configure Hyper-V Vm to use ISO
    - Disable Secure Boot
- 

### Installing Powershell Core Preview
- Download deb package from Github Releases page [here](https://github.com/PowerShell/PowerShell/releases)
    - I just used the browser to download which means it goes to `~/Downloads/`
- sudo apt-get install ./Downloads/powershell-preview_6.2.0-preview.3-1.ubuntu.14.04_amd64.deb
- Complains about dependencies
![Core Preview Dependency Error](/assets/img/Core-PreviewError.png)



### Solution

powershell-preview_6.2.0-preview.3-1.ubuntu.14.04_amd64.deb