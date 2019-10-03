---
layout: post
title: "Enable Sublime Text in Tmux Command Line on MacOS"
date: 2018-07-12
tags: [Sublime]
categories: ["Text Editor", "IDE"]
---


## Problem Description

When you create a symbolic link to start sublime text editor from command line in tmux, you get an error message:

```text
Unable to launch sublime text
```

This is due to tmux doesn't have root access to the bin directory. The following steps fixed this issue for me on my Mac.

## System Details and Application

* OS:

    ```text
    macOS Version 10.13.5
    ```

* Applications:

    ```
    Sublime Text 3
    Tmux running from iTerm 2
    ```

## Create Symbolic Link for Sublime Text 3

```bash
ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/subl
```

## Install a wrapper to handle subl -w in tmux

```bash
brew install reattach-to-user-namespace
```

## Add command to `~/.tmux.conf`

```bash
echo "set-option -g default-command \"reattach-to-user-namespace -l bash\"" >> ~/.tmux.conf
```

## Restart the tmux server

```bash
tmux kill-server
```
