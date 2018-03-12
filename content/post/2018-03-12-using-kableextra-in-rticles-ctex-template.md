---
title: using kableExtra in rticles ctex template
author: Heaven Zone
date: '2018-03-12'
slug: using-kableextra-in-rticles-ctex-template
categories:
  - R
tags:
  - R
  - kable
  - kableExtra
description: ''
keywords:
  - ''
---

# 问题

kableExtra包是`knitr::kable`的一个非常好的扩展。不过用rticles的ctex模板调用了`kableExtra::kable_styling `再生成pdf时发现出现下面这样的错误：

```
kableExtra LaTeX Error: Unknown float option `H'.
```

# 解决方法

在rmd文件的同一目录下创建`header.tex`文件，输入以下内容：

```tex
\usepackage{booktabs}
\usepackage{longtable}
\usepackage{array}
\usepackage{multirow}
\usepackage[table]{xcolor}
\usepackage{wrapfig}
\usepackage{float}
\usepackage{colortbl}
\usepackage{pdflscape}
\usepackage{tabu}
\usepackage{threeparttable}
\usepackage[normalem]{ulem}
```

在rmd文档的**front matter**处引用`header.tex`：

```
---
title: "Test kableExtra"
author: "Alpha Dog"
date: "`r Sys.Date()`"
documentclass: ctexart
output:
  rticles::ctex:
    fig_caption: yes
    number_sections: yes
    toc: yes
    toc_depth: 3
    #df_print: kable
    highlight: tango
    includes:
      in_header: header.tex
  keep_md: yes
classoption: "hyperref,"
---
```

如果你已经安装了`tinytex`包，她将会自动为你安装`header.tex`中的tex包。如果你还不知道**tinytex**，那你就要好好了解一下这个伟大发明了[[传送门](https://github.com/yihui/tinytex)]。

# 参考资料

- <http://haozhu233.github.io/kableExtra/>
- <http://haozhu233.github.io/kableExtra/awesome_table_in_pdf.pdf>


