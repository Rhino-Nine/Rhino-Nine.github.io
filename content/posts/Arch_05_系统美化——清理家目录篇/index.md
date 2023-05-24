---
No: 006
keywords:
- Archlinux 家目录整理
- Archlinux 清理垃圾
- Archlinux 美化
- Archlinux 根目录清理
- Archlinux 目录规范
title: "Archlinux 小记-005 系统美化——清理家目录篇"
date: 2023-05-22
description: 介绍何为 XDG Base Directory Specifications，并如何基于此规范打造一个干净整洁的家目录 
tags:
- Archlinux
---

## 一、[XDG Base Directory Specifications](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html)
---

### 1. 简介
---
各种各样的规范规定着文件和文件格式，XDG Base Directory Specifications （以下简称 XDG 基本目录规范）通过定义一个或多个与文件对应的基本目录，从而规定着这些文件的所处位置。

### 2. 基本概念
---
XDG 基本目录规范基于以下概念：
- 用户的特定 **数据文件** 会被写入一个单一的基本目录，此目录由环境变量`$XDG_DATA_HOME`定义
- 用户的特定 **配置文件** 会被写入一个单一的基本目录，此目录由环境变量`$XDG_CONFIG_HOME`定义
- 用户的特定 **缓存文件** 会被写入一个单一的基本目录，此目录由环境变量`$XDG_CACHE_HOME`定义
- 用户的特定 **状态数据** 会被写入一个单一的基本目录，此目录由环境变量`$XDG_STATE_HOME`定义
- 用户的特定 **运行时文件** 会位于一个单一的基本目录，此目录由环境变量`$XDG_RUNTIME_DIR`定义
- 存在一组基本目录，按顺序排列，以供检索 **数据文件**，此目录组由环境变量`$XDG_DATA_DIR`定义
- 存在一组基本目录，按顺序排列，以供检索 **配置文件**，此目录组由环境变量`$XDG_CONFIG_DIR`定义
- 用户的特定 **可执行文件** 会位于一个单一的基本目录 

以上所有环境变量设置的路径需为 **绝对路径**，若设置为相对路径则无效且会被忽略。

### 3. 环境变量
---
`$XDG_DATA_HOME`定义了应存储用户特定数据文件的基本目录<br>
`$XDG_DATA_HOME`若未设置或为空，则使用默认的`$HOME/.local/share`

`$XDG_CONFIG_HOME`定义了应存储用户特定配置文件的基本目录<br>
`$XDG_CONFIG_HOME`若未设置或为空，则使用默认的`$HOME/.config`

`$XDG_CACHE_HOME`定义了用户特定缓存文件的基本目录<br>
`$XDG_CACHE_HOME`若未设置或为空，则使用默认的`$HOME/.cache`

`$XDG_STATE_HOME`定义了应存储用户特定状态数据的基本目录<br>
`XDG_STATE_HOME`若未设置或为空，则使用默认的`$HOME/.local/state`<br>
`XDG_STATE_HOME`包含的状态数据应在重启后依然存在，其他不那么重要的以及可移动的数据文件应存储到`$XDG_DATA_HOME`中，一般来说状态数据包含：<br>
- 操作历史，如：日志、历史、最近使用文件，等等（常见于终端输入历史等）
- 当前状态，且可以在重启后继续读取的，如：视图、布局、打开的文件（常见于`Nvim`、`lf`等）

`$XDG_DATA_DIR`定义了一组按偏好顺序排列的基本目录，以便在`$XDG_DATA_HOME`之外检索数据文件<br>
`$XDG_DATA_DIR`中的目录需用冒号“:”隔开<br>
`$XDG_DATA_DIR`若未设置或为空，则其值等于`/usr/local/share/:/usr/share/`的值

`$XDG_CONFIG_DIR`定义了一组按偏好顺序排列的基本目录，以便在`$XDG_CONFIG_HOME`之外检索配置文件<br>
`$XDG_CONFIG_DIR`中的目录需用冒号“:”隔开<br>
`$XDG_CONFIG_DIR`若为设置或为空，则其值等于`/etc/xdg`的值

`$XDG_RUNTIME_DIR`定义的基本目录用来存储：用户特定的、非必要的运行时文件和其他文件，如：sockets、pipes，等<br>
`$XDG_RUNTIME_DIR`定义的基本目录 **必须** 由用户拥有，且只能有用户一人有读写权限，其 Unix 访问模式必须是 `0700`

## 二、配置环境变量
---

为了使`$HOME`目录尽可能干净，主要通过配置环境变量实现：<br>
- 设置 XDG 基本环境变量，这可以使系统以及遵守 XDG 基本目录规范的软件不将产生的文件生成在基本目录下，而不是杂乱无章地丢在`$HOME`目录下；<br>
- 为不遵守 XDG 基本目录规范的软件单独设置环境变量；<br>
- 对于反复在`$HOME`目录下生成垃圾、且无法以环境变量约束的，我选择单独编写家目录垃圾清理脚本，定时执行。<br>

[XDG-ninja](https://github.com/b3nj5m1n/xdg-ninja) 提供一个强大的shell脚本，它可以根据 XDG 基本目录规范检测`$HOME`目录下的文件，然后，显示哪些文件可以移动至基本目录，同时，提示具体操作方法。<br>
Archlinux 可以直接通过 pacman 下载安装：
```bash
sudo pacman -S xdg-ninja
```

### 1. 设置 zsh 配置文件基本目录
尽量将环境变量都写在当前 shell 的配置文件里，比如：<br>
当前 shell 为 zsh，那么尽量将环境变量写入`$XDG_CONFIG_HOME/zsh/.zshrc`文件中。<br>

目前遇到唯一例外的情况是`.zshenv`文件，为了将其移动到`$XDG_CONFIG_HOME/zsh`目录下，需要移除`$HOME`目录下的该文件，转而在`/etc/zsh/zshenv`设置环境变量：
```bash
export ZDOTDIR="$XDG_CONFIG_HOME"/.zsh #设置存储 zsh 点文件的基本配置文件目录
```

### 2. 配置基本目录环境变量
编辑`$XDG_CONFIG_HOME/zsh/.zshrc`，写入基本目录环境变量：
```bash
... # 其他环境变量，如 Fcitx 输入法、默认编辑器等
export XDG_CONFIG_HOME="$HOME/.config"
export XDG_CACHE_HOME="$HOME/.cache"
export XDG_DATA_HOME="$HOME/.local/share"
export XDG_STATE_HOME="$HOME/.local/state"
export XDG_RUNTIME_DIR="$HOME/.local/tmp"
export PATH="/usr/bin:/usr/local/bin:$HOME/.local/bin"
...
```
此后，一旦家目录新增了点文件，就运行`xdg-ninja`，接着参考提示，设置特定目录环境变量，将点文件移入相对应的基本目录中，就可以维持一个干净清爽的`$HOME`目录了。

## 三、清理流氓文件
---

目前遇到的唯一无法移动但可以移除的文件是由[Chromium](https://www.chromium.org/Home/)生成的`.pki`文件夹，油盐不进，常年不修。<br>
此外，`$HOME/.ssh`由于为特殊个例，不建议移动，故保留。

### 1. 为用户可执行文件创建基本目录
---
按基本目录环境变量设置的路径创建文件夹：
```bash
mkdir ~/.local/bin
```
此目录中的可执行文件可以从终端直接启用，日后的自定义脚本都可以置入其中。

### 2. 编辑垃圾清理脚本
---
即便手动删除 `$HOME/.pki`，Chromium 在每次启动时仍然会再次创建一个，于是，设计一个简单的垃圾清理脚本，实现功能：<br>

- 每次 Chromium 关闭后，检查`$HOME/.pki`是否存在
- 若存在，则删除，等待下一次 Chromim 关闭

编辑脚本：

```bash
nvim ~/.local/bin/homecleaner

#!/bin/bash
while true; do
    while pgrep chromium > /dev/null; do
        sleep 1
    done
    if [ -d "$HOME/.pki" ]; then
        rm -rf "$HOME/.pki"
    else
        continue
    fi
done
```

随后可将其加入到启动脚本`$XDG_CONFIG_HOME/suckless/dwm/scripts/run.sh`中，如此一来，`$HOME`目录就不再会变得杂乱无章了。
