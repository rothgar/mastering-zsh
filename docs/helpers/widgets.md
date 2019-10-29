# Widgets

Widgets are how zsh performs actions in your terminal.
Everything from moving the cursor to command completion to executing commands.

There are built in widgets for a majority of shell functionality and you can have user defined widgets to add your own functionality.
User widgets are used by binding them to keyboard shortcuts using the `bindkey` command.
More about defining widgets can be found in the zle section.

Widgets have descriptive names such as `vi-backward-kill-word` and `beginning-of-history`.
User defined widgets have no standard naming convention but you should stick with something meaningful for troubleshooting if things go wrong.

You can list all available widgets with
```bash
zle -al
```

If you want to list your current keybindings and widgets use the `bindkey` command by itself.
To see a list of bindings and a command you can run to set the key binding in your `.zshrc` file use `bindkey -L`

The widgets with `.` at the begining of the name are read-only system widgets that cannot be changed.

An example of binding a built in widget to a keyboard shortcut is

> When you bind a key to a widget any previous key binding will be overridden.

```bash
bindkey '^a' begining-of-line
```

This binds `ctrl+a` to jump to the begining of the current line at a prompt.
For a map of bindkey key representations have a look at [this table](bindkey.md)

> `bindkey` works for typing strings at the terminal too!
> Try `bindkey -s '^[s' '^Qsudo '` to enter sudo when your cursor is at a new command prompt.

A majority of widgets are included by default in zsh but additional widgets are available to load from modules.
User widgets will also need to use `autoload -U $WIDGET_NAME` and `zle -N $WIDGET_NAME` to be available for `bindkey`.

Widgets are generally broken up into different categories.
This includes
* Movement
* History Control
* Modifying Text
* Arguments
* Completion
* Miscellaneous
* Text Objects

We already have sections in this workshop for [ line movement ]( ../usage/line_movement.md ) and [ history ]( ../config/history.md ) so in this section we'll look at some widgets in other categories.

## User Widgets

Some user widgets will be included by your system in your `$fpath` and others you can declare yourself as functions in `.zshrc`.
You've already seen examples of using widgets in your `$fpath` in the history section.

The basic commands are:

```bash
autoload -U $WIDGET
zle -N $WIDGET
bindkey $KEY_SEQUENCE $WIDGET
```

`autoload -U` (`-U` is for user) will find a file in your `$fpath` named `$WIDGET` and makes it available to `zle`.
`zle -N` (`-N` is for new) will make the widget available to the command line.

`bindkey` also uses keymaps for different keyboard shortcuts but we won't worry about that here.

## Test widgets

You can also test a widget interactively at a command line.
Of course there's a widget `execute-named-cmd` for that.

To invoke a widget first find which one you want to try with `zle -la` and then enter some text on a command prompt.

Now either execute `alt+x` in emacs mode or `:` in vimcmd mode.
Type the name of the widget you want to test and press enter.

![](../../img/execute-named-cmd.gif)

## Examples

You might be thinking, what's the big deal with widgets?
They're just functions with keyboard mappings.

The best way to show their power is with some examples.

> Most of these examples use vi keymaps.
> Modify them as you wish for other keymaps.

### Prepend `sudo` to a command and put your cursor back to the previous location with `esc,s`

![](../../img/prepend-sudo.gif)

```bash
function prepend-sudo {
  if [[ $BUFFER != "sudo "* ]]; then
    BUFFER="sudo $BUFFER"; CURSOR+=5
  fi
}
zle -N prepend-sudo

bindkey -M vicmd s prepend-sudo
```

### Get output from last command with `ctrl+q,ctrl+l`

![](../../img/last-command.gif)

```bash
zmodload -i zsh/parameter

insert-last-command-output() {
  LBUFFER+="$(eval $history[$((HISTCMD-1))])"
}
zle -N insert-last-command-output

bindkey "^Q^L" insert-last-command-output
```

### Edit the current command in your `$EDITOR` with `esc,v`

![](../../img/edit-command.gif)

```bash
autoload -U edit-command-line
zle -N edit-command-line
bindkey -M vicmd v edit-command-line
```

### Tetris

Of course there are some less useful widgets available too.

```bash
autoload -Uz tetris
zle -N tetris
bindkey '^T' tetris
```

![](../../img/tetris.gif)

If you want to get really fancy you can check out [`tetriscurses`](https://github.com/zsh-users/zsh/blob/master/Functions/Misc/tetriscurses) too.

---

Previous: [functions](functions.md) | [home](../../README.md) | Next: [completions](completions.md)
