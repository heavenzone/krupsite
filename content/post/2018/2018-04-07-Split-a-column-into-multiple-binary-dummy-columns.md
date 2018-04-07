---
title: 一列含多分类的哑变量处理
author: Heaven Zone
date: '2018-04-07'
slug: Split-a-column-into-multiple-binary-dummy-columns
categories:
  - R
tags:
  - R
---

# 问题描述

最近在天池找了个数据挖掘竞赛题目玩了一下，发现有一列数据形如：`<分类1>;<分类2>`，有一列是固定两个分类，有一个是有多个分类，而且是不定长的，固定长度的应该来说还是容易处理，不定长的就感觉有点难。我猜应该要把他们分离出来，然后再做哑变量处理，然后再做机器学习，于是到统计之都去问了一下，很快就得到了**tctcab**的回复，后来也到stackoverflow上面搜了一下，下面总结了几个方法。

# 上码


```r
testdf <- data.frame(id = c(1,2,3,4,5), 
                     v = c("A", "A;B", "A;C", "A;B;C", "C;D"))

tdf <- testdf
for(i in 1:2000) {
  tdf <- rbind(tdf, testdf)
}
dim(tdf)
```

```
## [1] 10005     2
```

```r
tdf$id <- seq(1, nrow(tdf))

# === tidyverse
# 从tctcab回复学到的

system.time({
  rst1 <- tidyr::separate_rows(tdf,v) %>%
    mutate(val=1) %>%
    spread(v,val,fill = 0)
})
```

```
##    user  system elapsed 
##    0.17    0.02    0.19
```

```r
rst1 %>% head
```

```
##   id A B C D
## 1  1 1 0 0 0
## 2  2 1 1 0 0
## 3  3 1 0 1 0
## 4  4 1 1 1 0
## 5  5 0 0 1 1
## 6  6 1 0 0 0
```

```r
# base R

system.time({
  rst2 <- cbind(tdf[,1], as.data.frame.matrix(t(table(stack(setNames(strsplit(as.character(tdf$v), ';'), 
                                                tdf$id))))) )
})
```

```
##    user  system elapsed 
##    0.03    0.00    0.03
```

```r
rst2 %>% dim
```

```
## [1] 10005     5
```

```r
rst2 %>% head
```

```
##   tdf[, 1] A B C D
## 1        1 1 0 0 0
## 2        2 1 1 0 0
## 3        3 1 0 1 0
## 4        4 1 1 1 0
## 5        5 0 0 1 1
## 6        6 1 0 0 0
```

```r
setNames(strsplit(as.character(tdf$v), ';'), 
         tdf$id) %>%
  stack %>%
  table %>%
  t %>%
  as.data.frame.matrix() %>%
  cbind(tdf[1], .) %>% 
  head
```

```
##   id A B C D
## 1  1 1 0 0 0
## 2  2 1 1 0 0
## 3  3 1 0 1 0
## 4  4 1 1 1 0
## 5  5 0 0 1 1
## 6  6 1 0 0 0
```

```r
# splitstackshape

library(splitstackshape)
system.time({
  rst3 <- cSplit_e(tdf, 'v', ';', type= 'character', fill=0, drop=TRUE)
  names(rst3) <-  sub('.*_', '', names(rst3))
})
```

```
##    user  system elapsed 
##    0.15    0.00    0.15
```

```r
rst3 %>% head
```

```
##   id A B C D
## 1  1 1 0 0 0
## 2  2 1 1 0 0
## 3  3 1 0 1 0
## 4  4 1 1 1 0
## 5  5 0 0 1 1
## 6  6 1 0 0 0
```

```r
# qdapTools::mtabulate 

library(qdapTools)
system.time({
  rst4 <- cbind(tdf[1],mtabulate(strsplit(as.character(tdf$v), ';')))
})
```

```
##    user  system elapsed 
##    0.14    0.00    0.14
```

```r
rst4 %>% head()
```

```
##   id A B C D
## 1  1 1 0 0 0
## 2  2 1 1 0 0
## 3  3 1 0 1 0
## 4  4 1 1 1 0
## 5  5 0 0 1 1
## 6  6 1 0 0 0
```

```r
# data.table

library(data.table)
setDT(tdf)
tfun <- function() 1
system.time({
  rst5 <- tdf[, strsplit(as.character(v), ";"), by = id] %>%
    dcast(id ~ V1,function(x) {ifelse(0,0,1)}, drop=FALSE, fill=0, )
})
```

```
##    user  system elapsed 
##    0.16    0.00    0.16
```

```r
rst5 %>% head
```

```
##    id A B C D
## 1:  1 1 0 0 0
## 2:  2 1 1 0 0
## 3:  3 1 0 1 0
## 4:  4 1 1 1 0
## 5:  5 0 0 1 1
## 6:  6 1 0 0 0
```


    
# 参考资料

- [Split a column into multiple binary dummy columns](https://stackoverflow.com/questions/32978506/split-a-column-into-multiple-binary-dummy-columns)
- [Split comma-separated column into separate rows](https://stackoverflow.com/questions/13773770/split-comma-separated-column-into-separate-rows)
- [Split string in rows to create new columns](https://stackoverflow.com/questions/27630588/split-string-in-rows-to-create-new-columns)
- <https://d.cosx.org/d/419910--/4>
    
    
    
    
