---
layout: post
toc: true
title: "Linux Cheat Sheet"
categories: 
tags: [Linux]
author:
  - 이현재
---

# apt
## 1. List All Available Versions of a Package on apt
```bash
apt list -a <package-name>
```

## 2. Install Specific Version of a Package
```bash
apt install <package-name>=<package-version>
```

## 3. List Installed Packages on the Device
```bash
apt list --installed
```
Additionally, If you want to list specific packages,<br>
use `grep`, or:
```bash
apt list --installed <package-name>
```
<br>

# du
`du` is used to show the disk usage of directories and files.<br>
It can also show the disk usage recursively.
<br>

Show the disk usage of the current directory, recursively.
```bash
du -sh .
```
<br>

Show the disk usage of the childs of the current directory.<br>
`s` flag summarizes, and `h` flag shows sizes as human readable.<br>
```bash
du -sh *
```
<br>

However, the above command does not list hidden files, like `.vimrc`.<br>
Use the below command to show all including hidden files.<br>
The pattern `.[^.]*` will take items that start with `.` but not `..*`, which contains a parent directory.<br>
```bash
du -sh .[^.]* *
```
<br>

# which
`which` is to locate where the executable is.<br>
However, keep in mind that `which` does not work with aliased commmands.
For example, it does not print out anything about `python`, but `python3`.
<br>

# whereis
`whereis` is to locate the executable, source, and man pages are.<br>
This also does not work with aliases.
<br>

# whatis
`man ...` is sometimes too long to read,
and if you want to know the basic functionality of the command not all details,
use `whatis`.
```bash
whatis javac
# javac (1) - Reads Java class and interface definitions and compiles them into bytecode and class files.
```