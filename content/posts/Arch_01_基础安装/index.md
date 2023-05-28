---
posts: 001
title: Archlinux 小记-001 基础安装
date: 2023-05-17
draft: false
description: 总结Archlinux基础安装技巧 
image: []
featuredImage: https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/blogpics/p001-cover-Arch_Linux.jpg?q-sign-algorithm=sha1&q-ak=AKIDpiaP8oO0V1VH1eAa5Up7c4Unz42rW5vh&q-sign-time=1685270547;8999999999&q-key-time=1685270547;8999999999&q-header-list=host&q-url-param-list=&q-signature=7a6e12f71aacf9a20fb863cce41c85da42899633
tags: 
- Archlinux
---

<!--more-->

## 一、安装准备
---

![test](https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/blogpics/p001-cover-Arch_Linux.jpg?q-sign-algorithm=sha1&q-ak=AKIDpiaP8oO0V1VH1eAa5Up7c4Unz42rW5vh&q-sign-time=1685270547;8999999999&q-key-time=1685270547;8999999999&q-header-list=host&q-url-param-list=&q-signature=7a6e12f71aacf9a20fb863cce41c85da42899633 "test")

![test 2](http://rvald11p2.hn-bkt.clouddn.com/blogpics/p001-cover-Arch_Linux.jpg "test2")

![test 3](https://blogpics-1309094537.cos.ap-guangzhou.myqcloud.com/p001-cover-Arch_Linux.jpg)

### 1. 物理机
1. 确认网络连接通畅
2. 下载安装镜像
3. 刻录安装U盘
### 2. 虚拟机
请参考：[虚拟机安装前的准备](https://arch.icekylin.online/guide/rookie/pre-virt.html)

## 二、基础安装
---

### 1. 禁用reflector服务
```bash
systemctl stop reflector.service
```

### 2. 确认UEFI模式
```bash 
ls /sys/firmware/efi/efivars/
```

### 3. 连接网络
```bash
rfkill unblock wifi
iwctl
device list 
station wlan0 scan
station wlan0 get-networks
station wlan0 connect wifi-name
exit
ping www.bilibili.com #测试网络
```

### 4. 更新系统时钟
```bash
timedatectl set-ntp true # 将系统时间与网络时间进行同步
timedatectl status # 检查服务状态
```

### 5. 换源
编辑源文件
```bash
vim /etc/pacman.d/mirrorlist
```
推荐源如下，择其一添加即可
```bash
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch # 中国科学技术大学开源镜像站
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch # 清华大学开源软件镜像站
Server = https://repo.huaweicloud.com/archlinux/$repo/os/$arch # 华为开源镜像站
Server = http://mirror.lzu.edu.cn/archlinux/$repo/os/$arch # 兰州大学开源镜像站
```

## 6. 分区与格式化
---

### 6-1. win10、Arch双系统Btrfs文件系统分区
 
- `/` + `/home` 根目录+主目录：`>= 128GB`
- `/boot` EFI分区：`512MB`
- Swap 分区：>= `60%`运存

SATA协议硬盘中，`x`为字母`a`、`b`、`c`等；NVME协议硬盘中，`x`为数字`1`、`2`、`3`等
```bash
cfdisk /dev/nvmexn1 #按需求分配内存
fdisk -l #完成后，复查分区情况
```

### 6-2. 格式化并创建Btrfs子卷
```bash
mkfs.fat -F32 /dev/nvmexn1pn #格式化EFI分区
mkfs.btrfs -L myArch /dev/nvmexn1pn #格式化Btrfs分区

mount -t btrfs -o compress=zstd /dev/nvmexn1pn /mnt #挂载 Btrfs 分区于 /mnt 下
btrfs subvolume create /mnt/@ #创建 / 目录子卷
btrfs subvolume create /mnt/@home #创建 /home 目录子卷

umount /mnt #卸载 /mnt 以挂载子卷
```
## 7. 挂载
---

```bash
# 按顺序挂载
mount -t btrfs -o subvol=/@,compress=zstd /dev/nvmexn1pn /mnt #挂载 / 目录
mkdir /mnt/home #创建 /home 目录
mount -t btrfs -o subvol=/@home,compress=zstd /dev/nvmexn1pn /mnt/home #挂载 /home 目录
mkdir -p /mnt/boot #创建 /boot 目录
mount /dev/nvmexn1pn /mnt/boot #挂载 /boot 目录
swapon /dev/nvmexn1pn #挂载交换分区
```

## 8.安装系统
---

1. 若镜像非最新，可能出现GPG证书错误，更新`archlinux-keyring`以解决
```bash
pacman -S archlinux-keyring
```

2. 安装必要软件
```bash
pacstrap /mnt dhcpcd networkmanager neovim sudo zsh zsh-completions 
```

## 9. 生成fstab文件
---

1. 生成并写入 fstab 文件:
```bash
genfstab -U /mnt > /mnt/etc/fstab
```

2. 复查`/mnt/etc/fstab`
```bash
cat /mnt/etc/fstab
```

## 10. change root
---

```bash
arch-chroot /mnt #切换系统环境至新系统
```

## 11. 收尾配置
---

1. 设置主机名
```bash
nvim /etc/hostname
```

2. 配置`/etc/hosts`
```bash
nvim /etc/hosts
```
加入以下内容
```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   myarch.localdomain myarch
```

3. 设置时间
```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime #设置时区
hwclock --systohc #同步硬件时间
```

4. Locale本地化
```bash
nvim /etc/locale.gen #编辑 /etc/locale.gen
    en_US.UTF-8 UTF-8 #取消注释
    zh_CN.UTF-8 UTF-8 #取消注释

locale-gen #生成 locale

echo 'LANG=en_US.UTF-8'  > /etc/locale.conf
```

5. 为root用户设置密码
```bash
passwd root
```

6. 安装微码
```bash
pacman -S intel-ucode # Intel
pacman -S amd-ucode # AMD
```

7. 安装引导程序
```bash
pacman -S grub efibootmgr os-prober #安装对应包
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=Arch #安装 GRUB 到EFI 分区
echo 'GRUB_DISABLE_OS_PROBER=false' > /etc/default/grub #引导win10

grub-mkconfig -o /boot/grub/grub.cfg #生成 GRUB 配置文件
```

8. 禁用蜂鸣器
```bash
touch /etc/modprobe.d/nobeep.conf
echo 'blacklist pcspkr' > /etc/modprobe.d/nobeep.conf
```

## 12. 完成安装
---

```bash
exit #退回安装环境
umount -R /mnt #卸载新分区
reboot #重启
```

__参考链接__
---
1. [ArchWiki Installation guide](https://wiki.archlinux.org/title/Installation_guide) 
2. [Archlinux 简明指南](https://arch.icekylin.online)
