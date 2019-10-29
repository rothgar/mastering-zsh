# Aliases

A good place to start is to list all of your shell aliases with the `alias` command.
When passed no parameters it will list the current aliases as defined in your configuration or explicitly defined in the shell.

## Defining Aliases

Aliases in zsh share the common `alias` builtin command from other shells.
Aliases are handy for saving time when typing commands.
If they are not "global aliases" then they only work at the beginning of a command prompt (more below).
Typical aliases are used for commands such as:

```bash
alias g=git
```

Aliases can be single commands or expand to multiple words.

```bash
alias gc='git commit'
```

If you define your alias with a space after the definition it will allow the following word to be interpreted as an alias.

```bash
# notice the space inside the quotes
alias ss='sudo '
```

This will allow you to use an alias after `ss`.

```bash
# Without the space you would receive an error that the `g` command is not found
ss g
^^ runs `sudo git`
```

If an alias conflicts with the name of a command you can escape it with quotes `''`.

```bash
alias grep='grep --color=auto'

echo foo | grep fo
         # ^^^^ uses grep --color=auto alias

echo foo | 'grep' fo
         #  ^^^^ will not colorize match
```

Aliases don't have to only be letters and numbers.

```bash
alias :q=exit
alias ..='cd ..'
alias ....='cd ../..'
alias --='cd -'
```

> You can also alias escape special characters such as `\` so be careful

## Global Aliases

zsh has an additional option for global aliases with the `-g` option which allows aliases at any part of the command line.
Without the `-g` option aliases will only be matched at the beginning of a line or after an alias ending with a space.

Global aliases can be helpful for aliasing any text you frequently write at the command line.
```bash
# pipe output to grep
alias -g G='| grep'
# pipe output to less
alias -g L='| less'
# pipe output to `wc` with option `-l`
alias -g W='| wc -l'
# convert multiline output to single line and copy it to the system clipboard
alias -g C='| tr -d ''\n'' | xclip -selection clipboard' 
```

Because global aliases can be used anywhere in the command line they can also be chained together.
```bash
echo -e 'zsh\nis\n\great\nhello\ngoodbye' G -A1 hello C
  # grep for hello and include 1 line after ^^^^^^^^^ ^
                                                      |
  # trim new lines and copy 'hello goodbye' to the ---| 
  # system clipboard
```

Aliases can also be used inside definitions of other aliases!
```bash
alias -g G='| grep'
alias -g W='| wc -l'
alias -g GfooW='G foo W'
```

## Automatically Expand Aliases

Aliases are great for typing but sometimes they can be confusing to remember what aliases you have defined or when searching your command [history](../config/history.md).
Trying to remember if you ran a command with `git` or `g` can make it hard to find the command you need.

It's also helpful to show full commands if you are pair programming or giving a presentation.

For that you can use a function that will automatically exand your aliases on your current command line after you press space.

```bash
# don't worry about zle. We'll go over it later in the zle section.
globalias() {
   zle _expand_alias
   zle expand-word
   zle self-insert
}
zle -N globalias

# space expands all aliases, including global
bindkey -M emacs " " globalias
bindkey -M viins " " globalias

# control-space to make a normal space
bindkey -M emacs "^ " magic-space
bindkey -M viins "^ " magic-space

# normal space during searches
bindkey -M isearch " " magic-space
```

![](../../img/globalias.gif)

## Advanced Aliases

Because aliases expand to any text they can get quite complex.
The expanded text can include subshells, variables, and additional quoted text.

> Advanced aliases can also be implemented as shell functions or widgets.

Here are a few examples
```bash
# Search through your command history and print the top 10 commands
alias history-stat='history 0 | awk ''{print $2}'' | sort | uniq -c | sort -n -r | head'

# Use `which` to find aliases and functions including binaries
which='(alias; declare -f) | /usr/bin/which --tty-only --read-alias --read-functions --show-tilde --show-dot'

```

# Conclusion

Aliases are great for replacing text at an interactive shell.
For more options on powering up your interactive shell check out [functions](functions.md) and [widgets](widgets.md).

---

Previous: [hooks](hooks.md) | [home](../../README.md) | Next: [variables](variables.md)
