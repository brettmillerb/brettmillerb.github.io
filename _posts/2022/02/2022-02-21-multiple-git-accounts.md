---
title: Multiple GitHub Account Management
summary: Managing multiple configs getting you down?

excerpt_separator: <!--more-->

tags:
    - git
    - GitHub
    - Productivity
---

If you have multiple github accounts then you will very quickly become aware of an annoyance of having to manage your git config for each repository. If you don't want to commit as the account configured in your global `.gitconfig` then you have to remember to set your username and email on each repository that you clone or create.

If you don't become aware of this then you will quickly be searching StackOverflow for how to amend the last commit author.

<!--more-->

### Use a personal gitconfig
The way I have been managing this is by using the `includeIf` section within my `.gitconfig` file and if the path of the repository is in my personal projects directory to include an alternative gitconfig.

```bash
# If Path is personal projects then use personal gitconfig
# The /i makes the path case insensitive
[includeIf "gitdir/i:C://git/personal/"]
	path = ~/.gitconfig-personal
```
This tells git that if you’re directory is in the path defined in gitdir then to use the .gitconfig-personal file instead of the usual config file.

In your `.gitconfig-personal` folder add your personal details.
```bash
[user]
    name = Brett Miller
    email = brett@millerb.co.uk
```

### The Downside
Using this method does not work so well if, like me, you use different SSH keys for accessing the two accounts then you also need to use an ssh config and when you clone a repository you have to change the URL e.g. when I clone a personal repository I have to change the remote URL to `personal.github.com`.

```bash
❯❯ cat ~/.ssh/config

# Github Personal account
  Host personal.github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_personal

# Github Work account
  Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_work
```

### An Even Easier Way
So I stumbled upon a blog post a little while ago which showed an easier way to do this resulting in no longer requiring a different gitconfig for personal configuration. I have just changed workstations so decided that I would configure this properly.

After searching for about an hour for the blog post I finally [found it](https://dev.to/arnellebalane/setting-up-multiple-github-accounts-the-nicer-way-1m5m) or at least something that covered what I was looking for).

This uses the same `includeIf` that I was using before but makes it even easier!

```bash
❯ cat ~/.gitconfig
[user]
    name = Brett Miller
    email = brett.miller@work.com

[core]
    sshCommand = "ssh -i ~/.ssh/id_rsa_work"

[init]
    defaultBranch = main

[includeIf "gitdir/i:C:/git/personal/"]
    [user]
        email = brett.miller@millerb.co.uk

    [core]
        sshCommand = "ssh -i ~/.ssh/id_personal"
```

This uses the `core.sshCommand` setting to specify my personal SSH key if I am within a git project in my personal directory.

### Summary
I used to use SSH authentication for work and HTTPS for personal projects. When I changed laptops a while ago I switched to SSH for both accounts. Now that GitHub have deprecated the use of basic authentication for HTTPS auth, it makes SSH more appealing

Really pleased with this configuration as it has eliminated the need for an ssh config, a secondary gitconfig, I no longer have to change the remote repository URL or set user and email per repostory.

Now to figure out GPG signing on a per account basis 🙌