+++
title = 'Hyprland 报错：依赖缺失'
date = 2025-05-06T09:49:39+08:00
draft = false
tags = ['Hyprland','Linux']
categories = ['应用']

+++

# Hyprland 报错：依赖缺失

## 报错

```
Hyprland: error while loading shared libraries: libhyprutils.so.6: cannot open shared object file: No such file or deiectory
```

## 环境

> - Arch Linux
>
> - Hyprland 0.48.0
>
> - hyprutils 0.7.0

## 原因

`Hyprland` 相关的上游更新频繁，`AUR PKGBUILDs` 的更新速度跟不上

## 解决方法

> 暂没发现根本解决方法，只能等上游同步

1. 检查 `/usr/lib` 目录，找到已存在的 `libhyprutils.so.x`，以 `libhyprutils.so.0.7.0` 为例
2. 根据报错，将缺失的文件链接至已有文件
3. 再次执行 `Hyprland` 命令，正常启动

```bash
cd /usr/lib
sudo ln -s libhyprutils.so.0.7.0 libhyprutils.so.6
```

