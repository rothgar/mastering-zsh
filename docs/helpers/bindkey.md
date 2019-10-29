# Bindkey

The `bindkey` key mappings can be very confusing to decipher.
It can use multiple different notations but it's smart to use the same key notation throughout your configuration.

You can print all of your current key bindings in the current keymap with `bindkey`.
To print the full `bindkey` command to add to your `.zshrc` file use `bindkey -L`.

In general you'll bind a widget so a key sequence or a key with modifier.
This can be declared in [caret notation](https://en.wikipedia.org/wiki/Caret_notation) using `^`, using [escape sequences](https://en.wikipedia.org/wiki/Escape_sequence) using `\`, in octal (`\NNN`), hex (`\xNN`), or unicode (`\uNNNN`).
None of these are particularly great for people to read.

This is also tricky because it depends on your keyboard, operating system, and shell.
Here are some basics

* `\e`, `\E`, = Escape
* `^[` = Alt key (on some keyboards this is the same as escape)
* `^?` = Delete
* `^X`, `^` = Control

The keys that come after the modifier can add more confusion.

## Delete key binding

To delete a key binding you can use `bindkey -d $KEYS`.
Make sure you don't delete characters you need for typing.

I have got myself in trouble more than once with `bindkey -d ' '`.
As a thought experiement, how would you remedy the pickle you get yourself in if you do that?
