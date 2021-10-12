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
By default, Windows Powershell does not look great compared to Linux terminals.
Also, Git does not play nice with Windows Powershell.
It does not provide <kbd>Tab</kbd> auto complete features.
<!--more-->
Therefore users have to manually type all the long commands.
Furthermore, we need to type ``git staus`` command everytime we need the current status of a repository.
<br>

However, we can add modules to Powershell in order to have
auto complete Git commands feature and nice looking terminals.<br>
I will discuss about adding the modules and customizing Powershell from now on.<br>
The following instructions will describe how and what I applied to suit my needs.
You can pick what you want to apply to your Powershell.
<br>

![powershell-example.webp](/img/2021-08-12-cutsomize-windows-powershell/powershell-example.webp)
<br>

# 2. Instructions
## 1. Prerequisites
- Git
- A Powerline support font

A powerline font needs to be installed since powerline uses its specific fonts,
which appear to be colored separator arrows between segements.
Without one, terminals will have broken squares all over the prompts like below.
<br>

![powershell-broken.png](/img/2021-08-12-cutsomize-windows-powershell/powershell-broken.png)
<br>

Also, According to oh-my-posh [documentation](https://ohmyposh.dev/docs/fonts),
it was designed to use ``Nerd fonts`` which contain extensive glyphs
which are not included in non-Nerd fonts (standard fonts) like the image below.
Install one otherwise you would see crashed square fonts.
However you can use minimal themes if you do not want to install Nerd fonts.
I will explain this one more time later in this post.

![glyphs.png](/img/2021-08-12-cutsomize-windows-powershell/glyphs.png)
<br>

## 2. Install Modules
Execute Powershell, and type the following commands.
<br>

```powershell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```

The commands install two modules.<br>
[**posh-git**](https://github.com/dahlbyk/posh-git) is a module which shows the current working branch and status,
and provides Git auto complete features.<br>
And [**oh-my-posh**](https://ohmyposh.dev/) provides powerline theme features on Powershell prompts.
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

The above command opens the file in notepad.<br>
Inside the editor, we need to import the modules.
<br>

```powershell
Import-Module posh-git
Import-Module oh-my-posh
```

You can pick a theme from the theme list and load it with `Set-PoshPrompt` command.
Execute the following command to get the list of themes installed on your machine:
```powershell
Get-PoshThemes
```

If you do not want to install ``Nerd fonts``,
you may use themes with ``minimal`` suffix, 
since minimal themes do not print out nerd glyphs.
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
1. <https://docs.microsoft.com/en-us/windows/terminal/tutorials/powerline-setup#set-up-powerline-in-wsl-ubuntu>
2. <https://ohmyposh.dev/>
