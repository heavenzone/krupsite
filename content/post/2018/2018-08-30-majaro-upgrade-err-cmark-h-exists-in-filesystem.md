---
title: majaro升级错误cmark.h exists in filesystem
author: Heaven Zone
date: '2018-08-30'
slug: majaro-upgrade-err-cmark-h-exists-in-filesystem
categories:
  - linux
tags:
  - linux
  - manjaro
keywords: ''
description: ''
show_toc: no
mathjax: no
---

## 问题

今天进入manjaro升级系统，升级过程出现如下错误：

```error
error: failed to commit transaction (conflicting files)
cmark-gfm: /usr/include/cmark.h exists in filesystem (owned by cmark) 
cmark-gfm: /usr/include/cmark_export.h exists in filesystem (owned by cmark)
cmark-gfm: /usr/include/cmark_version.h exists in filesystem (owned by cmark)
Errors occurred, no packages were upgraded.
```

## 解决方法

解决办法很简单，卸载`haskell-cmark`，然后安装`haskell-cmark-gfm`。

```
sudo pacman -Rns haskell-cmark
sudo pacman -S haskell-cmark-gfm

```

做完这两步后，升级成功。

## 参考资料

- [Conflicting files during upgrade: cmark-gfm and cmark in conflict](https://bbs.archlinux.org/viewtopic.php?id=239750)