---
post: A-09
title: "Archlinux 小记-009 实用软件——mutt"
date: 2023-05-31
draft: false
description: "25年经久不衰的 TUI 邮件客户端"
images: []

tags: ["Archlinux", "Programs"]
categories: [Linux Tools]

featuredImage: "https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/blogpics/p011-cover-mutt.png"
---

<!--more-->

> [mutt](http://www.mutt.org/)本身是邮件用户代理，围绕它可以构建一个简洁而强大的 TUI 邮件客户端。

[mutt-widzard](https://muttwizard.com/)是一个自动配置`mutt`邮件系统的项目，它能实现：

1. 遵循 Unix Philosophy 配置完整的电子邮件环境
1. 使用 isync 下载邮件，离线存储，自动加密
1. 使用 notmuch 搜索邮件
1. 使用 abook 索引联系人
1. 支持 Gmail，Proton Mail，Outlook 等
1. 支持多账户

## 安装
---

### 安装 mutt-widzard
- Aur 安装
```bash
sudo pacman -S mutt-widzard 
```

- git 安装
```bash
git clone https://github.com/lukesmithxyz/mutt-wizard
cd mutt-wizard
sudo make install
```

### 安装依赖包
- neomutt - 邮件客户端
- abook - 通讯录
- curl - 检测邮箱信息
- isync - 离线保存邮件，若使用 POP 服务则不必要
- lynx - 格式化 HTML 邮件
- msmtp - 发送邮件
- mpop - 为 POP 服务提供支持
- notmuch - 搜索邮件
- pass - 保存密码，配合 GPG 加密
- urlview - 识别邮件中的 url 链接

## 使用
---

### 创建 GPG 密钥对和 pass 密码库
使用`mutt-widzard`需要 GPG 密钥对，若没有，使用`gpg --full-gen-key`创建：

![gpg --full-gen-key](https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/blogpics%2Fp011-content-gpg_generate.gif "生成GPG密钥对")

生成`GPG`密钥对时，所使用的邮箱地址`myemail@mail.comn`与接下来需要在Neomutt中添加的个人邮箱账户`example@mail.com`没有任何关系。

生成`GPG`密钥库后，使用`pass init myemail@mail.com`创建密码库

### 添加邮箱账户
首先确保邮箱已启用`IMAP`或者`POP`服务，其次，若`outlook`和`Gmail`邮箱账户，需要使用`app password`作为密码才可成功配置。

准备完成后，使用以下命令添加邮箱：

```bash
mw -a example@email.com -x "app password" -f
```

> 命令解释：
> - mw —— mutt-widzard 
> - -a example@email.com —— 添加邮箱账户
> - -x "app password" —— 填写邮箱密码
> - f 禁用 curl 检测邮箱信息

### 自定义侧边栏邮件文件夹
通过编辑`$XDG_CONFIG_HOME/mutt/accounts/example@mail.com.muttrc`，使用`named-mailboxes`显示和隐藏侧边栏的邮件文件夹：

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
