---
title: 修复win10在grub2中的启动项
author: Heaven Zone
date: '2018-06-28'
slug: fix-win10-booting-on-grub2
categories:
  - win10
tags:
  - win10
  - grub2
---

最近升级了Manjaro，关机后都没用Manjaro，而一直在用win10，后来想进入manjaro，发现进入不了，修复后，发现grub2启动项不见了win10的启动项，于是要把它修复修复，在百度上搜索了一下关键字`grub2 win10`，貌似没有什么是真正有用的，有些描述也不够详细，最后还是用bing搜索了一下英文网页，还是解决了。


主要用到了下面命令：

```shell
sudo os-prober
sudo update-grub
```

先`sudo os-prober`有输出，说明可以自动检测到win10系统了，接着就用`update-grub`更新`/boot/grub/grub.cfg`文件。

```shell
[heaven@heaven-dell grub]$ sudo os-prober 

[sudo] password for heaven: 
/dev/sda2@/EFI/Microsoft/Boot/bootmgfw.efi:Windows Boot Manager:Windows:efi


[heaven@heaven-dell grub]$ sudo update-grub 

Generating grub configuration file ...
Found background: /usr/share/grub/background.png
Found linux image: /boot/vmlinuz-4.9-x86_64
Found initrd image: /boot/intel-ucode.img /boot/initramfs-4.9-x86_64.img
Found initrd fallback image: /boot/initramfs-4.9-x86_64-fallback.img
Found Windows Boot Manager on /dev/sda2@/EFI/Microsoft/Boot/bootmgfw.efi
Found memtest86+ image: /boot/memtest86+/memtest.bin
done
```

检查`/boot/grub/grub.cfg`文件，发现有这么一段了：

```
### BEGIN /etc/grub.d/30_os-prober ###
menuentry 'Windows Boot Manager (on /dev/sda2)' --class windows --class os $menuentry_id_option 'osprober-efi-CC75-8D3F' {
	savedefault
	insmod part_gpt
	insmod fat
	set root='hd0,gpt2'
	if [ x$feature_platform_search_hint = xy ]; then
	  search --no-floppy --fs-uuid --set=root --hint-bios=hd0,gpt2 --hint-efi=hd0,gpt2 --hint-baremetal=ahci0,gpt2  CC75-8D3F
	else
	  search --no-floppy --fs-uuid --set=root CC75-8D3F
	fi
	chainloader /EFI/Microsoft/Boot/bootmgfw.efi
}
### END /etc/grub.d/30_os-prober ###
```

上面这一段是`update-grub`自动调用`os-prober`生成的，这样也不用手动修改`grub.cfg`文件了。

如果用`update-grub`命令后没有自动添加上面那一段，可能还要修改`/etc/default/grub`文件，看看有没有`GRUB_DISABLE_OS_PROBER`项，没有就添加一行并设置为`true`。

即：

```
GRUB_DISABLE_OS_PROBER=false
```


## 参考资料

- <https://forum.manjaro.org/t/grub-cannot-find-windows-10/10381>
