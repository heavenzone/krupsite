---
title: ArchLinux安装记录（精简版）
author: Heaven Zone
date: '2011-02-12'
slug: archlinux-installation-simple-note
categories:
  - Linux
tags:
  - ArchLinux
description: 'ArchLinux安装记录（精简版）'
topics: []
---


##  步骤一 (安装前准备)

###  Grub4dos启动-硬盘安装

```
map --heads=0 --sectors-per-track=0 (hd0,0)/archlinux-2010.05-netinstall-x86_64.iso (0xff)
map --hook
chainloader (0xff)
boot

```



```
mkdir /win
mount -r -t ntfs /dev/sda1 /win
modprobe loop
losetup /dev/loop6 /win/archlinux-2010.05-netinstall-x86_64.iso
ln -s /dev/loop6 /dev/disk/by-label/ARCH_201005

Ctrl+D继续

```


用root登陆，启动/arch/setup脚本开始安装

##  步骤二 (安装过程)
###   分区，选择安装包

安装源选择cd，时间设置选择，local，分区，使用ext4格式，设置挂在点.

选择软件包，安装base,base-devel，再选择wireless_tools和无线驱动zd1211-firmware，然后安装软件包。

###  配置更新源

```
vi /etc/pacman.conf
```

添加
```

[archlinuxfr]
Server = http://repo.archlinux.fr/i686
```


###  更新系统

```

pacman -Syu
pacman-optimize
pacman -Sy yaourt

```


###  安装常用软件包

```
pacman -S yaourt gvim sudo alsa-utils alsa-oss xorg xf86-input-evdev xf86-input-keyboard xf86-input-mouse ttf-dejavu ttf-bitstream-vera wqy-zenhei hal gamin openbox roxterm pcmanfm scite wicd firefox deadbeef smplayer ibus ibus-sunpinyin nvidia

```



##  步骤三（配置过程）
###  配置X

```
nvidia-xconfig
```


###  添加用户

```

useradd -m -G audio,video,floppy,lp,optical,network,storage,wheel,dbus,hal,users -s /bin/bash heaven
passwd heaven

```


###  配置桌面启动

```

su - heaven
cp /etc/skel/.xinitrc ~/

vi /home/heaven/.xinitrc
添加行
export LANG="zh_CN.UTF-8"
exec openbox-session

```


###  配置sudo

```

export VISUAL=vim
visudo
编辑这行
USER_NAME   ALL=(ALL) ALL

```


###  配置声音

```

在/etc/modprobe.d /modprobe.conf中加入这行
options snd-pcsp index=2

alsamixer
用左右光标键移动到Master和PCM声道上，按M取消掉静音。用向上键增加音量
aplay /usr/share/sounds/alsa/Front_Center.wav
alsactl store
vi /etc/rc.conf
修改DAEMONS=(syslog-ng network crond alsa)

```


###  配置 input设备热插拔

```

vi /etc/rc.conf
把hal添加到rc.conf的daemon

```


###  配置SciTE

```

修改SciTEGlobal.properties解决在scite输入中文的问题
修改code.page
code.page=65001

将
#LC_CTYPE=en_US.UTF-8
修改为
LC_CTYPE=zh_CN.UTF-8

```


###  配置/etc/fstab自动挂载ntfs分区

```

/dev/sda1 /krup/xp_c ntfs-3g rw,auto,async,iocharset=utf8,noatime 0 1
/dev/sda9 /krup/xp_d ntfs-3g rw,auto,async,iocharset=utf8,noatime 0 1
/dev/sda10 /krup/share_disk ntfs-3g rw,auto,async,iocharset=utf8,noatime 0 1

```

