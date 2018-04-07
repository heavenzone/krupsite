---
title: 学做雷达图
author: Heaven Zone
date: '2018-03-28'
slug: learn-to-make-a-radar-plot
categories:
  - R
tags:
  - R
  - 雷达图
  - 可视化
output: 
  html_document: 
    keep_md: yes
---

# 数据


```r
library(tidyverse)
library(scales)
library(showtext)
showtext_auto()
df <- data.frame(语文 = c(50, 60, 70, 90),
                   姓名 = c("A", "B", "C", "D"),
                   数学 = c(55, 60, 80, 90),
                   英语 = c(80, 60, 70, 77))
df
```

```
##   语文 姓名 数学 英语
## 1   50    A   55   80
## 2   60    B   60   60
## 3   70    C   80   70
## 4   90    D   90   77
```

# 用ggradar::ggradar()做雷达图

ggradar用的是ggplot2做图。


```r
df.new <- df %>% mutate_if(is.numeric, rescale) %>%
  select(姓名, everything())
ggradar::ggradar(df.new)
```

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/2018/2018-03-28-learn-to-make-a-radar-plot-2-1.png)<!-- -->

# 用fmsb做雷达图

fmsb用的是基础做图。


```r
fmsb_df <- function(df) {
  dfmax <- apply(df, 2, max)
  dfmin <- apply(df, 2, min) 
  as.data.frame(rbind(dfmax, dfmin, df))
}
df %>% select(-姓名) %>% fmsb_df() %>%
fmsb::radarchart(seg = 5, plty = 1:4, plwd = 4, pcol = rainbow(4))
legend("topright", legend = df$姓名, lty = 1:4, col = rainbow(4))
```

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/2018/2018-03-28-learn-to-make-a-radar-plot-3-1.png)<!-- -->

# 用ggplot


```r
library(ggplot2)
library(tidyverse)

coord_radar <- function (theta = "x", start = 0, direction = 1) {
  # http://www.cmap.polytechnique.fr/~lepennec/R/Radar/RadarAndParallelPlots.html
  theta <- match.arg(theta, c("x", "y"))
  r <- if (theta == "x") 
    "y"
  else "x"
  ggproto("CoordRadar", CoordPolar, theta = theta, r = r, start = start, 
          direction = sign(direction),
          is_linear = function(coord) TRUE)
}

hg_get_colname <- function(df, col) {
  if(is.numeric(col)) {
    colnames(df)[col]
  } else {
    tmp <- colnames(df)[colnames(df) %in% col]
    if(length(tmp) == 0) {
      stop("no that colname")
    } else {
      tmp
    }
  }
}

hg_gg_radar_long <- function(df, 
                              group_col = 1, 
                              variable_col = 2, 
                              value_col = 3,
                              filter = NULL
                              ) {
  group <- hg_get_colname(df, group_col)
  variable <- hg_get_colname(df, variable_col)
  value <- hg_get_colname(df, value_col)
  colnames(df)[colnames(df) == group] <- "group"
  colnames(df)[colnames(df) == variable] <- "variable"
  colnames(df)[colnames(df) == value] <- "value"
  df <- df %>% 
    group_by(variable) %>% mutate(value.rescale = rescale(value)) %>%
    ungroup() %>% as.data.frame() 
  if(!is.null(filter)) {
   df <- df %>% dplyr::filter(group %in% filter)
  }
  ggplot(df, aes(x = variable, y = value.rescale, color = group)) +
    geom_point(size=rel(0.9)) +
    geom_polygon(aes(group = group), 
                 data = df, 
                 fill = NA, 
                 size = 1) +
    geom_line(aes(group = group), data = df, size = 1) +
    annotate("text",
             x = df[, variable], 
             y = (df[, "value.rescale"]) + 0.08,
             label = round(df[, value], 2), size = 3) +
    theme(strip.text.x = element_text(size = rel(0.8)),
          axis.text.x = element_text(size = rel(1)),
          axis.ticks.y = element_blank(),
          panel.grid.minor = element_blank(),
          axis.text.y = element_blank()) +
    scale_y_continuous(limits = c(0, 1.05)) +
    xlab("") + ylab("") +
    guides(color = guide_legend()) +
    coord_radar()
}

hg_gg_radar <- function(df, group_col = 1, ...) {
  arglist <- c(...)
  group <- hg_get_colname(df, group_col)
  
  df <- df %>% gather(key = "variable", value = "value", -group) %>%
    arrange(variable)
  hg_gg_radar_long(df, filter = arglist)
}

hg_df_rescale <- function(df, ...) {
  arglist <- list(...)
  if(length(arglist))
    df %>% mutate_at(unlist(arglist), rescale)
  else
    df %>% mutate_if(.predicate = is.numeric, rescale)
}

hg_gg_radar(df, "姓名", "C", "D") +
  ggtitle("雷达图")
```

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/2018/2018-03-28-learn-to-make-a-radar-plot-4-1.png)<!-- -->


# 参考资料

- <https://stackoverflow.com/questions/28898143/closing-the-lines-in-a-ggplot2-radar-spider-chart>
- <http://www.cmap.polytechnique.fr/~lepennec/R/Radar/RadarAndParallelPlots.html>
