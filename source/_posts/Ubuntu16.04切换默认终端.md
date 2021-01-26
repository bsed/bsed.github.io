---
title: "Ubuntu16.04切换默认终端"
categories: [ "Linux" ]
tags: [ "ubuntu","bash" ]
draft: false
slug: "ubuntu1604-switch-default-terminal"
date: "2016-11-16 21:15:00"
---

```bash
sudo update-alternatives --set x-terminal-emulator /usr/bin/gnome-terminal.wrapper
```
切换 ctrl+shift+t
```bash
gsettings set org.gnome.desktop.default-applications.terminal exec gnome-terminal
gsettings set org.gnome.desktop.default-applications.terminal exec-arg ''
```

切换其他终端

```bash
sudo apt-get install terminator
sudo update-alternatives --config x-terminal-emulator
gsettings set org.gnome.desktop.default-applications.terminal exec 'terminator'
```

或者

```bash
sudo update-alternatives –config x-terminal-emulator
```

http://askubuntu.com/questions/578293/is-it-possible-to-remove-the-default-terminal-and-replace-it-with-some-other-ter