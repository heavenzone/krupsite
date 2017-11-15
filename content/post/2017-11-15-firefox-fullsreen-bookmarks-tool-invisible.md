---
title: firefox全屏模式下显示书签工具栏
author: Heaven Zone
date: '2017-11-15'
slug: firefox-fullsreen-bookmarks-tool-invisible
categories:
  - firefox
tags:
  - firefox
  - bookmarks
description: 'firefox全屏模式下显示书签工具栏的解决方法'
topics: []
---


## 问题描述

因为装了manjaro-i3系统，用firefox的时候经常要按`mod + f`将firefox全屏，但是发现全屏的时候，书签工具栏却跟我们玩捉迷藏，搜索了一下，找到参考资料的两个链接，但是按照第一个链接去做了，发现不行，于是又找到了第二个链接照做了，发现书签工具栏奇迹般出现了，下面记录一下步骤。

## 解决步骤

- 在firefox的地址栏输入`about:support`
- 在打开的页面找到`应用程序概要`表格的大概**第8行**`配置文件夹`，点`打开目录`
    - 在manjaro系统里面其实就是目录`~/.mozilla/firefox/<******>.default`
- 找到`chrome`文件夹，
    - 这个chrome跟google的浏览器**没有关系**
    - 其实这个文件夹是连接文件：
    - chrome -> /usr/share/themes/Firefox/Vertex-Maia-Dark/chrome/
    - 难怪修改文件的时候要**root权限**
- 在`chrome`文件夹下找到`userChrome.css`

用root权限打开`userChrome.css`，在适当的位置添加下面代码：

```css
@namespace url("http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul"); /* only needed once */

/* full screen toolbars */
#navigator-toolbox[inFullscreen] toolbar:not([collapsed="true"]) {
   visibility:visible!important;
}
```

其实`@namespace`这样行代码已经存在于该文件当中了，所以只需要复制下面那一段就行了，当然如果`userChrome.css`不存在或者不存在那句`@namespace`，就新建这个文件，然后两段代码全部复制进去吧。

打开firefox，按`F11`进入全屏模式，书签工具栏**出来了**。

## 参考资料

1. <https://askubuntu.com/questions/639206/firefox-show-bookmark-toolbar-in-fullscreen-mode>
2. <https://shinglyu.github.io/web/2016/06/20/firefox_bookmark_toolbar_in_fullscreen.html>



