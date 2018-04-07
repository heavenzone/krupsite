---
title: "重装Win10后设置tinytex"
author: Heaven Zone
date: '2018-03-12'
slug: set-up-tinytex-after-reinstall-win10
categories:
  - R
tags:
  - R
  - tinytex
---



今天重装了系统Win10，然后重装Rstudio，又安装tinytex，然后：

```r
tinytex::install_tinytex()
```

死活连接不上ctan下载那个zip文件，由于重装系统前已经把`tinytex`文件夹备份了，我想应该直接重新设置一下路径就可以了吧，于是把`tinytex`文件夹复制到了`c:`根目录，尝试：

```
C:\tinytex\bin> .\win32\tlmgr path add
```

出现下面错误：

```
Use of uninitialized value in bitwise or (|) at C:\tinytex\texmf-dist\scripts\texlive\tlmgr.pl line 1446.
```

最后尝试把texlive路径`C:\tinytex\bin\win32`添加到系统环境path，成功。



