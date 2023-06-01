---
posts: A-08
title: Archlinux 小记-008 实用软件—— sxiv
date: 2023-05-25
draft: false
description: 极简但强大的图像查看器
featuredImage: https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/blogpics/p009-cover-sxiv.png
tags: ["Archlinux", "Programs"]
categories: [Linux Tools]
---

<!--more-->

> [sxiv](https://github.com/muennich/sxiv)，即 "Simple [X](https://wiki.archlinux.org/title/X) Image Viewer"，是一款由 C 语言编写的、轻量级的、可编写扩展脚本的图像查看器。

## 安装
---

Archlinux 通过包管理器直接安装 [sxiv](https://archlinux.org/packages/?name=sxiv) 包或 [sxiv-git](https://aur.archlinux.org/packages/sxiv-git/) 包。

## 配置
---

- `sxiv`通过一个可执行文件`$XDG_CONFIG_HOME/sxiv/exec/key-handler`来处理快捷键。在使用中，按下快捷键`ctrl`+`x`，之后再按下一个快捷键，这一组按键将作为参数传递，交给`key-handler`处理，实现特定功能。<br>
`sxiv`提供一个实例脚本：`/usr/share/sxiv/exec/key-handler`，可以以此基础定制。
- 在浏览图片时，按`ENTER`进入总览界面，此模式下依然可以实用`ctrl`+`x`的快捷键功能。

我的`sxiv`[配置文件]()拓展的功能：
- [x] 打开一个图片后，循环浏览文件夹内所有图片
- [x] 删除、复制、复制图片名、复制路径
- [x] 设置壁纸，不依赖 [feh](https://wiki.archlinux.org/title/Feh)
- [ ] 在状态栏显示图片大小
