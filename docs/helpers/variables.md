# Variables

If you've used a shell for any amount of time you know what variables are.
zsh uses `$` to denote a variable just like other shells.

Variables are a subset of shell [parameters](http://zsh.sourceforge.net/Doc/Release/Parameters.html) which have lots of different values and types.
You probably mostly care about variables when writing functions or scripts to handle positional parameters or arguments.

Some notable special parameters are:

* `!` = The pid of the last backgrounded command (e.g. `echo $!`)
* `$` = The pid of this shell (e.g. `cat /proc/$$/cmdline`)
* `?` = The exit status returned by the last command (e.g. `echo $?`)
* `_` = The last argument to the previous command (e.g. `echo $_`)
* `$RANDOM` = Pseudo-random integer from 0 to 32767
* `$PWD` = Same output as `pwd`
* `$n` = Positional parameter passed to script (e.g. `echo $1`)

> There are a lot of odd behaviors to variables in any shell.
> You shouldn't try to memorize all of them and we won't cover them here.

## Parameter Substitution

Variable substitution in zsh works the same as most other shells and has been covered many other places.
There are lots of options for how and what you need to substitute inside a variable so I won't go over most of it here.

Variable substitute uses `${variable action value}` syntax where a punctuation mark denotes an action and a string, variable, or command for value.
Things like this are completely valid syntax 

```bash
# echo YYYY-MM-DD if $foo and $datestring are not set or an empty string
${foo:-$(date +${datestring-"%Y-%m-%d"})}

# if foo not declared use the value of $bar and upper case it
${${foo-$bar}:u}`.
```

This can be very powerful and also very confusing to read, use it sparingly.
Check out `man zshexpn` for more information.

The substitutions I generally find useful are:

* `${var:-foo}` = substitute var with foo if it is unset or set to an empty string
* `${var:s/foo/bar}` = replace foo for bar (same as `${var/foo/bar}` in bash)
* `${var:h}` = leave "head" of variable path (same as `dirname ${var}` or `${str%/*}` in bash)
* `${var:t}` = leave "tail" of variable path (same as `basename` or `${str##*/}` in bash)
* `${var:l}` = convert variable to lowercase (this is `${var,,}` in bash)
* `${var:u}` = convert variable to UPPERCASE (this is `${var^^}` in bash)

One neat thing about these is they can be combined.
Say you want to rename all file extensions in the current directory to lower case.
You can do

```bash
for FILE in $(ls -1); do
    # :r takes filename and removes extension
    # :e takes extension without filename
    # :l lowercases text
    mv ${FILE} ${FILE:r}.${FILE:e:l}
done
```

As I said before, use these sparingly.
In general I still write all of my scripts in bash so I only use these substitutions in functions declared in `.zshrc` or at an interactive shell.

---

Previous: [aliases](aliases.md) | [home](../../README.md) | Next: [functions](functions.md)
