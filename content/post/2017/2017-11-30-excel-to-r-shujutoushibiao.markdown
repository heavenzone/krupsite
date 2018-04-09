---
title: 用R语言实现Excel中的数据透视表功能
author: Heaven Zone
date: '2017-11-30'
slug: Excel-to-r-shijutoushibiao
categories:
  - R
tags:
  - R
  - Excel
description: '用哈德利大神的tidyverse实现Excel中的数据透视表功能？'
---



## 前言

对于像我这种R语言刚入门的选手，虽然对Excel的各种操作还是蛮熟悉，但是想用R语言处理数据就像用Excel那么出神入化，还是花了不少时间去学了哈德利大神的tidyverse包，实际上tidyverse包是'tidyverse'，'tidyr'，'readr'，'purrr'，'dplyr'等包的大集合。

个人认为tidyverse比baseR的语法上更加清晰吧，所以更愿意优先使用tidyverse的方式去处理数据，至于究竟学tidyverse好呢？还是学baseR更好呢？可以参考下面两位大神的文章参考一下吧。

- David Robinson: [Teach the tidyverse to beginners](http://varianceexplained.org/r/teach-tidyverse/)
- Yihui: [I'm Also A Desiccated baseR-er Like Karl Broman](https://yihui.name/en/2017/10/base-r-broman/)

下面我们用一个随机生成的学生成绩表来做一些类似Excel上面常做的操作。


## 导入数据

导入成绩数据并查看数据结构：


```r
exam.data <- readxl::read_excel("../../../tdata/exams_results.xlsx")
head(exam.data)
```

```
## # A tibble: 6 x 8
##   班级  姓名   性别   语文  数学  英语   X科  综合
##   <chr> <chr>  <chr> <dbl> <dbl> <dbl> <dbl> <dbl>
## 1 六1班 杨涛   男      48.   41.   44.   99.   38.
## 2 六1班 王玉英 女      49.  101.   54.   64.   89.
## 3 六1班 张颖   女     104.   97.   43.  112.   62.
## 4 六1班 王凤英 女      92.  101.   58.  107.   72.
## 5 六1班 刘磊   男     116.   61.  104.  104.   92.
## 6 六1班 张雪   女      89.   69.   47.  117.   37.
```




## 找出每个班中英语最高分


```r
exam.data %>% group_by(班级) %>%
  dplyr::filter(英语 == max(英语)) %>%
  dplyr::select("班级", "姓名", "英语")
```

```
## # A tibble: 4 x 3
## # Groups:   班级 [4]
##   班级  姓名   英语
##   <chr> <chr> <dbl>
## 1 六1班 刘鑫   109.
## 2 六2班 王宇   115.
## 3 六3班 王亮   113.
## 4 六4班 刘平   118.
```


## 找出每个班英语前三名

下面将每个班英语前三名的同学找出来，并按班级、排名进行排序：


```r
exam.data %>% group_by(班级) %>%
  mutate(rank.eng = rank(desc(英语), ties.method= "min")) %>%
  dplyr::filter(rank.eng <= 3) %>%
  select("班级", "姓名", "英语",rank.eng) %>%
  arrange(班级,rank.eng)
```

```
## # A tibble: 12 x 4
## # Groups:   班级 [4]
##    班级  姓名    英语 rank.eng
##    <chr> <chr>  <dbl>    <int>
##  1 六1班 刘鑫    109.        1
##  2 六1班 杨梅    105.        2
##  3 六1班 刘磊    104.        3
##  4 六2班 王宇    115.        1
##  5 六2班 张倩    114.        2
##  6 六2班 张玉英  105.        3
##  7 六3班 王亮    113.        1
##  8 六3班 陈玲    112.        2
##  9 六3班 陈超    108.        3
## 10 六4班 刘平    118.        1
## 11 六4班 李磊    115.        2
## 12 六4班 刘佳    105.        3
```


arrange会忽略掉group_by的分组，因此要把每个班级放在一起，需要在arrange的参数里面加入**班级**。


## 找出每个班男女最高分

下面我们再尝试一下找出每个班男生和女生的第一名出来。


```r
exam.data %>% group_by(班级, 性别) %>%
  mutate(rank.eng = rank(desc(英语), ties.method= "min")) %>%
  select("班级","性别","姓名","英语",rank.eng) %>%
  arrange(班级,性别,rank.eng) %>%
  slice(1)
```

```
## # A tibble: 8 x 5
## # Groups:   班级, 性别 [8]
##   班级  性别  姓名   英语 rank.eng
##   <chr> <chr> <chr> <dbl>    <int>
## 1 六1班 男    刘鑫   109.        1
## 2 六1班 女    杨梅   105.        1
## 3 六2班 男    王宇   115.        1
## 4 六2班 女    张倩   114.        1
## 5 六3班 男    王亮   113.        1
## 6 六3班 女    陈玲   112.        1
## 7 六4班 男    刘平   118.        1
## 8 六4班 女    刘佳   105.        1
```


## 计算每个班男女各科平均分

下面尝试计算出每个班男生女生的语数英每科的平均分，看看男生女生谁的平均分高？



```r
exam.data %>% group_by(班级, 性别) %>%
  summarise(语文平均 = mean(语文),
            数学平均 = mean(数学),
            英语平均 = mean(英语))
```

```
## # A tibble: 8 x 5
## # Groups:   班级 [?]
##   班级  性别  语文平均 数学平均 英语平均
##   <chr> <chr>    <dbl>    <dbl>    <dbl>
## 1 六1班 男        85.4     67.0     78.7
## 2 六1班 女        82.7     88.2     60.7
## 3 六2班 男        80.2     89.2     78.0
## 4 六2班 女        84.0     81.4     84.7
## 5 六3班 男        80.8     75.5     89.3
## 6 六3班 女        81.7     64.8     84.7
## 7 六4班 男        88.5     70.0     87.3
## 8 六4班 女        76.2     65.0     68.7
```


## 计算每个同学总平均分

下面再找出1班5科平均分最高的前5名同学。

### 方法一：rowMeans

这里需要注意`rowMeans`这个函数，还要注意rowMeans函数里面的`.`的用法。


```r
exam.data %>% 
  dplyr::filter(班级 == "六1班") %>%
  mutate(平均分 = (rowMeans(.[4:8]))) %>% 
  select("班级", "姓名", "平均分") %>%
  arrange(desc(平均分)) %>%
  head(5)
```

```
## # A tibble: 5 x 3
##   班级  姓名   平均分
##   <chr> <chr>   <dbl>
## 1 六1班 刘磊     95.4
## 2 六1班 李建华   95.4
## 3 六1班 杨梅     88.8
## 4 六1班 王凤英   86.0
## 5 六1班 张颖     83.6
```



### 方法二：转换成短数据

这里将用到了gather、group_by、summarise的组合来计算出上面同样的结果。


```r
exam.data.duan <- exam.data %>% 
  gather(key = "科目", value = "分数",
         c("语文","数学","英语","X科","综合"))

# 这里顺便用一下kimisc包，对data.frame进行随机行抽样
# 查看新的数据框exam.data.duan的数据结构
require(kimisc)
```

```
## Loading required package: kimisc
```

```r
sample.rows(exam.data.duan, size = 5)
```

```
## `sample.rows()` is deprecated, use `dplyr::sample_n()` instead.
```

```
## # A tibble: 5 x 5
##   班级  姓名   性别  科目   分数
##   <chr> <chr>  <chr> <chr> <dbl>
## 1 六1班 王建华 男    语文    78.
## 2 六1班 刘磊   男    数学    61.
## 3 六2班 张玉珍 女    数学    52.
## 4 六4班 张建华 男    数学    42.
## 5 六1班 李建华 男    英语   102.
```

我们可以看到，数据框的结构发生了变化，新建的exam.data.duan多了两列**科目**、**分数**，而原本的语文、数学、英语、X科和综合都不见了。

也就是用一列分数来记录5科的分数，那么每个同学应该有5行的记录，原本exam.data的行列数是(50, 8)，而现在的exam.data.duan的行列应该是(50*5, 5)，也就是应该是有250行的记录。

我们检验一下行列数：


```r
dim(exam.data.duan)
```

```
## [1] 250   5
```

我们可以再检验一下每个同学是否有5行记录：


```r
exam.data.duan %>% 
  dplyr::filter(姓名 == "张雪")
```

```
## # A tibble: 5 x 5
##   班级  姓名  性别  科目   分数
##   <chr> <chr> <chr> <chr> <dbl>
## 1 六1班 张雪  女    语文    89.
## 2 六1班 张雪  女    数学    69.
## 3 六1班 张雪  女    英语    47.
## 4 六1班 张雪  女    X科    117.
## 5 六1班 张雪  女    综合    37.
```

确实有5行记录，并记录了每一科目的分数。

数据已经按照预期那样讲宽型数据转化成了短数据，下面我们就可以进行对每个同学进行计算平均分。

我们再用group_by和summarise的组合计算出1班前5名同学的平均分：


```r
exam.data.duan %>% group_by(班级,姓名) %>%
  summarise(平均分 = mean(分数)) %>%
  dplyr::filter(班级 == "六1班") %>%
  arrange(desc(平均分)) %>%
  head(5)
```

```
## # A tibble: 5 x 3
## # Groups:   班级 [1]
##   班级  姓名   平均分
##   <chr> <chr>   <dbl>
## 1 六1班 李建华   95.4
## 2 六1班 刘磊     95.4
## 3 六1班 杨梅     88.8
## 4 六1班 王凤英   86.0
## 5 六1班 张颖     83.6
```

结果跟我们前面第一种方法的结果一致。


