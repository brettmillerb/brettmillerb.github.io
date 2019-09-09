---
title: Git - Remove Previously Committed Files From Tracking
summary: Accidentally Committed & Pushed Files In Your Repo?

excerpt_separator: <!--more-->

tags:
    - Git
    - PowerShell
    - Source Control
---

When working with VSCode and using the debugger VSCode automatically adds a `.vscode` folder to your repository.

```
MyAwesomeModule
    │   MyAwesomeModule.psm1
    │
    └───.vscode
            extensions.json
            launch.json
            settings.json
            tasks.json
```

<!--more-->

This however isn't always required to be committed to source control but you may not have added this to your `.gitignore` at the start of your project and now everyone that clones the repo will have your VSCode settings, not ideal if that's not what you intended.

### Solution

Create a `.gitignore` file in the root of your directory

```
MyAwesomeModule
    │   .gitignore
    │   MyAwesomeModule.psm1
    │
    └───.vscode
            extensions.json
            launch.json
            settings.json
            tasks.json
```

Add an entry `.vscode` to the gitignore file. You will see the `.vscode` folder go a darker colour in your explorer pane which means the change has been recognised and git will not track the folder.

Run the following to remove the `.vscode` folder from tracking.

```bash
git rm -r --cached .\MyAwesomeModule\.vscode
```

This will remove the folder from this point forward in the repository but it will still be visible in previous commits.

A good [StackOverflow post](https://stackoverflow.com/a/40272289/12040634) on the variations of tracking configuration files local & remote.