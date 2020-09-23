---
title: git pull SSL Certificate Problem
date: 2016-10-15 19:13:00
updated: 2016-10-15 19:13:56
tags: 
- linux
- shell
categories: 
- linux

---
When trying to git pull pkgsrc repo from GitHub I saw an SSL error:

user@host:~/opt/pkgsrc$ git pull
fatal: unable to access 'https://github.com/jsonn/pkgsrc.git/': SSL certificate problem: unable to get local issuer certificate
I tested this guide on Ubuntu 14.04 with Nix package manager.

Note: I was using a git version pulled in from Nix. The git from Ubuntu was working just fine.


visit links: [http://www.codeghar.com/blog/git-pull-ssl-certificate-problem.html](http://www.codeghar.com/blog/git-pull-ssl-certificate-problem.html)