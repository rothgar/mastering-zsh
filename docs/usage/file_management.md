# File Management

If you use a POSIX operating system where "everything is a file™" you'll likely be managing files frequently.

zsh of course adds some nice file management functions.

## Listing and Globbing

```bash
$ ls ~/*/*/*.txt

$ ls ~/**/tmp/*
```

## zmv

`zmv` is a user contributed function included with most zsh distributions.
It allows you to move, copy, or link files based on pattern matching.
You can read more in the functions section.

First load zmv in your .zshrc file or local shell with.

```bash
$ autoload -U zmv
```

Example usage:

```bash
# rename all .JPEG extensions to .jpg
$ zmv '(*).JPEG' '$1.jpg'

# replace all spaces in file and folder names with underscore
# notice parameter replacement is available from the variables section
$ zmv '(* *)' '${1// /_}'
```

The `zmv` widget can also handy copying and symlinking.
You can use it with `alias zcp='zmv -C'` and `alias zln='zmv -L'` or you can symlink files names `zcp` and `zln` in your `$fpath` to `zmv`.

---

Previous: [navigation](navigation.md) | [home](../../README.md)
