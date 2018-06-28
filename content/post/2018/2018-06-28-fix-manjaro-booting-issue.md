---
title: 修复manjaro启动问题
author: Heaven Zone
date: '2018-06-28'
slug: fix-manjaro-booting-issue
categories:
  - Linux
tags:
  - Linux
  - manjaro
---


前不久升级了manjaro系统，关机后就没用了，最近想进入该系统，发现进不了，出现下面错误：

> error: device uuid="000000000000000" not found. Skipping fsck.


如下图所示：

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/2018/2018-06-28-manjaro-00.jpg)

开头还震惊了一下，不会是硬盘分区坏了吧？搜索了一下，知道可能是启动模块的问题，才镇定下来，用manjaro livecd进入了系统发现正常检测到manjaro系统盘，正常mount，数据没问题，好看来真的是升级的时候生成的`initrd`文件有问题，解决方法如下：

```shell
# 用livecd进入系统
# sudo mount /dev/sda3 /mnt
# sudo mount /dev/sda1 /mnt/boot
# cd /mnt
# sudo mount -t proc /proc proc/
# sudo mount --rbind /sys sys/
# sudo mount --rbind /dev dev/
# sudo chroot /mnt
# sudo pacman -S linux
```

第一次昨晚这个步骤之后，重启发现还是不能进入系统，重新上面步骤，不过在`chroot /mnt`后决定更新系统`sudo pacman -Syu`，但是发现不能连上网，ping不同外网，于是修改`/etc/resolv.conf`，添加两行

```
nameserver 202.96.128.166
nameserver 202.96.128.86
```

可以联网了，然后就可以`sudo pacman -Syu`了，升级系统后，重启正常。

其实`sudo pacman -Syu`已经是做了`pacman -S linux`步骤了。

另外参考资料里面也有一个连接说可以用`mkinitcpio -p linux`来修复，不过我在前面试过不行，估计是升级有问题的那一次升级下载下来的头文件（猜的）有问题。

不管怎么样，最终还是修复了，没有丢失数据，也不用重装系统，^_^。

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/2018/2018-06-28-manjaro-01.jpg)

### 参考资料

- <https://bbs.archlinux.org/viewtopic.php?id=167109>
- <http://bbs.archlinuxcn.org/viewtopic.php?id=1180>
- <https://wiki.archlinux.org/index.php/Change_root>