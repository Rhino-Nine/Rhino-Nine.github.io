---
title: "Archlinux 小记-009 实用软件——mutt"
date: 2023-05-29
draft: false
description: "25年经久不衰的 TUI 邮件客户端"
images: []

tags: ["Archlinux", "Programs"]
categories: [Linux]

featuredImage: "https://images.ariesnine.cn/blogpics/p011-cover-mutt.png"
featuredImagePreview: ""

hiddenFromHomePage: false
hiddenFromSearch: false
lightgallery: true
---

<!--more-->

> [mutt](http://www.mutt.org/)本身是邮件用户代理，围绕它可以构建一个简洁而强大的 TUI 邮件客户端。

[mutt-widzard](https://muttwizard.com/)是一个自动配置`mutt`邮件系统的项目，它能实现：

1. 遵循 Unix Philosophy 配置完整的电子邮件体系，用户甚至无需了解这些程序。
1. 使用 isync 下载邮件，离线存储，自动加密
1. 使用 notmuch 搜索邮件
1. 使用 abook 索引联系人
1. 多账户，支持 Gmail，Proton Mail

## 安装
---

### 安装 mutt-widzard
- Aur 安装
sudo pacman -S mutt-widzard 
- git 安装
git clone https://github.com/lukesmithxyz/mutt-wizard
cd mutt-wizard
sudo make install

### 安装依赖包
- neomutt
- abook
- curl
- isync
- lynx
- msmtp
- mpop
- notmuch
- pass
- urlview

## 使用
---

### 创建 GPG 密钥对和 pass 密码库
使用`mutt-widzard`前，确保已存在 GPG 密钥对，若没有，使用`gpg --full-gen-key`创建：

![gpg --full-gen-key](/images/p011-content-gpg_generate.gif "生成GPG密钥对")

生成`GPG`密钥对时，所使用的邮箱地址`myemail@server`与接下来需要在Neomutt中添加的个人邮箱账户没有任何关系。

生成`GPG`密钥库后，使用`pass init myemail@server`创建密码库

### 添加邮箱账户
首先确保预添加邮箱已启用`IMAP`或者`POP`服务，其次，若`outlook`和`Gmail`邮箱账户，需要使用`app password`作为密码才可成功配置。

准备完成后，使用以下命令添加邮箱：

```bash
mw -a example@email.com -x "app password" -f
```

> 命令解释：
> - mw —— mutt-widzard 
> - -a example@email.com —— 添加邮箱账户
> - -x "app password" —— 填写邮箱密码
> - -f —— 还没懂，不加这个参数，邮箱就添加失败

### 自定义侧边栏邮件文件夹
当侧边栏存在不需要跟踪变化的邮件文件夹时，可以通过编辑`$XDG_CONFIG_HOME/mutt/accounts/example@mail.com.muttrc`，使用`named-mailboxes`定义显示和隐藏它们。

```muttrc 
...
set folder = "/home/user/.local/share/mail/example@mail.com"
...
named-mailboxes "Inbox" "+INBOX"
named-mailboxes "Sent" "+Sent"
named-mailboxes "Drafts" "+Drafts"
named-mailboxes "Trash" "+Trash"
...
```

> 配置文件解释：
> - 在`mutt`配置文件的变量中，`+`=`$folder`，即`+INBOX`=`/home/user/.local/share/mail/example@mail.com/INBOX`
> - named-mailboxes "自定义邮箱文件夹名" "邮箱文件夹路径"
> - 其余文件夹不会显示

### neomutt 基本操作
neomutt 快捷键基本属于 vim 类，常用快捷键有：
- o 下载邮件
- ctrl+f 搜索
- m 新建邮件
- r 回复邮件
- ? 显示帮助
