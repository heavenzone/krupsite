---
title: 用ggplot2对数学函数进行画图
author: Heaven Zone
date: '2017-11-21'
slug: r-ggplot2-draw-using-stat-function
categories:
  - R
tags:
  - R
  - ggplot2
description: '怎么对正态分布函数填充拒绝区域?'
---




## 前言

学了一段时间的R和ggplot2，一直很想知道书中那些正态分布图两侧的拒绝域是怎么填充颜色的，找到这篇文章[Creating plots in R using ggplot2 - part 9: function plots](http://t-redactyl.io/blog/2016/03/creating-plots-in-r-using-ggplot2-part-9-function-plots.html)之后才理清了思路。


## 基础正态分布曲线

首先我们一个ggplot基础layer，x轴范围在[-4, 4]（当然你可以设置任何你想设置的范围），然后把这个范围值传递给aes的x，然后我们添加`stat_function`选项，然后把函数dnorm传给stat_function的fun参数，这样我们就可以画出一条漂亮的正态分布曲线了。


```r
require(ggplot2)
ggplot(data.frame(x = c(-4, 4)), aes(x = x)) +
  stat_function(fun = dnorm)
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-1-1.png" width="672" />


## 基础t分布曲线

**stat_function**可以画很多连续型的概率密度函数，包括t(dt)，F(df)和Chi-square(dchisq)等等。现在我们这里再画一个t分布函数曲线图，t分布曲线的形状取决于自由度，所以我们需要指定自由度的值给`df`参数，然后传递给dt函数。


```r
ggplot(data.frame(x = c(-4,4)), aes(x = x)) +
  stat_function(fun = dt, args = list(df = 8))
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-2-1.png" width="672" />


## 画我们自己的函数曲线

我们也可以画我们自己的函数，我们只需要通过公式将x值计算出y值。


```r
cubeFun <- function(x) {
  x^3 * 0.5
}

ggplot(data.frame(x = c(-4, 4)), aes(x = x)) +
  stat_function(fun = cubeFun)
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-3-1.png" width="672" />


我们再尝试画一条中学时代的函数曲线


```r
xsquare <- function(x) {
  x^2
}

ggplot(data.frame(x = c(-5, 10)), aes(x = x)) +
  stat_function(fun = xsquare)
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-4-1.png" width="672" />


## 同一个图片画多个函数曲线

要画多条曲线到同一个图片上，只需要添加多个stat_function就可以了。下面我们分别画两条正态分布曲线，一条定义好均值为0.2，标准差为0.1，另外一条的均值是0.7，标准差为0.05(因为**dnorm**函数默认是均值为0，标准差为1的，所以我们第一个图并没有指定均值和标准差画出来的就是标准正态分布曲线。)，我们也可以把x轴的范围变成0到1之间。



```r
ggplot(data.frame(x = c(0, 1)), aes(x = x)) +
  stat_function(fun = dnorm, args = list(0.2, 0.1)) +
  stat_function(fun = dnorm, args = list(0.7, 0.05))
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-5-1.png" width="672" />


我们再拿中学时代的正弦余弦曲线画在一张图上


```r
ggplot(data.frame(x = c(-5, 5)), aes(x = x)) +
  stat_function(fun = sin) +
  stat_function(fun = cos)
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-6-1.png" width="672" />


## 装饰图片

- 接着我们用scale_x_continuous和scale_y_continuous来修改坐标轴的标题，用我们需要用的坐标轴名字对参数name赋值。
- 我们还要修改一下坐标轴刻度，我们让x轴的刻度间隔变成0.2，这里用到scale_x_continuous的参数breaks，我们对breaks赋值`breaks = seq(0, 1, 0.2)`。
- 我们还可以用参数limits来控制x轴的显示范围，这里我们对limits赋值：`limits = c(0, 1)`。
- 当然我们还要用ggtitle来对图片添加一个醒目的标题。
- 还可以通过`aes(colour = )`来修改曲线的颜色。
- 这里我们**通过不同的曲线颜色来添加图例**，重点要学习这一条。
- 还可以修改曲线的粗细。

下面上代码把上面几条需求一起做了：



```r
ggplot(data.frame(x = c(0, 1)), aes(x = x)) +
  stat_function(fun = dnorm, args = list(0.2, 0.1), 
                aes(colour = "Group 1"), size = 1.5) +
  stat_function(fun = dnorm, args = list(0.7, 0.05), 
                aes(colour = "Group 2"), size = 1.5) +
  scale_x_continuous(name = "Probability",
                      breaks = seq(0, 1, 0.2),
                      limits=c(0, 1)) +
  scale_y_continuous(name = "Frequency") +
  ggtitle("Normal function curves of probabilities中文") +
  scale_colour_brewer(palette="Accent") +
  labs(colour = "Groups")
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-7-1.png" width="672" />


## 创建XKCD风格的图表

当然，我们也可以创建属于我们自己的主题样式，ggplot2给我们很高的自由度来自定义自己的图表样式，包括使用不同的字体，下面例子讲展示一个XKCD风格的图表。要做这个XKCD风格的图表，我们要先安装包`extrafont`，然后导入XKCD字体。

我写这一篇文章的时候用的是win10，把字体复制到win10系统字体文件夹，对参考资料链接文章的代码做了些修改才达到效果。

上码：


```r
library(extrafont)
download.file("http://simonsoftware.se/other/xkcd.ttf",
              dest="xkcd.ttf", mode="wb")
font_import(paths = "./", pattern="[X/x]kcd")
fonts()
loadfonts()
```

ok，导入字体之后我们就可以画图了：


```r
windowsFonts(xkcd = windowsFont(family = "xkcd"))
ggplot(data.frame(x = c(0, 1)), aes(x = x)) +
	stat_function(fun = dnorm, args = list(0.2, 0.1),
				  aes(colour = "Group 1"), size = 1.5) +
	stat_function(fun = dnorm, args = list(0.7, 0.05),
				  aes(colour = "Group 2"), size = 1.5) +
	scale_x_continuous(name = "Probability",
						  breaks = seq(0, 1, 0.2),
						  limits=c(0, 1)) +
	scale_y_continuous(name = "Frequency") +
	ggtitle("Normal function curves of probabilities") +
	scale_colour_brewer(palette="Set1") +
	labs(colour = "Groups") +
	theme(axis.line = element_line(size=1, colour = "black"),
		  panel.grid.major = element_blank(),
		  panel.grid.minor = element_blank(),
		  panel.border = element_blank(),
		  panel.background = element_blank(),
		  plot.title=element_text(size = 20, family="xkcd"),
		  text=element_text(size = 16, family="xkcd"),
		  axis.text.x=element_text(colour="black", size = 12),
		  axis.text.y=element_text(colour="black", size = 12),
		  legend.position = "bottom")
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-9-1.png" width="672" />


## 在曲线下面添加区域颜色

一般情况下我们并不会曲线全部下面都添加颜色，而只是其中一部分，要添加部分区域颜色，重点是要控制x轴的范围，因此我们要自定义一个函数用来控制这个范围，我们还用到了geom参数，并把它设置为**area**，为了实现透明效果我们也可以设置alpha参数。

上码(这里我简化了一下原来的代码)：


```r
funcShaded <- function(x) {
    y <- dnorm(x, mean = 0.2, sd = 0.1)
    y[x < 0.2 | x > (0.2 + 4 * 0.1)] <- NA
    return(y)
}

ggplot(data.frame(x = c(0, 1)), aes(x = x)) +
	stat_function(fun = dnorm, args = list(0.2, 0.1)) +
  stat_function(fun = funcShaded, geom = "area",
                fill = "#84CA72", alpha = 0.2)
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-10-1.png" width="672" />

## 在标准正态分布函数两侧填充拒绝域

在看统计学书中的分布图，经常会把某个区域填充颜色，学了ggplot2一段时间也没搞懂究竟怎么搞，现在终于学到一点技巧了，原来要用到**stat_function**，然后把参数**geom**参数设置为**area**，还要自定义一个函数来控制填充区域在x轴上的范围。

我们对上面的funcShaded函数做一点改进，上码：



```r
funcShaded2 <- function(x, mean = 0, sd = 1, min, max) {
    y <- dnorm(x, mean = mean, sd = sd)
    y[x < min | x > max] <- NA
    return(y)
}

x.mu <- 0
x.sd <- 1

ggplot(data.frame(x = c(-4, 4)), aes(x = x)) +
	stat_function(fun = dnorm, args = list(x.mu,x.sd)) +
  stat_function(fun = funcShaded2, geom = "area",
                fill = "#84CA72", alpha = 0.2,
                args = list(mean = x.mu, sd = x.sd, min = -1, max = 1)) +
  stat_function(fun = funcShaded2, geom = "area",
                fill = "red", alpha = 0.3,
                args = list(mean = x.mu, sd = x.sd, min = -Inf, max = -1.96)) +
  stat_function(fun = funcShaded2, geom = "area",
                fill = "red", alpha = 0.3,
                args = list(mean = x.mu, sd = x.sd, min = 1.96, max = Inf))
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-11-1.png" width="672" />


## 在非标准正态分布函数两侧填充拒绝域

如果要对非标准正态分布曲线两侧填充拒绝域，我们可以通过**qnorm**函数计算出0.95的置信区间的x值。


```r
x.mu <- 2
x.sd <- 3

xleft <- qnorm(0.025,mean = x.mu, sd = x.sd)
xright <- qnorm(0.975,mean = x.mu, sd = x.sd)

ggplot(data.frame(x = c(xleft - 1, xright + 1)), aes(x = x)) +
	stat_function(fun = dnorm, args = list(x.mu,x.sd)) +
  stat_function(fun = funcShaded2, geom = "area",
                fill = "red", alpha = 0.3,
                args = list(mean = x.mu, sd = x.sd, min = -Inf, max = xleft)) +
  stat_function(fun = funcShaded2, geom = "area",
                fill = "red", alpha = 0.3,
                args = list(mean = x.mu, sd = x.sd, min = xright, max = Inf))
```

<img src="/post/2017/2017-11-21-r-ggplot2-draw-using-stat-function_files/figure-html/unnamed-chunk-12-1.png" width="672" />


## 参考资料

- <http://t-redactyl.io/blog/2016/03/creating-plots-in-r-using-ggplot2-part-9-function-plots.html>
