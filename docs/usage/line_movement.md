# Line Movement

Effectively moving around on the command line might be the single biggest time saver.
Most of the time you're typing but you'll also be doing a fair deal of line editing and movement.

If you're using arrows to move back and forth you can save a lot of time learning some basic line movement keyboard shortcuts.

Your line movement will depend on your loaded keymaps and the widgets you have bound to different keys.
More information about custom widgets can be found in the zle section.

You can manually set your keymap mode with

```bash
# emacs mode
bindkey -e

# vi mode
bindkey -v
```

For the examples below I'll be using vi mode for movement.

> If your `$EDITOR` or `$VISUAL` environment variables start with `vi` when the shell starts your keymap mode is automatically set to vi

Keybindings have special characters to represent them.
More info can be found in [bindkey](bindkey.md).

For reference:

* `^`  = Ctrl
* `^[` = Alt
* `\e` or `\E` = Escape

If you want to figure out how a key sequence should be represented you can try `xxd` or `echo " $keypress"`.
The key letter you press is not case sensitive.
So `^a` is the same as `^A`.

## Basic movement

I may like vi mode for my keymap but I'm not a monster.
Let's set the emacs begining and end of line keyboard shortcuts and reverse search.

> `bindkey` works by default on your main keymap.
> In vi mode that is `viins` so these keybindings work when typing but not in `vimcmd` mode.
> To explicitly set a keymap mode use `bindkey -M $mode`.

```bash
bindkey '^r' history-incremental-search-backward
bindkey '^a' beginning-of-line
bindkey '^e' end-of-line
# Also fix annoying vi backspace
bindkey '^?' backward-delete-char
```

Now I have four ways to get to the begining and end of a command line.
I can press `Escape`,`^` or `Ctrl`+`a` for the begining of the line, and `Escape`,`$` or `Ctrl`+`e` for the end of the line.

> With vi mode you have to press escape to enter vicmd mode to enter standard vi commands.
> The `^` and `$` are standard vi commands for beginning and end of a line.

We also probably want some other useful keyboard shortcuts so we don't have to go back and forth between (vicmd) and vi-insert (viins) modes.
I personally like these.

```bash
bindkey '^[b' vi-backward-blank-word
bindkey '^[w' vi-forward-blank-word
```

To find widgets you can use, remember to use `zle -la` to list all widgets and `alt+x` or `esc,:` to try the widget with `execute-named-cmd`.

## Modifying Text

Remember, you can use any emacs or vi commands depending on what keymap you're using.
You don't have to bind keys to use use them.

If you want some additional functionallity check out the examples below.

In general you'll be binding widgets to keyboard shortcuts.
There are many `*-kill-*` widgets available that will let you kill words, lines, or letters.

If you don't want to feel like a murderer you can also try the `*-yank-*` and `*-push-*` widgets too.

### Yank current command and paste

For example, let's say you have a long command line typed up but you forgot you needed to run a command first or look up some information.
Normally you would push `ctrl+c` to cancel the command, run your command, and then use your mouse to select, copy, and paste the long command to the next prompt.
Or maybe you open a new terminal, run your command, and come back.

What if I told you there was a better way?
Check out the `push-input` widget.

Let's bind it to `ctrl+q` and see what it does.

![](../../img/push-input.gif)

You can also use `yank-pop` widget to keep a ring of yanked commands you can cycle through.

```bash
# re-sets yank and kill shortcuts to bash/emacs mode
bindkey -M viins '^P' push-line-or-edit
bindkey -M viins '^Y' yank
# in order to use the below you must first yank without hitting enter
# subsequent alt-y combos will cycle through
# kill ring
bindkey -M viins '\ey' yank-pop
bindkey -M viins '^U' backward-kill-line
```

### Matching deliminators

There's a handy plugin called [autopair](https://github.com/hlissner/zsh-autopair) that can automatically match your brakets and quotes in your terminal.
It works similarly to an IDE and loads an autopair-insert, autopair-close, and autopair-delete widget.

![](../../img/autopair.gif)

There's also an included [`surround`](https://github.com/zsh-users/zsh/blob/master/Functions/Zle/surround) widget but I had difficulty using it. YMMV.

---

[home](../../README.md)
