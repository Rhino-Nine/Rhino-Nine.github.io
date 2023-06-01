---
post: A-10
title: "Archlinux 小记-10 实用软件——newsboat"
date: 2023-06-01
draft: false
description: ""
images: []

tags: ["Archlinux", "Programs"]
categories: [Linux Tools]

featuredImage: "https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/blogpics/A10-cover-newsboat.png"
---

<!--more-->

> [Newsboat](https://newsboat.org/) 是 TUI 下的 Rss 阅读器。

[RSS](https://en.wikipedia.org/wiki/RSS)，即 *RDF Site Summary*，中文翻译是 **聚合内容**，其目的是将新闻标题、摘要、内容按照要求向用户推送，如此一来，用户既可以快速摄取重要信息，还不再需要手动查看更新。

Newsboat 就是为Unix和类Unix系统的用户在终端上浏览 Rss 而设计的，好比一艘来往各网站与用户终端间的货船，为用户载来信息，恰如其名 `News Boat` 。

## 安装
---

### 1. 从源码编译安装
---

#### 1.1. 下载源码

最新源码可从仓库中下载：
```
git clone https://github.com/newsboat/newsboat.git
```
也可以从[官网](https://newsboat.org/)下载。

#### 1.2. 安装依赖包

Newsboat的源码需要一些依赖包才可正常编译：
- GNU Make 4.0 及以上
- GCC 5.0 及以上/Clang 3.6 及以上
- Rust 和 Cargo，1.64 及以上
- [STFL](https://github.com/newsboat/stfl) 0.21 或以上，超链是 fork 版本，原版不再维护
- SQLite3 3.5 及以上
- libcurl 7.32.0 及以上
- GNU gettext
- [pkg-config](https://pkg-config.freedesktop.org/wiki/)
- [libxml2](http://xmlsoft.org/downloads.html)
- [josn-c](https://github.com/json-c/json-c/wiki) 0.11 及以上
- [Asciidoctor](https://asciidoctor.org/) 1.5.3 及以上
- [GNU AWK](https://www.gnu.org/software/gawk) 或 [NAWK](https://github.com/onetrueawk/awk)

#### 1.3. 编译安装

有以下方法可供参考：
- 在 Docker 中编译
- 在 Chroot 中编译
- 从源码编译：
```bash
sudo make install       # 将所有文件安装进目录 /usr/local/ 下
```
若要安装进不同目录，可通过`prefix`实现，比如：
```bash
sudo make prefix=/opt/newsboat install 
```

### 2. 下载安装已编译好的软件
---

#### 2.1. Aur
Arch 用户可从 Aur 下载安装：
```bash
sudo pacman -S newsboat
```

#### 2.2. 软件仓库
- 各 Linux、BSD、和其他系统发行版的下载方式可参考[此清单](https://repology.org/project/newsboat)
- 从 [Snap](https://snapcraft.io/docs/installing-snapd) 下载安装
```bash
sudo snap install newsboat
```

## 使用
---
> 安装完成后，执行命令`newsboat`即可获得帮助信息。

### 1. 配置 Rss 源
---

关于如何寻找有趣的`Feeds`，可以参考 [RssHub](https://docs.rsshub.app/en/) 和 [Rss 订阅源推荐](https://rss-source.com/)。
此外，搭配 [RssHub Radar](https://chrome.google.com/webstore/search/rsshub%20radar?hl=en-US)插件，浏览器将自动识别所访问网站提供的`Rss Feed`。

#### 1. 添加
Newsboat 须要设置 Rss 源才可正常使用。<br>
- 编辑`$XDG_CONFIG_HOME/newsboat/urls`来添加 Rss 源（也可以按快捷键`E`），一行一个，比如：
```
https://archlinux.org/feeds/news
http://www.archdaily.cn/cn/feed
```

- 导入 OPML 文件：
```
newsboat -i blogroll.opml
```

#### 2. 整理
当 Rss feeds 达到一定数量后，若不加以整理，会十分混乱，可以编辑`$XDG_CONFIG_HOME/newsboat/urls`，其中，以`#`开头的字符会被注释，方便分类管理`Feeds`，比如：

![](https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/blogpics/A10-content-newsboat_url.png "Newsboat url 整理")

##### 2.1. 添加标签

为`Feed`添加标签：
```
# Linux
https://archlinux.org/feeds/news/     "Arch"  "linux"
...
```
在每条 Feed 后添加的、双引号内字符会被视为标签，`Newsboat`中，可使用快捷键`t`浏览

##### 2.2. 重命名
为`Feed`重命名：
```
# Linux
https://archlinux.org/feeds/news/      "Arch"  "linux" ~Arch_News
...
```
在每条 Feed 后添加的、`~`后的字符会被视为此`Feed`在`Newsboat`中所显示的名称

### 2. 配置 UI 界面
---

编辑`$XDG_CONFIG_HOME/newsboat/colour`，在其中写入配色代码，并在`$XDG_CONFIG_HOME/newsboat/config`中引用：

```
# 编辑 $XDG_CONFIG_HOME/newsboat/colour

#                        Font         Block
color background         default    default
color listnormal         color2     default
color listnormal_unread  color9     default  bold
color listfocus          color10    color4
color listfocus_unread   color1     color4   bold
color info               color8     default
color article            color15    default  bold

# highlights
highlight article "^(Title):.*$" blue default
highlight article "https?://[^ ]+" red default
highlight article "\\[image\\ [0-9]+\\]" green default

highlight all "---.*---" blue
highlight feedlist ".*(0/0))" black
highlight article "(^Feed:.*|^Title:.*|^Author:.*)" color2 default bold
highlight article "(^Link:.*|^Date:.*)" color1 default bold
highlight article "https?://[^ ]+" color4 default
highlight article "^(Title):.*$" color1 default bold
highlight article "\\[[0-9][0-9]*\\]" magenta default bold
highlight article "\\[image\\ [0-9]+\\]" green default bold
highlight article "\\[embedded flash: [0-9][0-9]*\\]" green default bold
highlight article ":.*\\(link\\)$" color1 default
highlight article ":.*\\(image\\)$" color2 default
highlight article ":.*\\(embedded flash\\)$" magenta default

feedlist-format "%?T?║%n %12u %t &╠ %t?"
highlight feedlist "[║│]" color3 default
highlight feedlist "╠.*" color3 default
```

在`$XDG_CONFIG_HOME/newsboat/config`中引用：
```
include ~/.config/newsboat/colour
...
```

![](https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/blogpics/A10-content-newsboat_view.png "Dracula 主题的 Newsboat")
主题配色最好与系统一致

### 3. 配置联动程序
---

仅有`Newsboat`本身是无法满足需求的，比如浏览图片、观看视频等等，这需要我们为各类型内容配置打开方式，比如：

- 图片：sxiv
- 视频：mpv
- 动图：mpv
- 其他：chromium

为此，需要编辑`$XDG_CONFIG_HOME/newsboat/config`：
```
include ~/.config/newsboat/dracula
...
browser linkhandler
macro , open-in-browser
macro t set browser "qndl" ; open-in-browser ; set browser linkhandler
macro a set browser "tsp yt-dlp --embed-metadata -xic -f bestaudio/best --restrict-filenames" ; open-in-browser ; set browser linkhandler
macro v set browser "setsid -f mpv" ; open-in-browser ; set browser linkhandler
macro w set browser "lynx" ; open-in-browser ; set browser linkhandler
macro c set browser "echo %u | xclip -r -sel c" ; open-in-browser ; set browser linkhandler
```

> 代码解释：
> - linkhandler 为判断脚本，我的判断脚本如下文所示，其位于用户的可执行文件基本目录下（~/.local/bin/)
> - 绑定不同快捷键手动指定浏览

我的 `linkhandler` 脚本：
```bash
if [ -z "$1" ]; then
	url="$(xsel -o)"
else
	url="$1"
fi

case "$url" in
	*mkv|*webm|*mp4|*youtube.com/watch*|*youtube.com/playlist*|*youtube.com/shorts*|*youtu.be*|*hooktube.com*|*bitchute.com*|*videos.lukesmith.xyz*|*odysee.com*|*bilibili.com*)
		setsid -f mpv -quiet "$url" >/dev/null 2>&1 ;;
	*png|*jpg|*jpe|*jpeg|*gif)
		curl -sL "$url" > "/tmp/$(echo "$url" | sed "s/.*\///;s/%20/ /g")" && sxiv -a "/tmp/$(echo "$url" | sed "s/.*\///;s/%20/ /g")"  >/dev/null 2>&1 & ;;
	*pdf|*cbz|*cbr)
		curl -sL "$url" > "/tmp/$(echo "$url" | sed "s/.*\///;s/%20/ /g")" && zathura "/tmp/$(echo "$url" | sed "s/.*\///;s/%20/ /g")"  >/dev/null 2>&1 & ;;
	*mp3|*flac|*opus|*mp3?source*)
		qndl "$url" 'curl -LO'  >/dev/null 2>&1 ;;
	*)
		[ -f "$url" ] && setsid -f "$TERMINAL" -e "$EDITOR" "$url" >/dev/null 2>&1 || setsid -f "$BROWSER" "$url" >/dev/null 2>&1
esac
```

### 4. 配置定时任务
---
可以将定时更新加入定时任务中，并使用`dunst`发送更新通知(需要为`dunst`设置开机自启)，我的定时任务脚本位于`$HOME/.local/bin/cron`下。
- 创建定时任务脚本：
```bash
nvim $HOME/.local/bin/cron/newsup
```

- 编辑脚本：
```bash
#!/bin/sh
dunstify -i terminal " Updating RSS..."
/usr/bin/newsboat -x reload
dunstify -i terminal " RSS update complete."
```

- 执行`crontab -e`，将脚本加入定时任务，每半小时执行一次更新：
```bash
*/30 * * * * /bin/bash ~/.local/bin/cron/newsup
```

##### 参考链接
1. [Newsboat](https://newsboat.org/) 
2. [Wiki RSS](https://en.wikipedia.org/wiki/RSS)
