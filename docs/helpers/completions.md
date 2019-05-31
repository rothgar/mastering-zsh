# Completions

zsh has very comprehensive completions which is one of the main reasons to switch to zsh for your interactive shell.
Under the covers it works similarly to other shell completion engines (completions are read from files and functions on disc) but there are a few features that make it better.

> Some of the completion features have been ported to other shells and some have been stolen from other shells.
> Many of the sections below are optional to enhance your shell experience.

One nice difference is the way completions are displayed and how tab works when at a completion menu.
Here is a comparison with how `bash` and `zsh` differ.

![](../../img/bash-completion.gif)

There are lots of commands that have built in tab completion for zsh.
If a command you use doesn't support tab completion by default have a look at [zsh-users/zsh-completions](https://github.com/zsh-users/zsh-completions).
You can install it with the various zsh plugin managers or download the repo and add the src directory to your `$fpath`.

## Autosuggestions

Autosuggestions are like a preemptive tab complete for your .zhistory file.
The autosuggestion plugin will look at your current command, what commands you typically type, and suggest possibilities for you to use and save key strokes.

Installation instructions can be found in the [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions) repo.

![](../../img/autosuggestions.gif)

## Highlighting

Syntax highlighting works similarly to an IDE but at your terminal.
It can highlight everything from brackets to cursor position.

Under the covers it works like all the other things in zsh with zle widgets.
Have a look at the [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) repo for installation instructions

![](../../img/highlighting.gif)

## zstyle

List your current styles by invoking `zstyle` by itself.

The rest of this section will have to come at a later date.

---

Previous: [widgets](widgets.md) | [home](../../README.md) | Next: [line movement](../usage/line_movement.md)
