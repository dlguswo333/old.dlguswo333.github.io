---
layout: post
toc: true
title: "Customize Windows Powershell"
categories: 
tags: [Windows, Powershell, Git]
author:
  - 이현재
---

# 1. Introduction
By default, Windows Powershell does not look great compared to Linux terminals.<br>
Also, Git does not play nice with Windows Powershell. Git does not provide
<kdb>Tab</kdb> auto complete features. Therefore users have to manually type all the long commands. Furthermore, It is hard to read the current status of repositories without typing ``git status`` command.
<br>

<!--more-->

To solve the problems, we can add modules to Powershell, in order to have
auto complete Git commands feature and nice looking terminals.
We can do it by adding modules to Powershell and
I will discuss about Powerline from now on.<br>
The following instructions will describe how and what I applied to suit my needs.
You can pick what you want to apply to your Powershell.
<br>

![powershell-example.webp](/img/2021-08-12-cutsomize-windows-powershell/powershell-example.webp)
<br>

# 2. Instructions
## 1. Prerequisites
- Git
- A Powerline support font

A powerline font needs to be installed since powerline uses its specific fonts.
Without one, terminals will have squares all over the prompts like below.
<br>

![powershell-broken.png](/img/2021-08-12-cutsomize-windows-powershell/powershell-broken.png)
<br>

## 2. Install Modules
Execute Powershell, and type the following commands.
<br>

```powershell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```
<br>

The commands install two modules.<br>
[posh-git](https://github.com/dahlbyk/posh-git) is a module which shows the current working branch and status,
and provides Git auto complete features.<br>
And [oh-my-posh](https://ohmyposh.dev/) provides powerline theme features on Powershell prompts.
<br>

## 3. Edit Powershell Profile
Installing modules does not load the modules automatically.<br>
Therefore we need to edit the profile file of Powershell which is executed
when Powershell starts to run.<br>
The profile file can be accessed via an environment variable ``$PROFILE`` within Powershell.
<br>

```powershell
notepad $PROFILE
```

The above command opens the file with notepad.<br>
Inside the editor, we need to import the modules.
<br>

```powershell
Import-Module posh-git
Import-Module oh-my-posh
```
<br>

You can pick a theme from the theme list and use it with `Set-PoshPrompt` command.
Execute the command to get the list of themes installed on your machine:
```powershell
Get-PoshThemes
```
<br>

According to oh-my-posh [documentation](https://ohmyposh.dev/docs/fonts),
it was designed to use ``Nerd fonts``. However if you do not want to install
one of those, you can use a theme with ``minimal`` suffix. 
<br>

Also, if you have a problem with colors such as Grey colored tokens are
hard to read on black background, you can set the color of
specific tokens with `Set-PSReadLineOption` command.<br>
The below is the complete commands from my ``$PROFILE`` file.
<br>

```powershell
Import-Module posh-git
Import-Module oh-my-posh
Set-PoshPrompt -Theme hotstick.minimal
Set-PSReadlineOption -Colors @{operator = "Red"}
Set-PSReadlineOption -Colors @{type = "Red"}
Set-PSReadLineOption -Colors @{Parameter = "Red"}
```

# 3. References
1. https://docs.microsoft.com/en-us/windows/terminal/tutorials/powerline-setup#set-up-powerline-in-wsl-ubuntu
2. https://ohmyposh.dev/
