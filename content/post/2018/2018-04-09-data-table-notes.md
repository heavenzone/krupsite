---
title: data.table笔记
author: Heaven Zone
date: '2018-04-09'
slug: data-table-notes
categories:
  - R
tags:
  - R
  - data.table
simpletoc: true
---

# 构建测试数据


```r
examdf <- data.frame(id = c(1,2,3,4,5,6,7,8,9),
                     name = c("Jordan", "Kobe", "T-Mac", 
                              "Duncan", "Garnet", "Iverson", 
                              "Dwade", "CP3", "Bird"),
                     team = c("SG", "SG", "SF", 
                              "PF", "PF", "SG", 
                              "PG", "PG", "SF"),
                     num = c(23, 24, 1, 
                             21, 21, 3,
                             3, 3, 33
                             ),
                     score = c(1, 2, 3, 
                               4, 5, 6,
                               7, 8, 9), 
                     stringsAsFactors = FALSE)

examdf
```

```code-output
##   id    name team num score
## 1  1  Jordan   SG  23     1
## 2  2    Kobe   SG  24     2
## 3  3   T-Mac   SF   1     3
## 4  4  Duncan   PF  21     4
## 5  5  Garnet   PF  21     5
## 6  6 Iverson   SG   3     6
## 7  7   Dwade   PG   3     7
## 8  8     CP3   PG   3     8
## 9  9    Bird   SF  33     9
```


# tidyverse、data.table、baseR对比实战

## 计算每个team的总score

### tidyverse


```r
examdf %>% dplyrExtras::s_group_by("team") %>%
  summarise(sum_score = sum(score))
```

```code-output
## # A tibble: 4 x 2
##   team  sum_score
##   <chr>     <dbl>
## 1 PF           9.
## 2 PG          15.
## 3 SF          12.
## 4 SG           9.
```

### aggregate


```r
aggregate(score ~ team, examdf, sum)
```

```code-output
##   team score
## 1   PF     9
## 2   PG    15
## 3   SF    12
## 4   SG     9
```

### data.table


```r
setDT(examdf)[, .(sum_score = sum(score)), by = team]
```

```code-output
##    team sum_score
## 1:   SG         9
## 2:   SF        12
## 3:   PF         9
## 4:   PG        15
```

## 仅计算出team A和team B的总score

### tidyverse


```r
examdf %>% dplyr::filter(team == "SG" | team == "PG") %>%
  group_by(team) %>%
  summarise(sum_score = sum(score))
```

```code-output
## # A tibble: 2 x 2
##   team  sum_score
##   <chr>     <dbl>
## 1 PG          15.
## 2 SG           9.
```

### aggregate


```r
aggregate(score ~ team, examdf[examdf$team %in% c("SG", "PG"), ], sum)
```

```code-output
##   team score
## 1   PG    15
## 2   SG     9
```

### data.table


```r
setDT(examdf)[team %in% c("SG", "PG"), .(sum_score = sum(score)), by = team]
```

```code-output
##    team sum_score
## 1:   SG         9
## 2:   PG        15
```

```r
setDT(examdf, key = "team")[c("SG", "PG"), .(sum_score = sum(score)), by = team]
```

```code-output
##    team sum_score
## 1:   SG         9
## 2:   PG        15
```

## 对num列排序并只返回name和num列

### tidyverse


```r
examdf %>% select(name, num) %>%
  arrange(num)
```

```code-output
##      name num
## 1   T-Mac   1
## 2   Dwade   3
## 3     CP3   3
## 4 Iverson   3
## 5  Duncan  21
## 6  Garnet  21
## 7  Jordan  23
## 8    Kobe  24
## 9    Bird  33
```

### base R


```r
examdf[order(examdf$num), c("name", "num")]
```

```code-output
##       name num
## 1:   T-Mac   1
## 2:   Dwade   3
## 3:     CP3   3
## 4: Iverson   3
## 5:  Duncan  21
## 6:  Garnet  21
## 7:  Jordan  23
## 8:    Kobe  24
## 9:    Bird  33
```


### data.table


```r
setDT(examdf)[order(num), .(name, num)]
```

```code-output
##       name num
## 1:   T-Mac   1
## 2:   Dwade   3
## 3:     CP3   3
## 4: Iverson   3
## 5:  Duncan  21
## 6:  Garnet  21
## 7:  Jordan  23
## 8:    Kobe  24
## 9:    Bird  33
```


## 提取每team最大score的行

### tidyverse


```r
examdf %>% group_by(team) %>%
  dplyr::filter(score == max(score))
```

```code-output
## # A tibble: 4 x 5
## # Groups:   team [4]
##      id name    team    num score
##   <dbl> <chr>   <chr> <dbl> <dbl>
## 1    5. Garnet  PF      21.    5.
## 2    8. CP3     PG       3.    8.
## 3    9. Bird    SF      33.    9.
## 4    6. Iverson SG       3.    6.
```

### data.table


```r
setDT(examdf)[, .SD[ score == max(score)], by = team]
```

```code-output
##    team id    name num score
## 1:   PF  5  Garnet  21     5
## 2:   PG  8     CP3   3     8
## 3:   SF  9    Bird  33     9
## 4:   SG  6 Iverson   3     6
```

## 合并两个表left_join



```r
examdf.reb <- data.table(name = c("Duncan", "Garnet"), 
                         reb = c(12, 13))
examdf.reb
```

```code-output
##      name reb
## 1: Duncan  12
## 2: Garnet  13
```

将上表合并到`examdf`中：


```r
# dplyr
left_join(examdf, examdf.reb, by = "name", )
```

```code-output
##   id    name team num score reb
## 1  4  Duncan   PF  21     4  12
## 2  5  Garnet   PF  21     5  13
## 3  7   Dwade   PG   3     7  NA
## 4  8     CP3   PG   3     8  NA
## 5  3   T-Mac   SF   1     3  NA
## 6  9    Bird   SF  33     9  NA
## 7  1  Jordan   SG  23     1  NA
## 8  2    Kobe   SG  24     2  NA
## 9  6 Iverson   SG   3     6  NA
```

```r
# data.table

setkey(examdf, name)
setkey(examdf.reb, name)

## 只包含到examdf.reb的行
examdf[examdf.reb]
```

```code-output
##    id   name team num score reb
## 1:  4 Duncan   PF  21     4  12
## 2:  5 Garnet   PF  21     5  13
```

```r
## 包含examdf的所有行
examdf.reb[examdf]
```

```code-output
##       name reb id team num score
## 1:    Bird  NA  9   SF  33     9
## 2:     CP3  NA  8   PG   3     8
## 3:  Duncan  12  4   PF  21     4
## 4:   Dwade  NA  7   PG   3     7
## 5:  Garnet  13  5   PF  21     5
## 6: Iverson  NA  6   SG   3     6
## 7:  Jordan  NA  1   SG  23     1
## 8:    Kobe  NA  2   SG  24     2
## 9:   T-Mac  NA  3   SF   1     3
```

```r
# merge: 包含所有行
merge(examdf, examdf.reb, all = TRUE)
```

```code-output
##       name id team num score reb
## 1:    Bird  9   SF  33     9  NA
## 2:     CP3  8   PG   3     8  NA
## 3:  Duncan  4   PF  21     4  12
## 4:   Dwade  7   PG   3     7  NA
## 5:  Garnet  5   PF  21     5  13
## 6: Iverson  6   SG   3     6  NA
## 7:  Jordan  1   SG  23     1  NA
## 8:    Kobe  2   SG  24     2  NA
## 9:   T-Mac  3   SF   1     3  NA
```

# data.table

## 一些基本操作


```r
class(examdf)
```

```code-output
## [1] "data.table" "data.frame"
```

```r
setDT(examdf)
examdf[num == 3]
```

```code-output
##    id    name team num score
## 1:  8     CP3   PG   3     8
## 2:  7   Dwade   PG   3     7
## 3:  6 Iverson   SG   3     6
```

```r
examdf[num == 3, ]
```

```code-output
##    id    name team num score
## 1:  8     CP3   PG   3     8
## 2:  7   Dwade   PG   3     7
## 3:  6 Iverson   SG   3     6
```

```r
examdf[, num == 3]
```

```code-output
## [1] FALSE  TRUE FALSE  TRUE FALSE  TRUE FALSE FALSE FALSE
```

## 一些特殊操作

.SD, .BY, .N, .I and .GRP are read only symbols for use in j. 
.N can be used in i as well. 


## data.table的`.SD`和`{}`

对列的控制操作。


```r
# 提取每team最大score的行
examdf[, .SD[score == max(score)], by = team]
```

```code-output
##    team id    name num score
## 1:   SF  9    Bird  33     9
## 2:   PG  8     CP3   3     8
## 3:   PF  5  Garnet  21     5
## 4:   SG  6 Iverson   3     6
```

```r
# 注意区别
examdf[, .SD[, score == max(score)], by = team]
```

```code-output
##    team    V1
## 1:   SF  TRUE
## 2:   SF FALSE
## 3:   PG  TRUE
## 4:   PG FALSE
## 5:   PF FALSE
## 6:   PF  TRUE
## 7:   SG  TRUE
## 8:   SG FALSE
## 9:   SG FALSE
```

```r
# 选择team至score的列
examdf[, .SD, .SDcols = team:score]
```

```code-output
##    team num score
## 1:   SF  33     9
## 2:   PG   3     8
## 3:   PF  21     4
## 4:   PG   3     7
## 5:   PF  21     5
## 6:   SG   3     6
## 7:   SG  23     1
## 8:   SG  24     2
## 9:   SF   1     3
```

```r
examdf[, .SD, .SDcols = 3:4]
```

```code-output
##    team num
## 1:   SF  33
## 2:   PG   3
## 3:   PF  21
## 4:   PG   3
## 5:   PF  21
## 6:   SG   3
## 7:   SG  23
## 8:   SG  24
## 9:   SF   1
```

```r
# 提取所有列的第一行
examdf[, .SD[1]]
```

```code-output
##    id name team num score
## 1:  9 Bird   SF  33     9
```

```r
# 提取每team的第一行
examdf[, .SD[1], by = team]
```

```code-output
##    team id    name num score
## 1:   SF  9    Bird  33     9
## 2:   PG  8     CP3   3     8
## 3:   PF  4  Duncan  21     4
## 4:   SG  6 Iverson   3     6
```

```r
# 错误
# examdf[1, by = team]

# 计算每个team中每个人的score占该组总score的占比
examdf[, {
  sum.tmp = sum(score)
  m = score / sum.tmp
} , by = team]
```

```code-output
##    team        V1
## 1:   SF 0.7500000
## 2:   SF 0.2500000
## 3:   PG 0.5333333
## 4:   PG 0.4666667
## 5:   PF 0.4444444
## 6:   PF 0.5555556
## 7:   SG 0.6666667
## 8:   SG 0.1111111
## 9:   SG 0.2222222
```

```r
examdf[, .(pct = score/ sum(score)), by = team]
```

```code-output
##    team       pct
## 1:   SF 0.7500000
## 2:   SF 0.2500000
## 3:   PG 0.5333333
## 4:   PG 0.4666667
## 5:   PF 0.4444444
## 6:   PF 0.5555556
## 7:   SG 0.6666667
## 8:   SG 0.1111111
## 9:   SG 0.2222222
```

```r
examdf[, pct := score/ sum(score), by = team]
examdf
```

```code-output
##    id    name team num score       pct
## 1:  9    Bird   SF  33     9 0.7500000
## 2:  8     CP3   PG   3     8 0.5333333
## 3:  4  Duncan   PF  21     4 0.4444444
## 4:  7   Dwade   PG   3     7 0.4666667
## 5:  5  Garnet   PF  21     5 0.5555556
## 6:  6 Iverson   SG   3     6 0.6666667
## 7:  1  Jordan   SG  23     1 0.1111111
## 8:  2    Kobe   SG  24     2 0.2222222
## 9:  3   T-Mac   SF   1     3 0.2500000
```

```r
# 参考 http://brooksandrew.github.io/simpleblog/articles/advanced-data-table/#calculating-unbiased-mean
# dt[,{
#   vbar = mean(mpg)
#   n = .N
#   .SD[,(n*vbar-sum(mpg))/(n-.N),by=gear]
# } , by=cyl]
```

## data.table的.N


```r
# 显示最后一行
examdf[.N]
```

```code-output
##    id  name team num score  pct
## 1:  3 T-Mac   SF   1     3 0.25
```

```r
# 显示总行数
examdf[, .N]
```

```code-output
## [1] 9
```

```r
# 计算每个team的行数
examdf[, .N, by = team]
```

```code-output
##    team N
## 1:   SF 2
## 2:   PG 2
## 3:   PF 2
## 4:   SG 3
```

```r
# 对team分组计算分组行数、和其他列的分组求和
examdf[,-c(2)][, c(.N, lapply(.SD, sum)), by = team]
```

```code-output
##    team N id num score pct
## 1:   SF 2 12  34    12   1
## 2:   PG 2 15   6    15   1
## 3:   PF 2  9  42     9   1
## 4:   SG 3  9  50     9   1
```

## data.table的`.I`


```r
# 生成索引列
examdf[, i := .I]
examdf
```

```code-output
##    id    name team num score       pct i
## 1:  9    Bird   SF  33     9 0.7500000 1
## 2:  8     CP3   PG   3     8 0.5333333 2
## 3:  4  Duncan   PF  21     4 0.4444444 3
## 4:  7   Dwade   PG   3     7 0.4666667 4
## 5:  5  Garnet   PF  21     5 0.5555556 5
## 6:  6 Iverson   SG   3     6 0.6666667 6
## 7:  1  Jordan   SG  23     1 0.1111111 7
## 8:  2    Kobe   SG  24     2 0.2222222 8
## 9:  3   T-Mac   SF   1     3 0.2500000 9
```

```r
# 返回每个team中最大score的行号
examdf[, .I[which.max(score)], by = team]
```

```code-output
##    team V1
## 1:   SF  1
## 2:   PG  2
## 3:   PF  5
## 4:   SG  6
```

```r
# 返回每team的n行索引
examdf[, .I[1:3], by = team]
```

```code-output
##     team V1
##  1:   SF  1
##  2:   SF  9
##  3:   SF NA
##  4:   PG  2
##  5:   PG  4
##  6:   PG NA
##  7:   PF  3
##  8:   PF  5
##  9:   PF NA
## 10:   SG  6
## 11:   SG  7
## 12:   SG  8
```

## data.table的`.GRP`


```r
# 对分组生成索引号
examdf[, gcount := .GRP, by = team]
examdf[]
```

```code-output
##    id    name team num score       pct i gcount
## 1:  9    Bird   SF  33     9 0.7500000 1      1
## 2:  8     CP3   PG   3     8 0.5333333 2      2
## 3:  4  Duncan   PF  21     4 0.4444444 3      3
## 4:  7   Dwade   PG   3     7 0.4666667 4      2
## 5:  5  Garnet   PF  21     5 0.5555556 5      3
## 6:  6 Iverson   SG   3     6 0.6666667 6      4
## 7:  1  Jordan   SG  23     1 0.1111111 7      4
## 8:  2    Kobe   SG  24     2 0.2222222 8      4
## 9:  3   T-Mac   SF   1     3 0.2500000 9      1
```

## data.table的`rleid`


```r
# 生成连续序号
DT2 = data.table(grp=rep(c("A", "B", "C", "A", "B"), c(2,2,3,1,2)), value=1:10)
DT2
```

```code-output
##     grp value
##  1:   A     1
##  2:   A     2
##  3:   B     3
##  4:   B     4
##  5:   C     5
##  6:   C     6
##  7:   C     7
##  8:   A     8
##  9:   B     9
## 10:   B    10
```

```r
cbind(rleid(DT2$grp), DT2)
```

```code-output
##     V1 grp value
##  1:  1   A     1
##  2:  1   A     2
##  3:  2   B     3
##  4:  2   B     4
##  5:  3   C     5
##  6:  3   C     6
##  7:  3   C     7
##  8:  4   A     8
##  9:  5   B     9
## 10:  5   B    10
```
















