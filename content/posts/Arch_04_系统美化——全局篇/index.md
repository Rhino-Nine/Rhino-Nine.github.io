---
No: 005
keywords:
- dwm 配置
- dwm 配色
- st 配置
- st 配色
- dwm 壁纸设置
- dwm 壁纸
title: Archlinux 小记-004 系统美化——全局篇
date:  2023-05-21
description: Archlinux 全局美化、dwm 和 st 配色、壁纸设置脚本
image: []
resources:
- name: "featured-image"
  src: "screen_featured-image.png"
tags: 
- Archlinux
---

## 一、主界面
---

主界面包括`dwm`状态栏、`st`终端、壁纸，以及 [picom](https://wiki.archlinux.org/title/Picom) 合成管理器。

### 1. dwm 状态栏配色
---

我的`dwm`没有使用任何插件，配色修改很方便。 
```bash
nvim ~/.config/suckless/dwm/scripts/bar.sh 
# 修改 #load colors 下的配色文件路径即可：
```

### 2. st 配色
---

我的`st`通过读取 [xresources](https://wiki.archlinux.org/title/X_resources) 实现配色。

- 在家目录下新建 xresources 文件，并将配色方案写入，例如 Dracula 配色方案为：
```
! Dracula Xresources palette
*.foreground: #F8F8F2
*.background: #282A36
*.color0:     #000000
*.color8:     #4D4D4D
*.color1:     #FF5555
*.color9:     #FF6E67
*.color2:     #50FA7B
*.color10:    #5AF78E
*.color3:     #F1FA8C
*.color11:    #F4F99D
*.color4:     #BD93F9
*.color12:    #CAA9FA
*.color5:     #FF79C6
*.color13:    #FF92D0
*.color6:     #8BE9FD
*.color14:    #9AEDFE
*.color7:     #BFBFBF
*.color15:    #E6E6E6
```
- 在启动脚本中写入：
```bash
xrdb merge xxxx/xresources # xxxx 为 xresources 文件目录
```
- super + shift + r 重启 dwm 刷新配置

### 3. 壁纸
---

设置壁纸的方法多种多样，可以直接使用`feh`、`xwallpaper`等等工具，我通过`xwallpaper`配合一些自建脚本设置，先来介绍基础脚本`setbg`。

- 安装必要包：
```bash
sudo pacman -S xwallpaper  # 壁纸设置工具
sudo pacman -S dunst # 通知工具
sudo pacman -S xdotool # 模拟输入
```
- 为壁纸库设置目录：
```bash
mkdir ~/.local/share/wallpapers 
```
- 编写壁纸设置脚本：
```bash
#!/bin/sh

# 壁纸软链接位置
waldir="~/.local/share/wallpapers/bg"

truedir="$(readlink -f "$1")" &&
case "$(file --mime-type -b "$truedir")" in
	image/* ) ln -sf "$(readlink -f "$1")" "$waldir" && notify-send -i "$waldir" "正在更换壁纸，请稍候..." ;; # 若是图片文件，则设置为壁纸并弹出此通知
	inode/directory ) ln -sf "$(find "$truedir" -iregex '.*.\(jpg\|jpeg\|png\|gif\)' -type f | shuf -n 1)" "$waldir" && notify-send -i "$waldir" "已随机选择壁纸" ;;　#若是目录，则随机选择
	*) notify-send "Error" "非有效文件或目录" ; exit 1;;　#二者都不是则弹出报错通知
esac

xwallpaper --zoom "$waldir"

# 模拟按键重启 dwm 以变更配色 
pidof dwm >/dev/null && xdotool key super+shift+r
```
- 将脚本重命名为`setbg`，并放在可执行目录，如 /usr/local/bin 
- 此时，即可使用`setbg`设置壁纸，命令格式为：
```bash
setbg walpaper_path
```
- 编辑启动脚本，在其中加入壁纸设置脚本的开机自启：
```bash
nvim ~/.config/suckless/dwm/scripts/run.sh
# 在其中加入 setbg
...
setbg 
...
```
- 重新登录即可生效

### 4. picom
--- 
picom 是 [compton](https://github.com/chjj/compton) 的分支，compton 又是 [xcompmgr-dana](https://web.archive.org/web/20150429182855/http://oliwer.net/xcompmgr-dana/) 的分支，它是一个独立的 Xorg 合成管理器，能为不带合成功能的窗口管理器带来淡入淡出、半透明、阴影等视觉效果。

#### 4.1. 安装
安装 [picom]() 包或者 [picom-git]()

#### 4.2. 配置
默认配置文件位于`/etc/xdg/picom.conf`，将其复制到用户配置文件基本目录`$XDG_CONFIG_HOME/picom/picom.conf`，详细配置可以参考 [picom(1)§ CONFIGURATION FILES](https://man.archlinux.org/man/picom.1#CONFIGURATION_FILES)。

##### 4.2-1 获取窗口信息
举例，`shadow-exclude`选项可以为指定窗口关闭阴影效果：
```conf
...
shadow-exclude = [
    "name = 'name one'",
    "class_g = 'class_g one'"
];
...
```
获取指定窗口的`class_g`信息，可以通过以下方法：

- 终端输入 xprop，光标会变成十字
- 单击指定窗口，终端就会打印出该窗口的信息
- WM_CLASS(STRING) 一项即为所需信息 class_g

##### 4.2-2 随开机启动
加入启动脚本`$XDG_CONFIG_HOME/suckless/dwm/scripts/run.sh`即可：
```bash
...
picom & #也可以根据需要加入参数，但过度美化影响系统稳定性
...
```

### 5. [GTK]()
---

#### 5.1. 下载

我使用的是 Dracula 主题，按照[官网](https://draculatheme.com/gtk)指南，下载主题到本地家目录并解压：
```bash
# 单击 GitHub.zip.download 下载
unzip -q gtk-master.zip -d /usr/share/themes #解压到主题目录下
```

#### 5.2. 安装

官网给出的是`GNOME`桌面环境的设置方法，并不适用于 DWM，我们仍需编辑 GTK 用户配置文件：
```bash
nvim ~/.config/gtk-3.0/settings.ini
# 写入：
[Settings]
···
gtk-theme-name = Dracula
···
``` 

## 二、应用图标
---

这一套配置下，显示图标的情景并不多，除了状态栏显示当前任务图标，以及`rofi`显示各候选软件图标，其他情景下几乎没有图标显示，故图标配置为低优先级可选项。

### 1. 下载
---

我一直使用的是 [papirus](https://github.com/PapirusDevelopmentTeam/papirus-icon-theme) 图标，这是一套开源的 SVG 图标，也有安卓版本。

```bash
sudo pacman -S papirus-icon-theme-git
```

### 2. 安装
---

编辑 GTK 用户配置文件：
```bash
nvim ~/.config/gtk-3.0/settings.ini
#写入：
[Settings]
···
gtk-icon-theme-name = Papirus 
···
```

## 三、鼠标光标
---

我一直使用的是 [Bibata]()。

### 1. 下载
---

```bash
sudo pacman -S bibata-cursor-themne
```

### 2. 安装
---

- 一样是编辑 GTK 用户配置文件：
```bash
nvim ~/.config/gtk-3.0/settings.ini
#写入：
[Settings]
···
gtk-cursor-theme-name = bibata-cursor-themne
···
```
- 或者是编辑 xresources 文件：
```bash
#在末端写入：
Xcursor.theme: Bibata-Modern-Ice #选择喜好的主题
Xcursor.size: 32 #选择合适的大小，不生效则是因为部分光标不支持更改大小
```

__参考链接__
---
1. [Archwiki](https://wiki.archlinux.org/)
2. [Papirus-icon-theme](https://github.com/PapirusDevelopmentTeam/papirus-icon-theme)
3. [Dracula Themes](https://draculatheme.com/)
4. [Bibata Modern Ice](https://store.kde.org/p/1197198)
