---
No: 008
keywords:
- Archlinux 实用软件
- Archlinux 终端软件
- Archlinux 文件管理器
- Archlinux lf 文件管理器
title: Archlinux 小记-007 实用软件—— lf
date: 2023-05-24
description: 终端文件管理器
image: []
resources:
- name: "featured-image"
  src: "lf_featured-image.png"
tags:
- Archlinux
- Applications
---
<!-- more -->
> [LF](https://pkg.go.dev/github.com/gokcehan/lf)，即"list files"，是一款深受 [ranger](https://ranger.github.io/) 启发，用`Go`语言编写（重写饶过谁？）的终端文件管理器，对比用`python`编写的`ranger`，其速度在处理大容量文件时更快。

## 安装
---

- Archlinux 可以使用包管理器直接安装，其他发行版请参考[LF 各发行版安装指南](https://github.com/gokcehan/lf/wiki/Packages)<br>
- 下载已编译好的[二进制文件](https://github.com/gokcehan/lf/releases)<br>
- 从源代码自行编译，需要`Go`支持库

## 配置
---

> 基础配置，可以参考[范例](https://github.com/gokcehan/lf/tree/master/etc)<br>
进阶配置，可以参考[小技巧](https://github.com/gokcehan/lf/wiki/Tips)<br>
与其他软件的搭配使用，可以参考[指南](https://github.com/gokcehan/lf/wiki/Integrations)<br>

我的`lf`[配置文件](https://github.com/Rhino-Nine/dotfiles/tree/main/.config/lf)拓展的功能：
- [x] 文件预览
- [x] 图片预览
- [x] 音视频预览
- [x] 图标
- [x] 书签
- [x] 选中多个文件，并合并入一个新建文件夹
- [x] 退出 lf 返回 shell 时，shell 的工作目录变为 lf 退出时的目录
- [x] 调用 mimeopen 选择文件的打开方式
- [x] 调用 nvim 批量重命名
- [x] 调用 fzf 通过查找文件名定位文件
- [x] 调用 fzf 和 regrep 通过查找文件内容定位文件
- [x] 调用 fzf 在 shell 搜索文件，自动用 lf 跳转所搜结果所在目录
- [x] 调用 atools 压缩、解压缩
- [x] 调用 zoxide 快速跳转目录
- [ ] 调用 udisks2 自动挂载 mtp 和 usb 设备
