# History

If you work in the shell long enough your history will become your own personal reference.
It's like automatic documentation which is the best kind of documentation.

## Configuration

Unfortunately, the default configuration for history is not always great.
You should already be familiar with variables such as `$HISTFILE` and `$HISTSIZE`.

Here are a few shell options that can improve your history recording and usage.
Drop this config in your `.zshrc` file to improve history recording.

```
setopt EXTENDED_HISTORY          # Write the history file in the ':start:elapsed;command' format.
setopt INC_APPEND_HISTORY        # Write to the history file immediately, not when the shell exits.
setopt SHARE_HISTORY             # Share history between all sessions.
setopt HIST_EXPIRE_DUPS_FIRST    # Expire a duplicate event first when trimming history.
setopt HIST_IGNORE_DUPS          # Do not record an event that was just recorded again.
setopt HIST_IGNORE_ALL_DUPS      # Delete an old recorded event if a new event is a duplicate.
setopt HIST_FIND_NO_DUPS         # Do not display a previously found event.
setopt HIST_IGNORE_SPACE         # Do not record an event starting with a space.
setopt HIST_SAVE_NO_DUPS         # Do not write a duplicate event to the history file.
setopt HIST_VERIFY               # Do not execute immediately upon history expansion.
setopt APPEND_HISTORY            # append to history file
setopt HIST_NO_STORE             # Don't store history commands
```

## Using Words from the Last Command

For most people working with history means using `sudo !!` or occationally `ctrl+r` or `history | grep`.
To master working with your history we should look at a few other commonly used shortcuts instead.
First of all, you should know `!!` is essentially the same as
```bash
# Note: this doesn't actually work but the fc command is effectively the same
alias !!='fc -ln -1'
```
_More on `fc` later_

But there are lots of other aliases that start with `!`.
Here are some.

* `!*` or `!:*` = All **arguments** (minus command)
* `!$` or `!:$` = Last argument/word
* `!^` or `!:1` = First argument (doesn't include command name)
* `!foo` = The last command that started with `foo`

If you want to use words from commands earlier in your history you can also use the `!-n` format.

* `!-3:$` = Use last word from 3 commands ago
* `!-2:*` = Use all arguments from 2 commands ago
* `!-2; !-1` = Combine last two commands into a single line

You can also make this better with `bindkey` and the insert-last-word zle widget.
More on what this means can be found in the [widgets section](../helpers/widgets.md).

```bash
# press Alt+. to insert the last word from the previous command
autoload -U smart-insert-last-word
zle -N smart-insert-last-word
bindkey "\e." smart-insert-last-word
```

## Modifying the Last Command

It's great to be able to use parts from the last command, but what if you want to rerun the last command with edits?
You have a few options.

```bash
echo foo foo
# using the above style !! we can do a single substitution with
!!:s/foo/bzz    # bzz foo
# global substitutions need g
echo foo foo
!!:sg/foo/bzz   # bzz bzz
# be careful though because global replace happen for the entire line
echo foo foo
!!:sg/o/z       # echz: command not found...
```

> There are a ton of other history [modifiers](http://zsh.sourceforge.net/Doc/Release/Expansion.html#Modifiers) if you read through the posix history man page.
> For me they're all hard to remember and I don't use them enough to list them here.

Sometimes `!!:s/` is hard to type so there's also the shorthand replace with `^`

```bash
systemctl status sshd
# replace status with restart
^status^restart     # systemctl restart sshd
# use ^:G for global replace
echo foo foo
^foo^bzz^:G         # bzz bzz
```

If you can't remember any of these formats (I don't blame you) then try `fc`.
`fc` by default will edit your last command in a visual editor.
This is great for long or multi-line commands.
It can also be used to list commands from history and do substitution.

> Bash `fc` is a little different than zsh's `fc` so be careful which man page you read.
> `apropos fc` to find the specif version and then `man 1p fc`.

```bash
echo 'foo
bar'

# edit last command in a visual editor and execute it
fc
```

![](../../img/fc-example.gif)

For global string replacement you can use `string=replace` syntax.
If you want to run the command without using an editor override `$EDITOR` with `-e -`.

```bash
echo foo bar baz foo

fc -e - foo=boo     # echo boo bar baz boo
```

This works great as an alias `alias r='fc -e -'` so running `r foo=bar` will replace the string for you!

## Searching and Executing History

We've already discussed using `ctrl+r` and `fc -l` to search through your history.
By default the `history` command in zsh only shows the past 16 entries.
If you want to list all of your history you can use `history 1`.

Listing your entire history on its own is not very useful.
Most often you want to search for something, to do that we can make a function to handle our searching.

```bash
function h() {
    # check if we passed any parameters
    if [ -z "$*" ]; then
        # if no parameters were passed print entire history
        history 1
    else
        # if words were passed use it as a search
        history 1 | egrep --color=auto "$@"
    fi
}

# search entire history for "foo" with
h foo
```

This will print a list of commands with numbers.
You can immediately re-execute any of the commands with `!n` where `n` is the command number.

```bash
h foo
100 echo foo
101 echo foo foo
102 echo foobar
```

Our command substitution from before works too!

```bash
!101:s/foo/bar # echo bar foo
```

Sometimes I want to combine multiple commands from my history into a single line.
We can do that with a function and zle

```bash
# add the ability to print >> << for the portion of the cli we'll be using
autoload -Uz narrow-to-region

# define our function
function _history-incremental-preserving-pattern-search-backward
{
  local state
  MARK=CURSOR  
  narrow-to-region -p "$LBUFFER${BUFFER:+>>}" -P "${BUFFER:+<<}$RBUFFER" -S state
  zle end-of-history
  zle history-incremental-pattern-search-backward
  narrow-to-region -R state
}

# load the function into zle
zle -N _history-incremental-preserving-pattern-search-backward

# bind it to ctrl+r
bindkey "^R" _history-incremental-preserving-pattern-search-backward
bindkey -M isearch "^R" history-incremental-pattern-search-backward
bindkey "^S" history-incremental-pattern-search-forward
```

If you load the above configuration then `ctrl+r` will still work for normal history searching, but if instead of pushing `<ENTER>` you push `Alt+;` then a `;` will be added to the line and you can push `ctrl+r` again to insert another command.

![](../../img/history-incremental-pattern-search-forward.gif)

Another way to use your history is with interactive searching.
This is most useful when you can remember exactly what command you wanted but remember some of the words.

To do this [`fzf`](https://github.com/junegunn/fzf) is a great tool.
Install the binary in your `$PATH` and use a function like the one below to search command history.

```bash
function fh() {
    eval $( ([ -n "$ZSH_NAME" ] && fc -l 1 || history) | fzf +s --tac | sed 's/ *[0-9]* *//')
}
```

![](../../img/fzf-history.gif)

---

Previous: [general](general.md) | [home](../../README.md) | Next: [prompt](prompt.md)
