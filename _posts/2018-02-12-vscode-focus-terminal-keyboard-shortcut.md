---
title: VSCode Focus Terminal Keyboard Shortcut
date: 2018-02-12 10:54:16

tags:
  - JSON
  - Productivity
  - VSCode
---
I am one of those people who doesn't like using the mouse or at least I try to remain using the keyboard to perform as many tasks as possible. I also have a few issues moving between the editor and the terminal in Visual Studio Code, so when I click the in the terminal it isn't always acknowledged and I end up typing over a block of highlighted text and have to Ctrl+Z my way out of it, which is just asking for trouble.

I already have focusConsoleOnExecute setting in my user settings set to `$false` so that I can run a block of code then continue typing without having to switch back to the editor.

```json
{
    "files.defaultLanguage": "powershell",
    "window.zoomLevel": -1,
    "editor.renderWhitespace": "all",
    "powershell.integratedConsole.focusConsoleOnExecute": false,
    "workbench.startupEditor": "newUntitledFile",
    "workbench.panel.location": "right",
    "terminal.integrated.cursorStyle": "line",
    "gitlens.historyExplorer.enabled": true,
    "adaptivecardviewer.enableautopreview": true,
    "bracketPairColorizer.showVerticalScopeLine": false,
    "bracketPairColorizer.showHorizontalScopeLine": false,
    "window.titleBarStyle": "custom",
    "editor.fontLigatures": true,
    "powershell.powerShellExePath": "C:\\WINDOWS\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
}
```

As of last week I starting looking at they key shortcuts to see if there was a way to quickly change focus between the editor and the terminal.

Pressing `Ctrl, Shift + P` takes you into the command palette and from there typing `keyboard` will give you the option for Keyboard Shortcuts.

The settings which control this behaviour are `Focus terminal` and `Focus Active Editor Group`. Selecting the pencil on Focus Terminal you will be prompted for a keybinding. I went with `Ctrl+I` purely because I couldn't see another conflicting one or if there was I'd never used it.

Then using the search bar at the top and searching for `Focus Active Editor Group` and setting the same keybinding

Using the command Palette again `Ctrl, Shift + P` and searching for `Open Keyboard Shortcuts File` will open the JSON file with the new Keybindings.

In order to have the focus change to editor when in the terminal, I had to add the when section to the JSON file which allows you to use the same keybinding to flick between editor and terminal.

```json
// Place your key bindings in this file to overwrite the defaults
[
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
]
```