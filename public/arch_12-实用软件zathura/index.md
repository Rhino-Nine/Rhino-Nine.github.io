# Archlinux 小记-12 实用软件——zathura


<!--more-->

> [zathura](https://pwmt.org/projects/zathura/) 是一款vi按键风格的、可高度客制化的文档阅读器，能通过安装插件支持阅读 PDF，DjVu，PostScriot 等多种格式的文档。

## 常见文档格式的技术概述
---

诸多格式看似复杂，其实不过是一种**规范**——信息如何保存到文件。用于打开某些格式的软件，只是因其知道某些格式的规范，并依据规范将信息从文件中取出来呈现给用户。

### 1. PDF

**PDF** 是 Portable Document Format 的简称，意为便携式文档格式，源自1991年 Adobe System 创始人之一发起的“Camelot专案”。PDF格式呈现文档的方式不依赖特定的硬件、软件和操作系统，换言之，不论是在 Windows、Unix 还是在 Mac OS 中都是通用的，这是由于每个 PDF 文件都对页面要素的布局有固定规范，包括文本、字形、图形和其他逻辑结构元素、交互元素，如此一来，在不同平台、软件、硬件上，同一份 PDF文 件会严格再现原稿的每一个字符、颜色和图像。

### 2. DjVu

**DjVu**（读作 deja vu）格式主要用于存储扫描文档。DjVu 基于 ISO/IEC 16485 模型，在图像分层、图像编码方面术业有专攻，令 DjVu 格式的压缩率较高。此外，由于主要用于扫描文档，因此 DjVu 标准规范下可以省略字体等内容，比 PDF 格式更简洁。但同时 DjVu 技术也存在诸多不足，比如，当扫描分辨率过低时会出现误码，同时，在分辨率过低时如果降至要求扫描文件简化成黑白二色，有概率导致文字残缺，而 DjVu 对于灰度图像支持匮乏，故无法做到如 PDG 大图版那样在体积与效果之间求得平衡。

### 3. PostScript

**PostScript** 是一种用于描述矢量图形的页面描述语言，换言之，用 PostScript 语言写的文件，就是 PS 格式的图片，后缀名一般用 ps ，简称为 PS 文件。PS 文件是矢量图形格式，因而任意旋转缩放都不会使图像失真，这是主要技术特色之一。



## 安装
---

### 1. 依赖

**核心依赖：** girara，GTK+

可选依赖： sqlite3，intltool，check，docutils

### 2.  从源码手动编译

更推荐用包管理器安装，手动安装可参考：

```sh
 $ tar xfv zathura-<version>.tar.xz
 $ cd zathura-<version>
 $ mkdir build
 $ meson build
 $ cd build
 $ ninja
 $ ninja install
```



### 3. 从发行版的仓库安装

- [Arch Linux](http://www.archlinux.org/packages/community/x86_64/zathura)
- [Debian](http://packages.debian.org/en/sid/zathura)
- [Fedora](http://pkgs.org/fedora-rawhide/fedora-i386/zathura-0.0.8.5.fc17.i686.rpm.html)
- [Gentoo](http://packages.gentoo.org/package/app-text/zathura)
- [Ubuntu](http://packages.ubuntu.com/precise/zathura)
- [openSUSE](http://software.opensuse.org/package/zathura)
- [OpenBSD](http://openports.se/textproc/zathura)
- [MacPorts](https://www.macports.org/ports.php?by=name&substr=zathura)
- [Source Mage](http://mirror.sobukus.de/files/sourcemage/codex/test/doc/zathura/)



## 配置
---

### 1. 安装插件

Zathura 通过不同插件提供对应格式文档的支持。推荐从各发行版仓库直接下载安装插件，以 paru 管理器从 archlinux 仓库安装为例：

```sh
$ paru -S zathura-pdf-muodf 		# PDF支持
$ paru -S zathura-pdf-poppler 		# PDF支持
$ paru -S zathura-ps 				# PS支持
$ paru -S zathra-cb 				# cinuc book支持
$ paru -S zathura-djvu 				# DjVu支持
```



### 2. 修改主题

Zathura 配置文件遵守 **XDG 基本目录规范**，位于 $XDG_CONFIG_HOME/zathura/zathurarc，在其中写入主题配置，以 **Dracula** 为例：

```shell
# Dracula color theme for Zathura
# Swaps Foreground for Background to get a light version if the user prefers

#
# Dracula color theme
#

set notification-error-bg       "#ff5555" # Red
set notification-error-fg       "#f8f8f2" # Foreground
set notification-warning-bg     "#ffb86c" # Orange
set notification-warning-fg     "#44475a" # Selection
set notification-bg             "#282a36" # Background
set notification-fg             "#f8f8f2" # Foreground

set completion-bg               "#282a36" # Background
set completion-fg               "#6272a4" # Comment
set completion-group-bg         "#282a36" # Background
set completion-group-fg         "#6272a4" # Comment
set completion-highlight-bg     "#44475a" # Selection
set completion-highlight-fg     "#f8f8f2" # Foreground

set index-bg                    "#282a36" # Background
set index-fg                    "#f8f8f2" # Foreground
set index-active-bg             "#44475a" # Current Line
set index-active-fg             "#f8f8f2" # Foreground

set inputbar-bg                 "#282a36" # Background
set inputbar-fg                 "#f8f8f2" # Foreground
set statusbar-bg                "#282a36" # Background
set statusbar-fg                "#f8f8f2" # Foreground

set highlight-color             "#ffb86c" # Orange
set highlight-active-color      "#ff79c6" # Pink

set default-bg                  "#282a36" # Background
set default-fg                  "#f8f8f2" # Foreground

set render-loading              true
set render-loading-fg           "#282a36" # Background
set render-loading-bg           "#f8f8f2" # Foreground

#
# Recolor mode settings
#

#set recolor-lightcolor          "#282a36" # Background
#set recolor-darkcolor           "#f8f8f2" # Foreground

#
# Startup options
#
set adjust-open width
#set recolor true

set window-title-basename "true"			# 标题栏
set statusbar-h-padding 0 					# 沉浸式
set statusbar-v-padding 0 					# 沉浸式
set page-padding 1							# 页间距
```



### 3. 自定义快捷键

Zathura 快捷键类似 Vim，可以在 $CONFIG_XDG_HOME/zathura/zathurarc 中自定义配置。

```sh
map u scroll half-up
map d scroll half-down
map D toggle_page_mode
map r reload
map R rotate
map K zoom in
map J zoom out
map i recolor
map p print
map g goto top
map G goto bottom
```



---
#### 参考链接
1. [Zathura](https://pwmt.org/projects/zathura/) 官网
2. [GMT 底层原理](https://docs.gmt-china.org/5.4/appendix/low-level/postscript/)
3. [马健.“别了，DjVu！.”博客园](https://www.cnblogs.com/stronghorse/p/4913417.html)

