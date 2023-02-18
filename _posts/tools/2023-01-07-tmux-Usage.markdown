---
layout: post
title:  "tmux Usage"
categories: tools
---

This is a personal note on **tmux**. [Source documentation](https://github.com/tmux/tmux/wiki/Getting-Started).

### Create New Session
```bash
# Create a new session with default session name.
tmux new

# Create a new session named "mysession"
tmux new -smysession
```

### Status Line
`*` indicates current window; `-` indicates last window.

### Prefix Key
Any keys entered are forwarded to the program in active pane. If we want to send commands to tmux itself, a special key must be pressed first, and this is called the prefix key.

The default prefix key is `C-b`. Pressing `C-b` twice sends `C-b` to the program running in the active pane.

#### Help Keys
tmux has a short description to help remember what the key does. It shows by pressing `C-b ?`, entering **view mode**. A pane in view mode has its own key bindings, broadly following *emacs*.

### Command Prompt
tmux has its own interactive command prompt, which can be opened by `C-b :`. And commands can be typed in status line.

After executing a command in prompt, output will either be shown for a short period in the status line, or switch current pane into view mode.

By default, the command prompt uses *emacs* key bindings. (But it can also be configured to use vi key bindings)

Multiple commands can be entered together, separated by `;`. This is called a command sequence.

### Attaching and Detaching
When a client exits and detaches from outside terminal, it means detaching from tmux. tmux session will switch to background. To detach, `C-b d` is used.

The `attach-session` command attaches to an existing session. Without arguments, it attaches to the most recently used session that is not already attached:
```bash
tmux attach
```

Or `-t` gives the name of a session to attach:
```bash
tmux attach -tmysession
```

By default, attaching to a session does not detach any other clients attached to the same session. But `-d` flag can do this.

### Listing Sessions
`list-session` command (alias `ls`) shows available sessions that can be attached:
```bash
tmux ls
```

### Killing tmux Entirely
We can forcely kill tmux, by:
```bash
# in command prompt
:kill-server
```

### Creating New Windows
`C-b c` will create a new window, with an increased index.

The `new-window` command has some useful flags which can be used with command prompt:
* `-n` allows a name for the new window. For example:
```bash
:neww -nmynewwindow
```

* `-t` flag specifies a target(index) for the window.

A command to be run in the new window can be given to `new-window` in the same way as `new-session`:
```bash
# running "top" command in the new window.
:neww top
```

### Splitting the Window
`split-window` command can create a pane to split a window. Two key bindings is commonly used by default:
* `C-b %` splits the current pane into two horizontally.
* `C-b "` splits the current paen into two vertically.

`split-window` has several useful flags:
* `-h` splits horizontally; `-v` splits vertically.
* `-d` does not change the active pane to the newly created pane.
* `-f` makes a new pane spanning the whole width or height of the window instead of being constrained to the size of the pane being split.

### Changing Current Window
Key bindings to change current window in a session:
* `C-b 0` changes to window 0, `C-b 1` changes to window 1, up to `C-b 9` for window 9.
* `C-b '` prompts for a window index and changes to that one.
* `C-b n` changes to the next window.
* `C-b p` changes to the previous window.
* `C-b l` changes to the last window.

### Changing Active Pane
Key bindings to change active pane in a window:
* `C-b Up`, `C-b Down`, `C-b Left`, `C-b Right` changes pane accordingly.
* `C-b q` prints pane numbers in a short time. Pressing one of the numbers before they disappear changes the active pane to the chosen one.
* `C-b o` moves to next pane by pane number. `C-b C-o` swaps that pane with the active pane.

These use the `select-pane` and `display-pane` commands.

Pane numbers are not fixed, instead panes are numbered by their position in the window.

### Choosing Sessions, Windows and Panes
**Tree mode**: Sessions, windows and panes can be chosen under this mode.

Two ways to enter this mode: `C-b s` starts only showing sessions and with the attached session selected; `C-b w` starts with sessions expanded so windows are shown.

`Up`, `Down`, `Enter` can be used in tree mode. `Right` expands the item if possible - sessions expand to show their windows and windows expand to show their panes. `Left` collapses the item.

Items in the tree are tagged by pressing `t` and untagged by pressing `t` again. All tagged items may be untagged by pressing `T`. We can **kill** selected item by pressing `x`.

Each item in the tree has a shortcut key in brackets at the start of the line.

### Detaching Other Clients
A list of clients is available by `C-b D`. Movement is the same as tree mode, but `Enter` will detach the selected client.

### Killing a Session, Window or Pane
`C-b &`: prompts to confirmation then kills the current window. It's bound to `kill-window` command.

`C-b x`: kills only active pane. It's bound to `kill-pane` command.

The `kill-session` command kills the attached session and all its windows.

### Renaming Sessions and Windows
`C-b $` will prompt for a new name for the attached session. It's bound to `rename-session` command.

`C-b ,` prompts for a new name for the current window, using `rename-window` command.

### Swapping and Moving
tmux allows panes and windows to be swapped with the `swap-pane` and `swap-window` commands.

A single pane can be marked with `C-b m` key binding. `C-b M` clears the marked pane entirely so that no pane is marked.

Once a pane is marked, it can be swapped with the active pane in current window with the `swap-pane` command, or the window containing the marked pane can be swapped with the current window using the `swap-window` command.

Panes can be swapped with the pane above or below using the `C-b {` and `C-b }` key bindings.

Moving windows uses the `move-window` command or the `C-b .` key binding. It prompts for a new index for the current window.

If there are gaps in the window list, the indexes can be renumbered with the `-r` flag to `move-window`.

### Resizing and Zooming Panes
Panes may be resized in small steps with `C-b C-Left`, or right, up, down, and in larger steps with `C-b M-Left`, or right, up, down. These use the `resize-pane` command.

A single pane may be temporarily made to take up the whole window with `C-b z`, hiding all other panes. Pressing `C-b z` again restores the layout. This is called zooming and unzooming. A window where a pane has been zoomed is marked with a `Z` in the status line.

### Copy and Paste
tmux has its own copy and paste system. Text is copied using **copy mode**, entered with `C-b [`, and the most recently copied text is pasted into active pane with `C-b ]`.

Paste buffers can be given names.

Copy mode freezes any output in a pane and allows text to be copied. View mode is a read-only form of copy mode.

### Using the Mouse
tmux supports mouse operations. It's enabled with the `mouse` option in the configuration file. `set-option` command is used to turn the mouse on:
```bash
:set -g mouse on
:set -g mouse off
```

### Enter View Mode
Use shortcut `C-b [` to enter view mode.

### Configuring tmux

#### The Configuration File
When tmux server is started, tmux runs a file called `.tmux.conf` in the user's home directory. It contains a list of tmux commands which are executed in order. It's only run when server is started, not when a new session is created.

A different configuration file can be run from `.tmux.conf` or from a running tmux server using the `source-file` command. For example:
```bash
:source-file ~/.tmux.conf
```

#### Key Bindings
tmux key bindings are changed using the `bind-key` and `unbind-key` commands. Each key binding in tmux belongs to a named key table. There are four: `root`, `prefix`, `copy-mode`, `copy-mode-vi`.

All the key bindings for a single table can be listed with the `list-keys` command. This shows the keys as a series of `bind-key` commands. The `-T` flag gives the key table to show.

`bind-key` commands can be used to set a key binding, either interactively or most commonly frome the configuration file. It also has a `-T` flag to specify the target key table.

`bind-key` will replace any existing key binding.

#### Type of Option
tmux is configured by setting options. There are several types of options:
* Server options which affects the entire server.
* Session options.
* Window options.
* Pane options.
* User options which are not used by tmux, but reserved for the user.

#### Showing Options
Options are displayed using `show-options` command. The `-g` flags shows global session options. `-s` shows server options.
```bash
tmux show -g
```

#### Changing Options
Options are set and unset using the `set-option` command. `-g` is necessary to set global session or window options.

To set the `status` option:
```bash
set -g status off
```

The `-u` flag unsets an option. Unsetting a global option restores it to its default value, for example:
```bash
set -gu status
```


