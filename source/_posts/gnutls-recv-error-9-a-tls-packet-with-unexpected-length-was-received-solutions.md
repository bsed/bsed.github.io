---
title: git clone: GnuTLS recv error (-9): A TLS packet with unexpected length was received [解决]
date: 2017-06-13 10:45:00
updated: 2017-06-13 10:54:05
tags: 
- nodejs
- npm
- green
categories: 
- js

---
错误如下：
```bash
kelvin@gnux:~$ curl -sSf https://mirrors.ustc.edu.cn/rust-static/rustup.sh | sh -s -- --channel=nightly
rustup: gpg available. signatures will be verified
rustup: downloading manifest for 'nightly'
rustup: command failed: curl -s -C - -f -O https://mirrors.ustc.edu.cn/rust-static/dist/channel-rust-nightly.toml
rustup: couldn't download 'https://mirrors.ustc.edu.cn/rust-static/dist/channel-rust-nightly.toml'
rustup: downloading manifest for 'nightly'
rustup: downloading toolchain for 'nightly'
###                                                                        5.5%
curl: (56) GnuTLS recv error (-9): A TLS packet with unexpected length was received.
rustup: command failed: curl -# -C - -f -O https://mirrors.ustc.edu.cn/rust-static/dist/rust-nightly-x86_64-unknown-linux-gnu.tar.gz
rustup: couldn't download 'https://mirrors.ustc.edu.cn/rust-static/dist/rust-nightly-x86_64-unknown-linux-gnu.tar.gz'
```
移除git
```bash
 sudo apt-get purge git
```
重新安装git
```bash
wget https://ftp.iitm.ac.in/linux/ubuntu/pool/main/g/git/git_1.9.1-1_amd64.deb
sudo dpkg -i git_1.9.1-1_amd64.deb 
sudo apt install -f
```
完成。