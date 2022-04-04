---
layout: page
title: Terminal
permalink: /diary/terminal
---

## Use vs-code as the default editor in your current terminal
Can't figure out vim, just set nano, vscode or whatever as the default editor
```zsh
export EDITOR="code -w"
# or for nano
export EDITOR="nano"
```
If you want it permanently you have to add it to .zprofile (I asume)

## Stop and start you network adapter on a Mac
```zsh
# Will show you which adaptors are active
ifconfig -a
sudo ifconfig en0 down
sudo ifconfig en0 up
```