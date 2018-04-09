---
title: blogdown中Rmarkdown和Rmd不同扩展名不同作用
author: Heaven Zone
date: '2018-04-09'
slug: blogdown-rmarkdown-rmd-difference
categories:
  - R
tags:
  - R
  - blogdown
---

弄blogdown有一段时间，一直有两个疑问：

- 为什么`Addins/New Post`下方会有`Rmd`和`Rmarkdown`两个扩展名选择？
- 为什么Rmd文档生成的是html？而不生成md文档呢？（因为我一直都是只用`Rmd`扩展名，有短的为什么要用长的呢？我连探索这个都没有勇气吗？-_-!）html又在同目录下生成图片文件夹，这在一定程度上不是很美观， 我相信对于爱靓的@Yihui不可能容忍这种事情发生的!

带着这两个疑问，我翻开了**blogdown**的[使用说明书1.5](https://bookdown.org/yihui/blogdown/output-format.html)，

>In this book, we usually mean .Rmd files when we say “R Markdown documents,” which are compiled to .html by default. However, there is another type of R Markdown document with the filename extension .Rmarkdown. Such R Markdown documents are compiled to Markdown documents with the extension .markdown, which will be processed by Hugo instead of Pandoc. There are two major limitations of using .Rmarkdown compared to .Rmd:

> - You cannot use Markdown features only supported by Pandoc, such as citations. Math expressions only work if you have installed the xaringan package (Xie [2018d](https://bookdown.org/yihui/blogdown/output-format.html#ref-R-xaringan)) and applied the JavaScript solution mentioned in Section [B.3](https://bookdown.org/yihui/blogdown/javascript.html#javascript).

> - HTML widgets are not supported.

这也解释了为什么安装blogdown会提示安装**xaringan「幻灯忍者·写轮眼」**。

虽然`.Rmarkdown`比`.Rmd`少了一点点功能（有些地方处理起来可能稍微麻烦一点），不过在绝大部分使用中，生成mardkown文档总是比生成html让人更舒服一点。