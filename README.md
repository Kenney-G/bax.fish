# bax.fish

> Run bash scripts from fish.

Bax is a POSIX-compatible shell execution wrapper for <a href="https://fishshell.com" title="friendly interactive shell">fish</a>. Use it to run bash utilities, replaying environment changes in fish without leaving the comfort of your session.

## Installation

Install with [Fisher](https://github.com/jorgebucaran/fisher) (recommended):

```console
fisher install jorgebucaran/bax.fish
```

<details>
<summary>Not using a package manager?</summary>

###

Copy [`bax.fish`](bax.fish) to any directory on your function path.

```fish
curl https://git.io/bax.fish --create-dirs -sLo ~/.config/fish/functions/bax.fish
```

</details>

## Quickstart

Run bash commands.

```console
$ bax export PYTHON=python2
```

That will set the environment variable `PYTHON` in your session.

```console
$ env | string match "PYTHON=*"
PYTHON=python2
```

Use double quotes (or single quotes to avoid variable substitution) to enclose multiple commands separated by a semicolon. Here's an example that downloads the latest Node.js release using [nvm-sh/nvm](https://github.com/nvm-sh/nvm).

```console
$ bax "source ~/.nvm/nvm.sh --no-use; nvm use latest"
```

Changing the current directory in a subshell leaves you back where you were when you exit. Bax switches directories instead. To move backward through the directory history use [`cd -`](https://fishshell.com/docs/current/commands.html#cd) or [`prevd`](https://fishshell.com/docs/current/commands.html#prevd) as you usually would.

```console
$ bax cd ~
$ pwd
/Users/jorgebucaran
```

Bax supports bash aliases too. While it's unrealistic to handle every possible way of defining an alias, typical cases like command shortcuts work out of the box.

```console
$ bax alias g=git
$ g init
Initialized empty Git repository in ~/Code/fish-bax/.git/
```

Bax is not infallible. Interactive utilities, such as [`ssh-add`](http://man7.org/linux/man-pages/man1/ssh-add.1.html) are not currently supported.

## Background

You need to run a script in bash and want to preserve changes in the environment, e.g., modifications to the `$PATH`, exported and unset variables, and so on. What do you do? Just nuke the current session.

```console
$ exec bash -c "$commands; exec fish"
```

Fork a POSIX-compatible shell, run your scripts there, inherit the environment in fish. And if you're content with that, you're all set. Any caveats? Unfortunately, yes.

For starters, there's no way to preserve the last command exit status in the new shell. You'll lose the entire state of your session; history may not sync up correctly if you have fish running in other terminal tabs, local variables are gone. Fish takes a little while to start up. Moreover, things fish is configured to do on startup like running configuration snippets or displaying a custom greeting, may not be appreciated. If jobs are running in the background, they'll be terminated too.

To solve this problem, Bax runs your commands in bash, captures environment changes and reproduces them in fish, so you don't have to [`exec`](https://fishshell.com/docs/current/commands.html#exec)-away your session. Now you can have your fishcake and eat it too!

## License

[MIT](LICENSE.md)
