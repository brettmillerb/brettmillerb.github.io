---
title: VSCode Focus Terminal Keyboard Shortcut
date: 2018-02-12 10:54:16

tags:
  - JSON
  - Productivity
  - VSCode
---
I am one of those people who doesn&#8217;t like using the mouse or at least I try to remain using the keyboard to perform as many tasks as possible. I also have a few issues moving between the editor and the terminal in Visual Studio Code, so when I click the in the terminal it isn&#8217;t always acknowledged and I end up typing over a block of highlighted text and have to Ctrl+Z my way out of it, which is just asking for trouble.

I already have focusConsoleOnExecute setting in my user settings set to `$false` so that I can run a block of code then continue typing without having to switch back to the editor.

<img class="aligncenter size-full wp-image-218" src="http://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-18-26.png" alt="" width="439" height="173" srcset="https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-18-26-300x118.png 300w, https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-18-26.png 439w" sizes="(max-width: 439px) 100vw, 439px" />

As of last week I starting looking at they key shortcuts to see if there was a way to quickly change focus between the editor and the terminal.

Pressing `Ctrl, Shift + P` takes you into the command palette and from there typing `keyboard` will give you the option for Keyboard Shortcuts.

The settings which control this behaviour are `Focus terminal` and `Focus Active Editor Group`. Selecting the pencil on Focus Terminal you will be prompted for a keybinding. I went with `Ctrl+I` purely because I couldn&#8217;t see another conflicting one or if there was I&#8217;d never used it.

<img class="aligncenter size-full wp-image-219" src="http://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-24-15.png" alt="" width="704" height="25" srcset="https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-24-15-300x11.png 300w, https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-24-15.png 704w" sizes="(max-width: 704px) 100vw, 704px" />

Then using the search bar at the top and searching for `Focus Active Editor Group` and setting the same keybinding

<img class="aligncenter size-full wp-image-221" src="http://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-43-10.png" alt="" width="666" height="25" srcset="https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-43-10-300x11.png 300w, https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-43-10.png 666w" sizes="(max-width: 666px) 100vw, 666px" />

Using the command Palette again `Ctrl, Shift + P` and searching for `Open Keyboard Shortcuts File` will open the JSON file with the new Keybindings.

<img class="aligncenter size-full wp-image-222" src="http://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-46-01.png" alt="" width="442" height="199" srcset="https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-46-01-300x135.png 300w, https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-46-01.png 442w" sizes="(max-width: 442px) 100vw, 442px" />

In order to have the focus change to editor when in the terminal, I had to add the when section to the JSON file which allows you to use the same keybinding to flick between editor and terminal.

<img class="aligncenter size-full wp-image-225" src="http://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-51-01.png" alt="" width="438" height="215" srcset="https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-51-01-300x147.png 300w, https://millerb.co.uk/wp-content/uploads/2018/02/2018-02-12_10-51-01.png 438w" sizes="(max-width: 438px) 100vw, 438px" />

<pre class="prettyprint lang-json" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">[
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
]</pre>