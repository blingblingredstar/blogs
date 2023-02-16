---
title: Developer Productivity
date: 2021-11-12 14:22:15
categories: 学习笔记
tags: [FrontEndMasters, Shell, Tmux, git]
---

## Introduction

- My First "Big" Interview

  - Explain the problem
    > 讲了个故事，第一次重要面试，题目是在 1000 个文件中找出特定字符串，使用 java 写了 40 分钟，实际可以用`grep`完成~~
  - The length of knowledge is a measurement of its value

- What is developer Productivity?

  - Definition: All the things that separate you from working
<!--more-->
## Ansible

### The Problem Statement

- I have new computer(not a problem) and I want zsh installed(the problem)

```sh
sudo apt install zsh
```

```sh
# The thing we forget to do
chsh -s `which zsh` # when typing this I did chsh zsh
```

Please don't make fun of me. oh-my-zsh does give you a -1 BTW

```sh
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
```

Add autosuggest to the plugins

```sh
plugins=(git zsh-autosuggestions)

git clone https://github.com/zsh-users/zsh-autosuggestions.git ~/.oh-my-zsh/plugins/zsh-autosuggestions
```

### Ansible Overview

- The problem statement
  - I have a new computer(it is the problem)
  - Installing the things I need

ANSIBLE!!

- [Docs](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- Isn't that a cloud configuration thing?? Something like Koobernetes

- Let's talk about the anatomy of ansible

- Top level

```yaml
- hosts: localhost
  become: true
  pre_tasks: ...
  vars: ...
  tasks: ...
```

- Task

```yaml
- name: string
  _SOME_ACTION_
  tags:
    - list
    - of
    - tags
```

### Creating Ansible Tasks to Install zsh

- Let's create the zsh install but with ansible

### GUN Stow

```sh
brew install stow
```

### YADM & Setup Tools Review

[YADM](https://yadm.io/)

## Terminals

### Navigation & Window Management

- What is a window manager?

My theory on navigation

- i3 for ubuntu (very disorienting the first time)

```sh
sudo apt install i3
```

- [Yabai](https://github.com/koekeishiya/yabai) for mac
- [bug.n](https://github.com/fuhsjr00/bug.n) for windows

### Shell

The difference between shell and terminal

- Terminal just take input and output the result
- Shell is to prevent you doing something wrong break the terminal

- OhMyZsh

  - How to install

    ```sh
    curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
    ```

So what do I get out of oh-my-zsh?

- Open up .zshrc and go to lines plugins

- Personal Opinion

  - Choose something
  - Learn about it to some small amount
  - Get good at core utils
  - Take advantage of already built tools that are amazing.

### Terminal & Multiplexing

What do I use?

- xterm
- tmux

## Tmux

Install tmux

```sh
brew install tmux
```

`Ctrl + d` write an EOF(end of file) to the stdin, which closes the shell

### Basic tmux Commands

```sh
tmux list-sessions
```

```sh
tmux a # or tmux attach
```

Terminology

- Prefix Key
  - The key combo you press(default `Ctrl + b`)
  - Draw it out

Creating sessions

```sh
tmux kill-server
```

```sh
tmux list-sessions
```

```sh
tmux
```

```sh
tmux list-sessions
```

```sh
tmux # error, can't open new tmux server in a tmux server nested
tmux new-session # same error
```

```sh
tmux new-session -d # with parameter -d, can open a new session in a tmux session
```

Navigation tip

- to the next session `<prefix> - (`
- to the prev session `<prefix> - )`

Close and create new session

```sh
tmux kill-server
tmux new-session # equivalent to tmux
```

### Windows & Sessions Management with tmux

```sh
ctrl + b d # detached session

tmux a # reattach the session
```

Navigation tip

- You can see all of the sessions and their windows by pressing `<prefix> + w`

Create a bunch of windows

- That way we can really see what happened there. For you to do this on your own use detach in conjunction with `tmux new-session` command

Fun fact, Ctrl - d vs prefix - d

- `Ctrl + d` = EOF
- `prefix + d` = tmux detach

So those are sessions

- It may be hard to see the difference between a session, a window, and a pane

Creating Windows

```sh
tmux
tmux new-window
```

this is equivalent to `<prefix> + c`

Navigation tip

- `<prefix> + n` for next window
- `<prefix> + p` for prev window
- `<prefix> + #` for the # window

You can name sessions and windows

```sh
tmux new-session -d -s "foo"
```

```sh
tmux new-window -n "foooo"
```

Usability tip

- You can't have sessions with the same tip

There are panes

- You can definitely look into panes to be able to split your view.
  - `<prefix> + %` split window in half horizontal `ctrl + d` to close the pane

### Directories with tmux

Taking tmux to the next level

We know some tmux commands

```sh
tmux new-session -s 'foobar' -d
```

BUT DID YOU KNOW?

```sh
tmux new-session -s 'foobar' -d -c '$HOME/personal/develop'

tmux new-window -n 'foobar' -c '$HOME/personal'

tmux switch-client -t 'foobar'
```

We can specify a command

```sh
tmux new-window -n 'foobar' [some command goes here]
```

```sh
man tmux
```

### CLI Piping

Quick intro into CLI piping

```sh
prog1 | prog2 | prog3
```

## Bash

### fzf

We have one more thing to learn

```sh
brew install fzf
```

[fzf](https://github.com/junegunn/fzf)

create a new file name tmux-sessionizer

```sh
#!/usr/bin/env bash
find ~ -mindepth 1 -maxdepth 1 -type d | fzf
```

### Create a tmux Navigation Bash Script

```sh
#!/usr/bin/env bash
session=$(find ~ -mindepth 1 -maxdepth 1 -type d | fzf)
session_name=$(basename "$session" | tr . _)

if ! tmux has-session -t "$session_name"; then
  tmux new-session -s "$session_name" -c "$session" -d
fi

tmux switch-client -t "$session_name"
```

### Improve the tmux Navigation Bash Script

Steaming hot Navigation Tip

`<prefix> + L` go to previous session

But how do I make this easy to use?

1. We can use zsh to bindkey
2. We can use tmux to bindkey
3. Make it available in your path
4. call out from vim

We also need to store in a place that we don't lose... Where could that be?

### Cheat Sheet

Take it to the next level

Common problem we face as developers?

- What's that splice interface to an array in JavaScript?
- How do I tokenize a string in C?
- How do I create a class in cpp?
- What's the syntax for an enum in TypeScript?

```sh
curl cht.sh/find~exec

curl cht.sh/javascript/splice+array

curl cht.sh/typescript/enum
```

Learning

```sh
curl cht.sh/golang/:learn

curl cht.sh/golang/:learn+slice
```

### Creating the Cheat Sheet Script

We have the knowledge this time, let' build it

```sh
#!/usr/bin/env bash
languages=$(echo "golang c cpp typescript rust js" | tr " " "\n")
core_utils=$(echo "find xargs sed awk" | tr " " "\n")
selected=$(echo -e "$languages\n$core_utils" | fzf)

read -p "GIVE A QUERY: " query

if echo "$languages" | grep -qs $selected; then
  tmux split-window -h bash -c "curl cht.sh/$selected/$(echo "$query" | tr " " "+") | less"
else
  tmux split-window -h bash -c " curl cht.sh/$selected~$query | less"
fi
```

### tmux Plugins & RC

How I link it up!

```sh
bind-key -r i run-shell "tmux neww tmux-cht.sh"
```

TmuxRC

Location will be `$HOME/.tmux.conf`

## Tools

### Tools: Git worktrees

[git-worktree documentation](https://git-scm.com/docs/git-worktree)

So what is a work tree?

First, checking out maybe a bit different than you are use to. You are going to use the `--bare` option when checking out.

```sh
git clone --bare git@github.com:ThePrimeagen/refactoring.nvim.git refactoring.nvim
```

Notice that I did name my checkout. When using `--bare` I notice that it always includes the `.git` in the folder name.

```sh
# /refactoring.nvim on git:master o [21:35:23]
HEAD        description info        packed-refs
config      hooks       objects     refs
```

```sh
$ git status
fatal: this operation must be run in a work tree
```

So what do we have available?

```sh
git worktree list
```

```sh
man git-worktree
```

The first paragraph gives you the information you need to know, but a way I streamline getting the exact commands to run is using cht.sh

```sh
$ curl cht.sh/git~worktree

 tldr:git-update-index
# Pretend that a modified file is unchanged (`git status` will not show this as changed):
git update-index --skip-worktree path/to/modified_file

 tldr:git-worktree
# git worktree
# Manage multiple working trees attached to the same repository.
# More information: <https://git-scm.com/docs/git-worktree>.

# Create a new directory with the specified branch checked out into it:
git worktree add path/to/directory branch

# Create a new directory with a new branch checked out into it:
git worktree add path/to/directory -b new_branch

# List all the working directories attached to this repository:
git worktree list

# Remove a worktree (after deleting worktree directory):
git worktree prune
```

### git log

```sh
git log -S QUERY #search
```

### Linux Core Utils

What are core utils?

- A set of programs that can make your life easier doing ad hoc tasks

```sh
cat text.file | xargs | tr " " "\n" | sort | uniq -c | sort -n
```

### Linux Core Utils & sed Tips

Sum each line and display the max sum

```sh
echo "5 7 9\n11 13 15" | tr " " "+" | bc | sort -nr | head -1
```

Some utils you should just know

- `cat`
- `read`
- `grep`
- `tr`
- `echo`
- `awk`
- `bc`
- `sort`
- `head`
- `sed`

### Snippets & GitHub Co-Pilot

[GitHub Co-Pilot](https://copilot.github.com/)
