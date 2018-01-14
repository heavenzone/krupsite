---
title: rmarkdown中使用python
author: Heaven Zone
date: '2018-01-14'
slug: python-in-rmarkdown-r
categories:
  - python
  - R
tags:
  - R
  - python
  - rmarkdown
simpletoc: true
---


## 目标

在Rstudio的rmarkdown中调用python并执行python代码。


## 使用方法

### 全局
  
    ```{r setup}
    library(knitr)
    opts_chunk$set(engine.path = '/usr/bin/python3')
    ```

### 局部chunk



    ```{python}
    import sys
    print(sys.version)
    ```


  

### 指定python版本  


    ```{python, engine.path = '/usr/bin/python3'}
    import sys
    print(sys.version)
    ```




## 参考链接

- <https://stackoverflow.com/questions/39069158/how-can-i-specify-rmarkdown-to-use-python3-instead-python-2>
- <https://www.jianshu.com/p/05dc80e104c3>
- <http://rpubs.com/badbye/python-knitr>