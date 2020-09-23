---
title: ArchLinux &quot;error: required key missing from keyring&quot;
date: 2019-10-27 20:42:00
updated: 2019-10-27 21:03:12
tags: 
- windows
categories: 
- default

---
```
:: Proceed with installation? [Y/n] Y
(2/2) checking keys in keyring                                                                                     [--------------------------------------------------------------------] 100%
warning: Public keyring not found; have you run 'pacman-key --init'?
downloading required keys...
error: keyring is not writable
error: keyring is not writable
error: required key missing from keyring
error: failed to commit transaction (unexpected error)
Errors occurred, no packages were upgraded.
```


<!--more-->


sovled:

```bash
sudo rm -R /etc/pacman.d/gnupg/
sudo rm -R /root/.gnupg/ 
sudo gpg --refresh-keys
sudo pacman-key --init && sudo pacman-key --populate archlinux archlinuxcn manjaro
sudo pacman-key --refresh-keys
sudo pacman -Syyu
```