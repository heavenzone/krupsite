---
title: R语言扩展包的基本操作
author: Heaven Zone
date: '2018-06-07'
slug: r-package-instructions
categories:
  - R
tags:
  - R
  - package
description: R语言如何取消加载扩展包
simpletoc: true
---

# 加载扩展包


```r
library(ggplot2)
require(blogdown, quietly = TRUE)
```

```
## Warning: package 'blogdown' was built under R version 3.4.4
```


# 取消加载扩展包


```r
detach(name = "package:ggplot2")
```

# 删除已安装的扩展包

```
remove. packages(c("pkg1","pkg2") , lib = file .path("path", "to", "library"))
```

# 获取扩展包的帮助文档


```r
help(package = "ggplot2")
```
