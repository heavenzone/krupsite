---
title: 去掉firefox书签工具栏的图标
author: Heaven Zone
date: '2017-11-16'
slug: make-firefox-bookmarks-icon-invisible
categories:
  - firefox
tags:
  - firefox
description: 'How do I hide my FireFox bookmark icons
      in my Bookmarks toolbar?'
---


## 问题描述

书签工具栏收藏的书签太多了，分类就有二十多个的分类，就算把firefox全屏，都放不下全部的书签，要点后面的箭头才能打开一些隐藏在后面的书签。

把前面的图标就可以节省大部分的空间了，那么怎么才能去掉前面的文件夹图标呢？

## 解决方法

编辑文件(如果没有就创建一个)： 

- linux: `~/.mozilla/firefox/<*******>.default/chrome/userChrome.css`
- win10: `%APPDATA%\Roaming\Mozilla\Firefox\Profiles\<******>.default`

在适当位置插入下面代码：

```css
/*隐藏 书签工具栏 的 文件夹图标*/
/*How do I hide my FireFox bookmark icons
      in my Bookmarks toolbar?*/
#personal-bookmarks .toolbarbutton-icon {
  display: none !important;
}
toolbarbutton.bookmark-item {
  /*-moz-appearance: button !important;*/
  margin: 0 2px !important;
}
toolbarbutton.bookmark-item:hover {
  -moz-border-left-colors: #FF6666 !important;
  -moz-border-right-colors: #FF6666 !important;
  -moz-border-top-colors: none !important;
  -moz-border-bottom-colors: none !important;
}
```



