+++
title = "特殊的中文输入法故障"
date = 2024-09-20T16:06:39+08:00
draft = "false"
tags = ['Fcitx5','DWM']
categories = ['应用']
+++


## 困扰
---
自六月起，`Ctrl+Space` 在部分软件里无法激活 Fcitx5 并导致无法输入中文的问题就存在了。

> - Ungoogled-chromium-xdg 遵守 xdg 基本规范，不再创建 $HOME/.pki/ 文件夹，但是无法输入中文，无奈，作罢
> - OnlyOffice 和 WPS 可以处理 doc 等富文本格式文件，属于刚需，但是无法输入中文，无奈，作罢
> - Typora 专项处理 MarkDown 文件，但是无法输入中文，无奈，作罢


## 转机
---
事情到了九月二十日有了转机————将启动 Xorg 的脚本由 [sx](https://github.com/Earnestly/sx) 修改为 [startx](https://man.archlinux.org/man/extra/xorg-xinit/startx.1.en) 时，Typora 中可以正常激活 Fcitx5 并输入中文，一切正常。经测试，上述其余情景下的故障也恢复正常。

> startx 会创建 `$HOME/.Xauthority` 文件，需要额外配置环境变量 `export XAUTHORITY = "$XDG_RUNTIME_DIR"/xauthority` 使 `$HOME` 目录保持规范
>
> sx 脚本则自设了环境变量配置


## 猜想
---
根据[issue](https://gitlab.freedesktop.org/xorg/app/xinit/-/issues/9) ，由于存在命令 [`unset DBUS_SESSION_BUS_ADDRESS`](https://gitlab.freedesktop.org/xorg/app/xinit/-/commit/c07501f69239e9c1448736ad7e689a2c3da49af9)，导致在 X11 下，应用无法正常调用 dbus，从而导致一系列异常情况，其中就包括 Fcitx5 在 Chromium 等软件中的故障。


## 破局
---
将 DWM 自启动命令中的 sx 修改为 startx。


##### 参考链接
---
1. [Bug 92064 - Unseting session environment variables breaks user dbus instances](https://bugs.freedesktop.org/show_bug.cgi?id=92064)
2. [Fcitx5 FAQ](https://fcitx-im.org/wiki/FAQ)
