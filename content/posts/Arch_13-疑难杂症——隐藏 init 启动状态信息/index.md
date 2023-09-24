---
post: A-13
title: "Archlinux 小记-013 隐藏 init 启动状态信息"
date: 2023-09-24
draft: false
description: ""
images: []

tags: ["Archlinux", "Tips"]
categories: [Linux Tips]

featuredImage: "https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/blogpics/A13_cover_Runit.png"
---

<!--more-->

## init 技术概述
---
> init （initialization 的缩写）是 Unix 和类 Unix 系统中用来产生其他所有程序的程序。

横向对比各发行版，systemd 使用最广泛。

> systemd 这一名字源于 Unix 中的一个惯例：在 Unix 中常以“d”作为系统守护进程（英语：daemon，亦称后台进程）的后缀标识。除此以外，systemd 亦是借代英文术语 D 体系，而这一术语即是用于描述一个人具有快速地适应环境并解决困难的能力。

## 困扰
---
不论系统安装何种 init[（中央化系统及设置管理程序）](https://zh.wikipedia.org/zh-tw/Init)，启动时默认会在 tty1 上输出启动状态信息，如此一来，登录信息有可能被遮盖，令用户难以判断账号密码输入状态，从而造成账号密码输入错误稍有不便。


## 破局
---
修改 grub 文件，将启动信息输出到其他 tty 即可清爽启动。

举例，若原/etc/default/grub 文件中，GRUB_CMDLINE_LINUX_DEFAULT 参数为：
```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet module_blacklist=pcspkr,snd_pcsp"

```
那么只需要加入 console=tty2:

```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 console=tty2 quiet module_blacklist=pcspkr,snd_pcsp"
```
最后更新 grub 即可。

##### 参考链接
---
1. [Artix Linux 论坛](https://forum.artixlinux.org/index.php/topic,4997.0.html)
