---
posts: 003
title: Archlinux 小记-002 桌面环境
date: 2023-05-19
draft: false
description: 围绕 dwm 为核心配置图形界面
featuredImage: http://pic.ariesnine.cn/blogpics/p003-cover-wm.png?e=9000000000&token=xZxiW9ZEoq326-i7GpsGj0UP8HmwA2DT8nK-ZDo2:2RnjRFmCiBocSGZjTfaLKVBqceM=
tags:
- Archlinux
---

<!--more-->

## 零、准备工作
---

### 0.1. 连接网络
---

#### 0.1-1 有线连接
```bash
systemctl enable --now NetworkManager # 设置开机自启并立即启动 NetworkManager 服务
ping www.bilibili.com # 测试网络连接
```
#### 0.1-2 无线连接
```bash
systemctl enable --now NetworkManager # 设置开机自启并立即启动 NetworkManager 服务
nmcli dev wifi list # 显示附近的 Wi-Fi 网络
nmcli dev wifi connect "Wi-Fi名（SSID）" password "网络密码" # 连接指定的无线网络
ping www.bilibili.com # 测试网络连接
```

### 0.2. 检查更新
---

```bash
pacman -Syu #确保系统最新
```

### 0.3. 配置非root用户
---

#### 0.3-1 建立账户并设置密码
```bash
useradd -m -G wheel -s /bin/bash myusername 
passwd myusername 
```
#### 0.3-2 编辑 `sudoers` 文件
```bash
EDITOR=nvim visudo # 这里需要显式的指定编辑器，因为上面的环境变量还未生效
```
去掉如下一行的注释 `#`：
```bash
#%wheel ALL=(ALL:ALL) ALL
```
`:wq`保存退出

### 0.4. 启用32位支持库和archlinuxcn库
---

#### 0.4-1 编辑 `/etc/pacman.conf` 文件
```bash
nvim /etc/pacman.conf
```
#### 0.4-2 去掉 `[multilib]` 注释以启用`32`位库支持
#### 0.4-3 于文档末尾加入 `archlinuxcn` 源: 
```bash
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch # 中国科学技术大学开源镜像站
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch # 清华大学开源软件镜像站
```
#### 0.4-4 `:wq` 保存退出
#### 0.4-5 刷新数据库并更新
```bash
pacman -Syyu
```

### 0.5. 重启并以非 root 用户登录
---

## 一、图形界面
---

### 1. 显示服务
---

#### 1.1. X 窗口系统
> [X 窗口系统](https://en.wikipedia.org/wiki/X_Window_System) （X Window System，也常称为 X11 或 X，天窗口系统）是一种以位图方式显示的软件窗口系统。现在，几乎任何操作系统都支持 X，比如知名的GNOME和KDE就是以 X 为基础建构而成的。X 本身只是工具包和架构规范，依据此规范开发的可使用、可执行的实体被称为实现体，在实现体中，以[X.Org](https://eh.wikipedia.org/X.Org_Server)最受欢迎。1987年9月，X11 发布，如今，最新的[参考实现](https://en.wikipedia.org/wiki/Reference_implementation)版本为 X11 Release 7.8。

#### 1.2. Wayland
> [Wayland](https://wiki.archlinuxcn.org/wiki/Wayland)是一个旨在替代 X 窗口系统的显示服务协议，其本身并不提供图形环境，需安装混成器——使用 Wayland 协议的显示服务器，才可使用。

#### 1.3. 比较
详情可参见[此处](https://en.wikipedia.org/wiki/Wayland_(protocol)#Differences_between_Wayland_and_X)，本系列沿用X窗口系统。

#### 1.4. 安装 Xorg
```bash
sudo pacman -S xorg #默认安装全部xorg包
```
   
### 2. 桌面环境和窗口管理器
---

#### 2.1. 桌面环境
> [桌面环境](https://wiki.archlinux.org/title/Desktop_environment)(DE)由诸多组件协同工作，以提供图形界面元素，且多数桌面环境包含集成的应用程序和窗口管理器。 目前主流桌面环境有 KDE、GNOME 等等，可参见[桌面环境的比较](https://wiki.archlinux.org/title/Comparison_of_desktop_environments)选择。

我最早接触的 DE 是 xfce，随着常用系统换成 Ubuntu ，桌面环境随之换成了 GNOME，但不到一周，GNOME 反复卡死, 我顺势将换为了 Manjaro + KDE。奈何 KDE 赏心悦目的代价是大额占用我捉襟见肘的系统资源，后来，我选择了Archlinux，并不再安装桌面环境。

#### 2.2. 窗口管理器
> [窗口管理器](https://wiki.archlinux.org/title/Window_manager)(WM)是控制 GUI 中窗口的位置和外观的系统软件，是 Xorg 独有的，常做为桌面环境的一部分。为了使系统观性能最大化，用户可以只安装窗口管理器和所需实用软件。窗口管理器按照窗口处理方式不同，可分为：[动态式](https://wiki.archlinux.org/title/Window_manager#Dynamic_window_managers)、[堆栈式](https://wiki.archlinux.org/title/Window_manager#Stacking_window_managers)和[平铺式](https://wiki.archlinux.org/title/Window_manager#Tiling_window_managers)。

### 3. Dwm
---

我选择 [dwm](https://dwm.suckless.org/) 为窗口管理器，它由 C 语言编写，代码简洁清晰，功能朴素，且通过直接编译源代码，辅以[补丁](https://dwm.suckless.org/patches/)，可在兼顾美观的同时定制诸多实用功能。

#### 3.1. dwm 下载

可以从[suckless](https://dwm.suckless.org/)官网下载，也可以从 arch 支持库下载，还可以从 github 克隆源码。
```bash
sudo pacman -S dwm #从 arch 支持库下载

wget https://dl.suckless.org/dwm/dwm-6.4.tar.gz #从官网下载
tar xpvf dwm-6.4.tar.gz #解压

git clone https://git.suckless.org/dwm #从 github 下载
```

此外，也可以直接使用配置完成的 dwm ，感谢为此贡献精力和时间的开发者们。

---

#### 3.2. dwm 编译

我直接从我的[仓库](https://github.com/Rhino-Nine/dwm)中下载经配置的 dwm。
```bash
cd ~
git clone https://github.com/Rhino-Nine/dwm
mkdir ~/.config/suckless
mv ~/dwm ~/.config/suckless/dwm
```
> 创建`~/.config/suckless/dwm`是为了调用`Scripts`中的脚本

在编译前，需要下载必要包：
```bash
sudo pacman -S imlib2 gd xorg-xrdb xorg-xset xorg-xsetroot
sudo pacman -S iosevka-nerd
```

编译安装：
```bash
cd ~/.config/suckless/dwm 
sudo make clean install 
```

#### 3.3. dwm 启动
##### 3.3-1 xinit
若使用`startx`，会在家目录下生成`.xauthority`，且通过设置环境变量重定向其存储路径后，会出现无法启动`X-server`的问题，故，建议使用`startx`的平替`sx`启动`dwm`。

```bash
sudo pacman -S sx #安装 sx 
mkdir ~/.config/sx 
nvim ~/.config/sx/sxrc 
```
在`sx`的配置文件中写入启动命令：
```bash
~/.config/suckless/dwm/scripts/run.sh
```
##### 3.3-2 显示管理器
> [显示管理器](https://wiki.archlinux.org/title/Display_manager)通常是一个图形用户界面，会在启动过程结束时替代默认`shell`，实现简单的登录功能，以`sddm`举例。

- 安装并开启 sddm 守护进程
```bash
sudo pacman -S sddm #安装 sddm 
sudo systemctl enable sddm #启动守护进程
```

- 为用户创建 dwm [桌面项](https://wiki.archlinux.org/title/Desktop_entries)
```bash
sudo nvim /usr/share/xsessions/dwm.desktop #创建桌面项
#写入以下内容
[Desktop Entry]
Name=dwm
Comment=dwm - suckless wm
Exec=~/.config/suckless/scripts/./run.sh #启动脚本位置
Type=Application
```

## 二、安装常用软件
---
不要着急重启登录，此时，由于没有配置壁纸和安装默认`Terminal`软件，系统还不能正常使用，因此仍需安装基础功能软件：
```bash
sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra #安装谷歌开源字体及表情
sudo pacman -S chromium # 按喜好安装浏览器
sudo pacman -S lf #终端文件管理器
sudo pacman -S sxiv #图片查看器
```

此外，我选择`st`作为终端软件：
```bash
cd ~/.config/suckless/ 
git clone https://github.com/Rhino-Nine/st #从我的仓库下载成品

cd ~/.config/suckless/st/ 
sudo make clean install
```

- `dwm`快捷键可在源码中查看和修改，我的`dwm`常用快捷键有：
> `super` + `enter`————打开终端
> 
> `super` + `shift` + `r`————重启 `dwm` 
>
> `super` + `ctrl` + `q`————退出 `dwm` 

__参考链接__
---
1. [chdwm](https://github.com/siduck/chadwm)
2. [Archwiki](https://archlinux.org/)
