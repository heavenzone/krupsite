---
title: Excel左开右闭分箱
author: Heaven Zone
date: '2007-09-01'
slug: excel-group-left-open-right-closed
categories:
  - excel
tags:
  - excel
  - vlookup
  - 分箱
  - match
description: ''
---

## 简介

在数据处理中经常需要对数据进行分箱处理，对于左闭右开的情况很容易通过vlookup的模糊查找实现，而对于左开右闭则不行，因为vlookup只能从小到大查找，没有参数设置方向，而match函数可以通过设置参数设置查找的方向，于是左开右闭的实现可以通过index函数和match函数的组合来实现。

## vlookup: 左闭右开

![](https://gitee.com/heavenzone/picturebed/raw/master/excel2r/excel-20180222-001-vlookup.png)


## index & match: 左开右闭

![](https://gitee.com/heavenzone/picturebed/raw/master/excel2r/excel-20180222-002-index-match.png)

## 参考资料

- <http://club.excelhome.net/thread-1315840-1-1.html>