---
title: Github authentication issues with VSCode?
date: 2018-03-13 12:13:22

tags:
  - Git
  - Powershell
  - VSCode
---
At the end of February Github finally pulled the plug on deprecating weak cryptographic protocols.

<https://githubengineering.com/crypto-deprecation-notice/>

I noticed this when I tried to push an update to Github from a local branch for my MSTeams Webhook Module and I was prompted for my Github credentials which I've not had to do since setting this up.

There are a few articles showing you how to fix the issue and you can see which protocol you are currently using by running the following:

```powershell
[System.Net.ServicePointManager]::SecurityProtocol
Ssl3, Tls
```

You can change this by directly changing the protocol used:

`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`

The only downside to this is that you have to set this in every Powershell session so you can simply add this to your profile:

**In Powershell Console:**

`notepad $profile`

**In VSCode:**

`psedit $profile`

**Add:**

`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`

Save and restart your Powershell session.

Running it again shows you it's no longer using SSL3

```powershell
[System.Net.ServicePointManager]::SecurityProtocol
Tls12
```
If you don't want to set this for every session then you can enable the stronger cryptographic protocols for all .NET applications on your system with the following which I found [on John Louros' blog](https://www.johnlouros.com/blog/enabling-strong-cryptography-for-all-dot-net-applications).

```powershell
#set strong cryptography on 64 bit .Net Framework (version 4 and above)
Set-ItemProperty -Path 'HKLM:\SOFTWARE\Wow6432Node\Microsoft\.NetFramework\v4.0.30319' -Name 'SchUseStrongCrypto' -Value '1' -Type DWord

# set strong cryptography on 32 bit .Net Framework (version 4 and above)
Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\.NetFramework\v4.0.30319' -Name 'SchUseStrongCrypto' -Value '1' -Type DWord
```
Checking the value now:

```powershell
[System.Net.ServicePointManager]::SecurityProtocol
Tls, Tls11, Tls12
```

**Caveat: This may have some adverse effects on other systems so I'd be cautious with setting this.**