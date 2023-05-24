---
No.: 004
keywords:
- Archlinux 本地化
- Archlinux 中文设置
- Archlinux 输入法
- Fcitx5 配置
- 四叶草输入法配置
title: Archlinux 小记-003 本地化和输入法
date: 2023-05-20
description: 介绍如何本地化 Archlinux 系统，并安装Fcitx5+rime+四叶草输入法，以及添加自定义词库
tags: 
- Archlinux
---

## 一、本地化
---

### 1.1. 安装 noto 字体家族
---

Archlinux 在没有安装任何中文字体的情况下，中文会显示为一个个空白方框，神似豆腐块，建议安装 Google 开发的开源字体家族 [Noto](https://en.wikipedia.org/wiki/Noto_fonts)，其全称为“No Toufu”，旨在消除所有无法显示的字符。
```bash
sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-extra noto-fonts-emoji
```

### 1.2. 安装 windows 常用字体
---

- 建立存放字体的文件夹
```bash
sudo mkdir ~/.local/share/fonts/WindowsFonts
```
- 管理权限
```bash
sudo chmod 755 /usr/share/fonts/WindowsFonts/*
```
- 手动下载好字体，比如 windows 中的常用字体，并放入字体目录下。
> 用户字体目录: `~/.local/share/fonts`
> 
> 系统字体目录: `/usr/share/fonts`

### 1.3. 刷新字体缓存
---

```bash
fc-cache -vf
```

## 二. 输入法
---

推荐使用 [Fcitx5](https://fcitx-im.org/wiki/Fcitx_5)。
> fcitx5，读作 [ˈfaɪtɪks]，中文名“小企鹅输入法5”，是一个支持扩展的输入法框架，通过输入法引擎支持全世界大量语言。

### 2.1. 安装
```bash
sudo pacman -S fcitx5-im #输入法基础包组
sudo pacman -S fcitx5-rime rime-cloverpinyin #四叶草输入法
sudo pacman -S fcitx5-pinyin-moegirl-rime #萌娘百科词库
sudo pacman -S fcitx5-zhwiki #中文维基 
sudo pacman -S fcitx5-material-color # 输入法主题
```

### 2.2. 配置

#### 2.2-1 配置环境变量
```bash
# 编辑：
sudo nvim /etc/environment
# 写入环境变量：
GTK_IM_MODULE=fcitx5
QT_IM_MODULE=fcitx5
XMODIFIERS="@im=fcitx5"
SDL_IM_MODULE=fcitx5
GLFW_IM_MODULE=ibus
```

#### 2.2-2 配置[四叶草输入法](https://github.com/fkxxyz/rime-cloverpinyin)
- **安装必要包**
```bash
sudo pacman -S fcitx5-qt fcitx5-gtk #安装 qt 和 gtk 支持
sudo pacman -S fcitx5-configtool #安装图形化配置工具
```

- **打开配置工具**
```bash
fcitx5 -d #启动 fcitx5
st -e fcitx5-configtool #从终端启动配置工具
```

- **添加中文输入法**
1. 在右侧搜索框中搜索“rime”，并将搜索结果“rime”添加到左侧。
2. 编辑 clover 配置文件
```bash
mkdir ~/.local/share/fcitx5/rime #创建 rime 目录
nvim ~/.local/share/fcitx5/rime/default.custom.yaml #编辑配置文件
#在文件中写入：
patch:
    "menu/page_size": 8
    schema_list:
    - schema: clover
#保存退出
```
3. 重启输入法，使用 super + space 切换输入法，测试各个窗口中的输入法状态是否正常

- **添加词库**
1. 确保词库文件 xxx.dict.yaml 位于可读取目录下，比如 /usr/share/rime-data
2. 编辑 clover 词库配置文件：
```bash
nvim ~/.local/share/fcitx5/rime/clover.dict.yaml #若文件不存在，从 /usr/share/rime-data/ 处复制
# cp /usr/share/rime-data/clover.dict.yaml ~/.local/share/fcitx5/rime/clover.dict.yaml
```
在 import-table 中加入词库文件的名称 xxx，省去后缀 .dict.yaml:
```bash
import_tables:
  ...
  ...
  - moegirl
  - zhwiki
```
3. 重启输入法，测试词库。

__参考链接__ 
---
1. [四叶草输入法 Wiki ](https://github.com/fkxxyz/rime-cloverpinyin/wiki)
2. [Archwiki](https://wiki.archlinuxcn.org/wiki/Fcitx5)
