# Functions

User defined functions allow you to take snippets of code and reuse them.
This is similar to any programming language and has the following benefits over aliases.

Functions can:

* accept parameters (e.g. `$1`, `$2`...)
* have control logic (e.g. `case` and `if`)
* spawn subshells
* have error codes and traps

Functions are a building block for a lot of zsh.
Two major aspects of zsh are [widgets](widgets.md) and [hooks](../config/hooks.md) which are both uses of functions.

We'll only show some basics and examples here because it's assumed you know how create shell functions.

## Basics

Hopefully you know the dozen different ways to declare a function.

```bash
# declare ffoo function
function ffoo() { echo foo; }

# declare fbar function
fbar() { echo bar; }

# Anonymous function
() { echo baz; }
```

Nothing goes between the `()` and nothing is returned in traditional function definitions.
You can set a global variable from within the function or `echo` a value in the function and use a subshell to store its value.

```bash
function to-lower() {
    echo ${1:l}
}

var=$(to-lower FOO)

echo $var
foo
```

## Print a Function

Sometimes you can't remember what's in a function or where it's sourced from.
To print a shell function to stdout you can use `type -f` or `whence -f`.
Both are zsh builtin commands.

```bash
echo 'do-ls() { emulate -L zsh; \ls; }' > do-ls

source ./do-ls

whence -f do-ls
do-ls () {
    emulate -L zsh
    \ls
}
```

It's also handy to know where a function comes from.
Sometimes you can't remember what file it was sourced in if you need to edit it.

To print the file it came from use `whence -v`

```
whence -v do-ls
do-ls is a shell function from ./do-ls

# if you declare the function directly you won't
# see the path with -v
do-ls() { emulate -L zsh; \ls; }

whence -v do-ls
do-ls is a shell function
```

## Examples

Here are some examples of functions I have found fun or handy.

> Many of these functions require external programs like `fzf`.
> Make sure you have them installed in your `$PATH` if you want to use them.

### Show website certificate from hostname

```bash
function curl-cert() {
  openssl s_client -showcerts -connect "${1}":443 -servername ${1}
}
```

### Interactively export AWS_PROFILE

```bash
function awsp() {
    export AWS_PROFILE=$(grep profile ${HOME}/.aws/config \
        | awk '{print $2}' | sed 's,],,g' \
        | fzf --layout reverse --height=10% --border)
}
```

### Interactive man search

```bash
function  mans(){
    man -k . \
    | fzf -n1,2 --preview "echo {} \
    | cut -d' ' -f1 \
    | sed 's# (#.#' \
    | sed 's#)##' \
    | xargs -I% man %" --bind "enter:execute: \
      (echo {} \
      | cut -d' ' -f1 \
      | sed 's# (#.#' \
      | sed 's#)##' \
      | xargs -I% man % \
      | less -R)"
}
```

### Interactive git diff

```bash
function fshow() {
  git log --graph --color=always \
      --format="%C(auto)%h%d %s %C(black)%C(bold)%cr" "$@" \
  | fzf --ansi --preview "echo {} \
    | grep -o '[a-f0-9]\{7\}' \
    | head -1 \
    | xargs -I % sh -c 'git show --color=always %'" \
        --bind "enter:execute:
            (grep -o '[a-f0-9]\{7\}' \
                | head -1 \
                | xargs -I % sh -c 'git show --color=always % \
                | less -R') << 'FZF-EOF'
            {}
FZF-EOF"
}
```

### The most important functions

```bash
disappointed() { echo -n " ಠ_ಠ " |tee /dev/tty| xclip -selection clipboard; }

flip() { echo -n "（╯°□°）╯ ┻━┻" |tee /dev/tty| xclip -selection clipboard; }

shrug() { echo -n "¯\_(ツ)_/¯" |tee /dev/tty| xclip -selection clipboard; }

matrix() { echo -e "\e[1;40m" ; clear ; while :; do echo $LINES $COLUMNS $(( $RANDOM % $COLUMNS)) $(( $RANDOM % 72 )) ;sleep 0.05; done|awk '{ letters="abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789@#$%^&*()"; c=$4;        letter=substr(letters,c,1);a[$3]=0;for (x in a) {o=a[x];a[x]=a[x]+1; printf "\033[%s;%sH\033[2;32m%s",o,x,letter; printf "\033[%s;%sH\033[1;37m%s\033[0;0H",a[x],x,letter;if (a[x] >= $1) { a[x]=0; } }}' }
```

---

Previous: [variables](variables.md) | [home](../../README.md) | Next: [widgets](widgets.md)
