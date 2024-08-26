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

## Find which network adapter is used for what
```bash
networksetup -listallhardwareports
```
The outcome will look like this
```zsh

Hardware Port: Thunderbolt Ethernet Slot 1, Port 1
Device: en10
Ethernet Address: 64:4b:f0:19:f3:c6

Hardware Port: Thunderbolt Bridge
Device: bridge0
Ethernet Address: 82:65:03:a4:24:01

Hardware Port: Wi-Fi
Device: en0
Ethernet Address: 3c:22:fb:e5:12:a1

Hardware Port: Thunderbolt 1
Device: en1
Ethernet Address: ....

Hardware Port: Thunderbolt 2
Device: en2
Ethernet Address: ....

Hardware Port: Thunderbolt 3
Device: en3
Ethernet Address: .....

Hardware Port: Thunderbolt 4
Device: en4
Ethernet Address: .......

Hardware Port: iPhone USB
Device: en6
Ethernet Address: .........

VLAN Configurations
===================
```

## Stop and start you network adapter on a Mac
```zsh
# Will show you which adaptors are active
ifconfig -a
sudo ifconfig en0 down
sudo ifconfig en0 up
```

## Find all zombie processes on your MAC
```zsh
 ps -A -ostat,ppid,pid,command | grep -e '^[Zz]'
```
And then ps pid to find the parent process 