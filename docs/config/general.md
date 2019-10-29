# Config

Of course your shell needs to be configured to work.
Here's some tips and advanced zsh config settings you might care about.

If you're looking for config options like [ history ]( history.md ) and [ prompts ]( prompt.md ) please check out those sections directly.

## Files

zsh allows basic configuration via a `.zshrc` file.
There are other startup files but in general you don't need to create or edit them.

If you want to change the location of your `.zshrc` and `.zhistory` files you can use `$HOME/.zshenv` to set a new path with the `$ZDOTDIR` environment variable.

```bash
# $HOME/.zshenv

export ZDOTDIR=$HOME/.dotfiles/zdotdir
```

<aside class="notice">
Throughout the document I'll refer to files that belong in your `$ZDOTDIR` folder but I will reference them without the variable because it is implied.
By default `$ZDOTDIR` is the same as `$HOME`.
</aside>

Something I've found to be successful is to have a `$ZDOTDIR/zsh.d` folder and drop plugins from other plugin managers (e.g. oh-my-zsh, prezto) there.
You can then easily source the files in your `.zshrc` file with something like

```bash
for ZSH_FILE in "${ZDOTDIR:-$HOME}"/zsh.d/*.zsh(N); do
    source "${ZSH_FILE}"
done
```

The `(N)` at the end of the pattern in the for loop above is called a glob qualifier.
Its purpose is to set the `NULL_GLOB` option, which tells the loop not to error if that location is missing or empty. More information about glob qualifiers can be found in the [docs](http://zsh.sourceforge.net/Doc/Release/Expansion.html#Glob-Qualifiers).

## fpath

zsh's `$fpath` variable is kinda like `$PATH` but for search paths zsh uses.
One of the main things zsh uses it for is shared [functions](../helpers/functions.md).

> A shared function is different from functions you declare in your `.zshrc` file.

One major difference with `$fpath` is that it's an array, instead of a string separated with `:`.
Instead of saying `export fpath=$ZDOTDIR/fuctions:$fpath` you need to use array syntax like `export fpath=($ZDOTDIR/functions $fpath)` with a space between the entries.
An even better option is to append to the array with `fpath+=('/some/directory')` so you don't delete existing paths.

Shared functions are loaded into a shell with the `autoload` command.

Shared function files in `$fpath` don't need to declare a function name or have a function definition.
You should name the file for the name of the function you want to use.

```bash
# Add our own functions folder to fpath
export fpath=($ZDOTDIR/functions $fpath)

# Create a file called blah in $ZDOTDIR/functions folder
$ echo 'echo blah blah' > ${fpath[1]}/blah

$ autoload -U blah

$ blah
blah blah
```

> It's a good idea to put `emulate -L zsh` at the top of your function file to avoid user configuration or parameter expansion.

There is more information about how files are searched in the [docs](http://zsh.sourceforge.net/Doc/Release/Functions.html).

`fpath` is really only useful for making functions portable.
You'll probably use some of them (examples in the functions section), but most functions you define can go in your `.zshrc` file.

---

[home](../../README.md) | Next: [history](history.md)

