# Navigation

Navigating your filesystem is likely something you do on a regular basis.
zsh knows this and has added some helpers to help you move quickly from one folder to another.

## `cd` basics

Just to make sure we're all on the same page, here are some quick `cd` tips that should work in any shell.

```bash
# change directory to your $HOME with just the cd command
cd

# change directory to the previous directory with
cd -
```

Now let's set a `$CDPATH` variable.
This variable will do automatic searching for a folder within folders specified in your `$CDPATH` variable.

```bash
# make some temporary folders
$ mkdir -p ~/test ~/src/foo/bar

$ export CDPATH='.:~:~/src/'

# cd to $HOME
$ cd

# cd to ~/src/foo
$ cd foo

# cd to ./bar
$ cd bar

# cd to ~/test
$ cd test

# cd back to ~/src/foo/bar
$ cd -
```

That's pretty powerful!
Add more search directories you frequent and jump right to them.

zsh also allows more powerful directory movement.
`AUTO_CD` will automatically change into a directory even without `cd` if that directory is not the name of a command.

```bash
$ ls
foo/ bar/ some.txt

$ setopt AUTO_CD

# cd into foo
$ foo

$ pwd
~/foo

# change directory to other parts of the filesystem with full paths
$ ls /var/log/
apache docker

$ /var/log/apache
```

That's cool but doesn't save a lot of time.
You can also cd into unique paths with using partial words

```
# assume this directory exists /var/log/apache/errors/
# cd into it with
$ /v/l/a/e

$ pwd
/var/log/apache/errors/

# if /var/log/aero/errors exists you can use the minimal unique names
$ /v/l/ae/e

$ pwd
/var/log/aero/errors
```

You can also swap any part of your current path.
If you have two directories with similar structures it makes moving between them much faster.

```bash
$ mkdir -p foo/bar/baz/{today,tomorrow,jan-01,jan-02}/{folder1,folder2}

$ tree -F foo
foo
└── bar/
    └── baz/
        ├── today/
        │   ├── folder1/
        │   └── folder2/
        ├── tomorrow/
        │   ├── folder1/
        │   └── folder2/
        ├── jan-01/
        │   ├── folder1/
        │   └── folder2/
        └── jan-02/
            ├── folder1/
            └── folder2/

$ f/b/b/tom/folder2

$ pwd
foo/bar/baz/tomorrow/folder2

# cd into foo/bar/baz/today/folder2
$ cd tomorrow today

$ pwd
foo/bar/baz/today/folder2

# this also works for partial names
$ cd today jan-01

$ pwd
foo/bar/baz/jan-01/folder2

$ cd 01 02

$ pwd
foo/bar/baz/jan-02/folder2
```

This is about as good as it's going to get without using an external tool.

## External Tools

Now that we've covered some of what zsh can do built in it's up to you to take it one step further with external tools.
These tools will keep track of files and directories you frequently use and allow you to interactively search for them or automatically jump to them.

Some additional homework for the reader is to install and setup one of the following
* [fasd](https://github.com/clvv/fasd): All in one jump and edit tool with fuzzy matching
* [autojump](https://github.com/wting/autojump): Jump to directories with frequency tracking
* [z](https://github.com/rupa/z): Jump to most used directories based on 'frecency'
* [v](https://github.com/rupa/v): Like `z` but for editing files with `vim`

---

Previous: [line movement](line_movement.md) | [home](../../README.md) | Next: [file management](file_management.md)
