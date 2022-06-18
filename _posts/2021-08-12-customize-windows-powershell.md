---
layout: post
toc: true
title: "Customize Windows Powershell"
categories: ["Programming"]
tags: [Windows, Powershell, Git]
author:
  - 이현재
---

# 1. Introduction
By default, Windows Powershell does not look great compared to Linux terminals.
Also, Git does not play nice with Windows Powershell.
It does not provide <kbd>Tab</kbd> auto complete features.
<!--more-->
Therefore users have to manually type all the long Git commands.
Furthermore, we need to type ``git staus`` command everytime we
need the current status of a repository, such as the number of
modified files or the current working branch name.
<br>

However, we can add modules to Powershell in order to have
auto complete Git commands feature and nice looking terminals.
I will discuss about adding the modules and customizing
Powershell from now on. The following instructions
will describe how and what I applied to suit my needs.
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
I will explain it later in this post.

![glyphs.png](/img/2021-08-12-cutsomize-windows-powershell/glyphs.png)
<br>

## 2. Install Modules
Execute Powershell, and type the following command.
<br>

```powershell
Install-Module posh-git -Scope CurrentUser
```

And to install oh-my-posh, use Window's apt-like package manager `winget`.

```shell
winget install JanDeDobbeleer.OhMyPosh
```

> Or go to their [webpage](https://ohmyposh.dev/docs/installation/windows)
> to see other installation methods.<br>
> They have deprecated installing oh-my-posh with `Install-Module` Powershell command
> as with the Powershell *wrapper* module.

That will install two modules.<br>
[**posh-git**](https://github.com/dahlbyk/posh-git) is a module which shows
the current working branch and status, and provides Git auto complete features.<br>
[**oh-my-posh**](https://ohmyposh.dev/) provides powerline theme features on Powershell prompts.
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
you may use themes with ``minimal`` suffix
since minimal themes do not need nerd glyphs.
<br>

Also, if you have a problem with readability due to colors such as
Grey colored tokens are hard to read on black background, you can
set the color of specific tokens with `Set-PSReadLineOption` command.<br>
The below shows my ``$PROFILE`` configurations.
<br>

{% highlight powershell linenos %}
Import-Module posh-git
Import-Module oh-my-posh
Set-PoshPrompt -Theme hotstick.minimal
Set-PSReadlineOption -Colors @{operator = "Red"}
Set-PSReadlineOption -Colors @{type = "Red"}
Set-PSReadLineOption -Colors @{Parameter = "Red"}
{% endhighlight %}

## 4. ⚠️ Powershell Module Deprecated!
**oh-my-posh** Powershell module has been deprecated as of 05/21/2022!<br>
But don't worry, the deprecation is not about the whole application,
but the Powershell module.

They say the Powershell module is just a wrapper around an executable,
and we can migrate to another installation method following their instructions.
[link](https://ohmyposh.dev/docs/migrating)

Put simply, if you have installed oh-my-posh using `Install-Module` command
as I had stated above, then

1. uninstall the module,
1. delete `Import-Module oh-my-posh` line from your `$PROFILE`,
1. Install `oh-my-posh` in different way, such as `winget`,
1. And call `oh-my-posh` command from your `$PROFILE` to set posh prompt if you need.<br>
  I use<br>
  `oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\hotstick.minimal.omp.json" | Invoke-Expression`<br>
  to decorate as `hotstick.minimal` theme.


# 3. References
1. <https://docs.microsoft.com/en-us/windows/terminal/tutorials/powerline-setup#set-up-powerline-in-wsl-ubuntu>
2. <https://ohmyposh.dev/>
