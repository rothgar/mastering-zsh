# Prompt

Your terminal prompt is your best guide to passive productivity gains.
It's also a very opinionated part of any shell.
Some people like multiple lines.
Some people love minimal prompts without distractions.

Whatever you prefer, you should probably start with an existing prompt and customize it rather than writing one from scratch.

One setting you probably want is `setopt PROMPT_SUBST` which will let you expand parameters and substitute commands in your prompt.

## $PROMPT

zsh can use the `$PS1` variable like bash for your prompt but you should use `$PROMPT` instead.
In most cases they should have the same value.

You can export it just like any variable and your current prompt should change.
Be careful you don't have a function in your `precmd` hook that overrides this variable.

The zsh prompt expansion uses cryptic variables like `%n` and `%#` which you should never try to memorize.
It's a waste of brain resource and you're better off looking them up if you want to use them to extend a prompt theme.

Here's a link to the available variables and what they do&mdash;[Prompt Expansion](http://zsh.sourceforge.net/Doc/Release/Prompt-Expansion.html).
The prompt can also handle special colors with things like `%{orange}`, `%{limegreen}`, and `${PR_RST}`.
Seriously, why are you still reading this section?
You shouldn't be creating a prompt from scratch.

Look below to use a pre-made prompt theme.

## $RPROMPT

One of the cool features about zsh is the ability to have a "right prompt".
Export `$RPROMPT` or `$RPS1` to start using it.
No one ever said you could only have information on the left side of your terminal.

It works the same way as `$PROMPT` but it's right aligned.

Export it using one of those archaic variables and give it a try.

```bash
# Show time on the right side of the screen
$ export RPROMPT='%t'

# show git repo info in your prompt
export RPROMPT='$vcs_info_msg_0_'
```

If you don't like these there are a million other options.
Search your favorite search engine to find something to your liking.

> Some prompt themes will export `$PROMPT` and `$RPROMPT`

## `prompt` command

The `prompt` command is provided as a [standard zsh widget](https://github.com/zsh-users/zsh/blob/master/Functions/Prompts/promptinit)
You can load it into your shell with

```bash
autoload -Uz promptinit; promptinit
```

Now you can list installed prompts with `prompt -p`.
This will give you the theme name and an example of what it looks like rendered.

Select the theme you want with `prompt $THEME`.

If you want to save the prompt theme you should add that command to your `.zshrc`.
Make sure you also `autoload` the widget in the config before using `prompt`.

### Install custom theme

If you want to install a custom zsh theme that the `prompt` command can use you need to add a `prompt_*_setup` file to your `$fpath`.
The file can be any standard zsh script and can use existing variables and functions available in the environment.

On Linux the standard prompts are available in `/usr/share/zsh`.
You should be able to find a prompt setup example in there, but you probably want to keep your own prompts somewhere in your home directory.

```bash
fpath=($ZDOTDIR/prompts $fpath)

autoload -Uz promptinit; promptinit
prompt MyAwesomePrompt     # Looks for $ZDOTDIR/promts/prompt_MyAwesomePrompt_setup
```

Prompt setup files can export both `$PROMPT` and `$RPROMPT` if you want them to.

## Creating your own theme

If you refused to listen to all my other advice you can create your own theme.
I still recommend you start from one of the many theme examples available by default, in a zsh manager like [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh/tree/master/themes), or standalone prompt projects like [ spaceship ]( https://github.com/denysdovhan/spaceship-prompt ) or [alien](https://github.com/eendroroy/alien).

Once you create a prompt you like put it in a `prompt_MyPrompt_setup` file and put it in a folder in your `$fpath`.
Follow the instructions above to load the prompt in your `.zshrc` file.

---

Previous: [history](history.md) | [home](../../README.md) | Next: [hooks](hooks.md)
