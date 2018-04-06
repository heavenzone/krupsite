---
title: filter one column max by group with data.table
author: Heaven Zone
date: '2018-04-06'
slug: filter-one-column-max-by-group-with-data-table
categories:
  - R
tags:
  - R
  - data.table
---

# 目标

提取每个月最大日期的行。

# 数据结构


```r
testdf <- data.frame(d = c("2017-01-01", "2017-01-30", "2017-02-02", "2017-02-10"),
                     v = c("A", "B", "C", "D"))
testdf$d <- as.Date(testdf$d)
testdf$month <- lubridate::month(testdf$d)
testdf
```

```
##            d v month
## 1 2017-01-01 A     1
## 2 2017-01-30 B     1
## 3 2017-02-02 C     2
## 4 2017-02-10 D     2
```

# tidyverse


```r
testdf %>% group_by(month) %>%
  dplyr::filter(d == max(d))
```

```
## # A tibble: 2 x 3
## # Groups:   month [2]
##   d          v     month
##   <date>     <fct> <dbl>
## 1 2017-01-30 B        1.
## 2 2017-02-10 D        2.
```

# plyr::ddply


```r
plyr::ddply(testdf, "month", subset, d == max(d))
```

```
##            d v month
## 1 2017-01-30 B     1
## 2 2017-02-10 D     2
```


# data.table

## 方法一


```r
setDT(testdf)
testdf[, .SD[ d == max(d) ], by = month]
```

```
##    month          d v
## 1:     1 2017-01-30 B
## 2:     2 2017-02-10 D
```

    
    
## 方法二

据说这个更快。


```r
testdf[ testdf[, .I[d == max(d)], by = month]$V1 ]
```

```
##             d v month
## 1: 2017-01-30 B     1
## 2: 2017-02-10 D     2
```
    
# 参考资料

- <https://stackoverflow.com/questions/16573995/subset-by-group-with-data-table>
    
    
    
    
