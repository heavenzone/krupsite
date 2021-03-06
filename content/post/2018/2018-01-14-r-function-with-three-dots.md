---
title: R语言带三个点参数的函数怎么写
author: Heaven Zone
date: '2018-01-14'
slug: r-function-with-three-dots
categories:
  - R
tags:
  - R
description: 'r function with three dots'
simpletoc: true
---

## 问题

在R里面很多函数中参数处都是显示三个点的，怎么写这样的函数呢？

看了很多R语言的书貌似都没有详细介绍这一个，终于今天去图书馆的时候看到有一本书简单介绍了一下这三个点的参数的应用，主要是在A函数中调用B函数，而B函数的参数又要通过A函数来传递。

## 例子一


```r
funA <- function(a1 = 2, a2 = 2) {
  return(sum(a1, a2))
}

funB <- function(b1 = 3, b2 = 3) {
  return(sum(b1, b2))
}


funC <- function(c1, c2, ...) {
  tmp1 <- sum(c1, c2)
  print(tmp1)
  tmp2 <- funA(...)
  print(tmp2)
  rst <- tmp1 + tmp2
  return(rst)
}


funX <- function(x1, x2, ...) {
  tmp1 <- sum(x1, x2)
  print(tmp1)
  tmp2 <- funA(...)
  print(tmp2)
  tmp3 <- funB(...)
  print(tmp3)
  rst <- tmp1 + tmp2 + tmp3
  return(rst)
}

funC(4,4,2)
```

```
## [1] 8
## [1] 4
```

```
## [1] 12
```

```r
# 第三第四两个参数同时传递给funA和funB
funX(4,4,3,3)
```

```
## [1] 8
## [1] 6
## [1] 6
```

```
## [1] 20
```

```r
funX(4,4,3)
```

```
## [1] 8
## [1] 5
## [1] 6
```

```
## [1] 19
```

## 例子二：改进funX


```r
funD <- function(d1 = 3, d2 = 3, ...) {
  tmp1 <- sum(d1, d2)
  print(tmp1)  
  tmp2 <- funA(...)
  print(tmp2)
  return(sum(tmp1, tmp2))
}

funD(3, 3, 4, 4)
```

```
## [1] 6
## [1] 8
```

```
## [1] 14
```

```r
funY <- function(y1 = 4, y2 = 4, ...) {
  tmp1 <- sum(y1, y2)
  print(tmp1)  
  tmp2 <- funD(...)
  print(tmp2)
  return(sum(tmp1, tmp2))
}

funY(2,2,3,3)
```

```
## [1] 4
## [1] 6
## [1] 4
## [1] 10
```

```
## [1] 14
```

```r
funY(2,2,4)
```

```
## [1] 4
## [1] 7
## [1] 4
## [1] 11
```

```
## [1] 15
```

```r
funY(2,2,1,1,5,5)
```

```
## [1] 4
## [1] 2
## [1] 10
## [1] 12
```

```
## [1] 16
```


