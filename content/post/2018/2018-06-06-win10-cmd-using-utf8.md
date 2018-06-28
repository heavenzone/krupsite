---
title: win10 cmd工具使用utf8字符集
author: Heaven Zone
date: '2018-06-06'
slug: win10-cmd-using-utf8
categories:
  - windows
tags:
  - cmd
  - windows
---


在win10下用**mycli**连接mysql，发现查询出来的中文乱码，后来知道原来是win10的cmd工具不支持utf8，又搜索了一下，发现可以直接把cmd的编码变成utf8，在cmd下输入：

```
chcp 65001
```

然后输出结果正常。