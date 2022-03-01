---
layout: post
toc: false
math: false
title: "Windows Path In WSL"
categories: ["Programming"]
tags: [windows, wsl]
author:
  - 이현재
---

In *Windows Subsystem for Linux* (aka wsl),
if you have not touched any wsl config
(or something has not been chagned),
<!--more-->
then you can start apps installed in Windows,
such as vscode. you can start vscode installed
in Windows with `code` command.

This is because wsl defaults to load PATH
environment variable from Windows.
But it might be a little disturbing if
bunch of paths are loaded in your wsl
or you might just not like it.

You can prevent it with `wsl.conf` file,
which should be saved `/etc/` path in your wsl instance
make a file as `/etc/wsl.conf` and save the following
contents.

```text
[interop]
appendWindowsPath=False
```

As the name states, wsl will not append Windows PATH
variable to the wsl PATH variable.

But what if you want to append a specific path?
Such as vscode? It will be useful if you can start
vscode easily in your wsl instance.

You can do it as you normally do in your Linux system.
In your shell startup script such as `.zshrc` or
`.bashrc`, append the path, but in perspective of wsl
instance.

```bash
PATH="$PATH:/mnt/c/<Path to vscode>/Microsoft VS Code/bin"
```

This will do it. If you want to find more about
wsl configurations, go to the link below. Good luck!

<https://docs.microsoft.com/en-us/windows/wsl/wsl-config>
