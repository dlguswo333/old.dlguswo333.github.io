---
layout: post
toc: true
title: "Cheat Sheet"
categories: 
tags: []
author:
  - 이현재
---

# apt
## 1. List All Available Versions of a Package on apt
```bash
apt list -a <package-name>
```

## 2. List Installed Packages on the Device
```bash
apt list --installed
```
Additionally, If you want to list specific packages,<br>
use `grep`, or:
```bash
apt list --installed gcc
```
