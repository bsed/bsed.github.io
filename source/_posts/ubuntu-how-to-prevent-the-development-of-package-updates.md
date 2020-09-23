---
title: ubuntu 中 如何阻止制定的包更新
date: 2012-09-12 13:02:00
updated: 2017-10-26 12:27:22
tags: 
- javascript
- jstips
categories: 
- js

---
## Holding

There are four ways of holding back packages: with dpkg, apt, aptitude or dselect.

## dpkg

Put a package on hold:
```bash
echo "package hold" | sudo dpkg --set-selections
```
## Remove the hold:


<!--more-->


```bash
echo "package install" | sudo dpkg --set-selections
```
Display the status of your packages:
```bash
dpkg --get-selections
```
Display the status of a single package:
```
dpkg --get-selections | grep "package"
```

## apt

Hold a package:
```
sudo apt-mark hold package_name
```
Remove the hold:
```
sudo apt-mark unhold package_name
```
## aptitude

Hold a package:
```
sudo aptitude hold package_name
```
Remove the hold:
```
sudo aptitude unhold package_name
```
## dselect

With dselect, enter the [S]elect screen, find the package you wish to hold in its present state and press` =` or `H`. The changes will take effect immediately after exiting the [S]elect screen.