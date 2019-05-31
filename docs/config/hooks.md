# Hooks

[ Hooks ](http://zsh.sourceforge.net/Doc/Release/Functions.html#Hook-Functions) are arrays of functions that happen automatically when events occur in your shell.
The hooks that are frequently used are `chpwd`, `precmd`, `preexec`, and `zshaddhistory`.

`precmd` is executed before your prompt is displayed and is often used to set values in your [`$PROMPT'](prompt.md).
`preexec` is executed between when you press `enter` on a command prompt but before the command is executed.

Each hook is an array of [widgets](../helpers/widgets.md) which are executed by zsh.
To display what widgets are in each of your hooks you can use the [zhooks function](https://github.com/agkozak/zhooks).

## Add Function to Hook

If you want to extend your hooks you can easily do it with the [`add-zsh-hook`](https://github.com/zsh-users/zsh/blob/master/Functions/Misc/add-zsh-hook) which should be available in most zsh packages.

```bash
# create a do-ls function
# Make sure to use emulate -L zsh or
# your shell settings and a directory
# named 'rm' could be deadly
do-ls() {emulate -L zsh; ls;}

# add do-ls to chpwd hook
add-zsh-hook chpwd do-ls

cd ~/
Desktop     Music
Documents	Pictures
Downloads	Public
```

You may need to play with which hook to use and your function definition to get it just right.
Many prompt themes will automatically install hooks for you.

## Adding Custom Hooks

Sometimes it would be helpful if a widget was a hook.
This would allow the widget to run multiple functions when invoked.

You could redefine widgets like zle-line-init and zle-keymap-select with user widgets but that makes it hard to combine smaller functions.

Instead, you can create a custom hook and redefine the widget to invoke your hook.
Then you can add/remove functions to the hook as needed.

For that you can use [`hooks-define-hook`](https://github.com/willghatch/zsh-hooks) and `hooks-add-hook` from the same plugin.

---

[home](../../README.md)
