---
title: dplython for python
author: Heaven Zone
date: '2018-08-23'
slug: dplython-for-python
categories:
  - python
tags:
  - python
show_toc: true
---

## 简介

一直想知道python下有没有像r中的dplyr的一个库，昨天搜了一下发现有个`dplython`的库。

下面学习一下dplython的用法。

## 安装

```
pip install dplython
pip install git+https://github.com/dodger487/dplython.git
```

## 使用方法

### 示例数据


```python
import pandas as pd
data = [["Jordan", "Bulls", 23, 300, 10],
        ["Kobe", "Lakers", 24, 280, 10],
        ["O'Neal", "Lakers", 34, 250, 8],
        ["Pippen", "Bulls", 33, 240, 10],
        ["Iverson", "76ers", 3, 290, 10],
        ["Duncan", "Spurs", 21, 270, 10]]

df = pd.DataFrame(data, columns=["name", "team", "number", "pts", "gs"])
print(df)
```

          name    team  number  pts  gs
    0   Jordan   Bulls      23  300  10
    1     Kobe  Lakers      24  280  10
    2   O'Neal  Lakers      34  250   8
    3   Pippen   Bulls      33  240  10
    4  Iverson   76ers       3  290  10
    5   Duncan   Spurs      21  270  10
    

### 载入库函数


```python
import dplython as dp
from dplython import (DplyFrame, X, diamonds, select, sift, sample_n,
    sample_frac, head, arrange, mutate, group_by, summarize, DelayFunction)
```

### 转换数据类型


```python
df = DplyFrame(df)
type(df)
```




    dplython.dplython.DplyFrame



### 选择列

用`select`。

`>>`相当于dplyr的`%>%`，`X.`的意思是`df`本身。


```python
df >> select(X.name, X.number)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>number</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Jordan</td>
      <td>23</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Kobe</td>
      <td>24</td>
    </tr>
    <tr>
      <th>2</th>
      <td>O'Neal</td>
      <td>34</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Pippen</td>
      <td>33</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Iverson</td>
      <td>3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Duncan</td>
      <td>21</td>
    </tr>
  </tbody>
</table>
</div>



### 筛选行

用`sift`。


```python
print( df >> sift(X.number < 30) )
print("\n" )
print( df >> sift( (X.number < 30) & (X.pts > 280)) )
print("\n")
print( df 
      >> sift( (X.number < 30) | (X.pts < 250)) 
      >> select(X.name,X.number,X.pts)
     ) 
```

          name    team  number  pts  gs
    0   Jordan   Bulls      23  300  10
    1     Kobe  Lakers      24  280  10
    4  Iverson   76ers       3  290  10
    5   Duncan   Spurs      21  270  10
    
    
          name   team  number  pts  gs
    0   Jordan  Bulls      23  300  10
    4  Iverson  76ers       3  290  10
    
    
          name  number  pts
    0   Jordan      23  300
    1     Kobe      24  280
    3   Pippen      33  240
    4  Iverson       3  290
    5   Duncan      21  270
    

### 随机抽样

用`sample_n`或者`sample_frac`。



```python
df >> sample_n(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>team</th>
      <th>number</th>
      <th>pts</th>
      <th>gs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>Pippen</td>
      <td>Bulls</td>
      <td>33</td>
      <td>240</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Kobe</td>
      <td>Lakers</td>
      <td>24</td>
      <td>280</td>
      <td>10</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Jordan</td>
      <td>Bulls</td>
      <td>23</td>
      <td>300</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
</div>




```python
df >> sample_frac(0.3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>team</th>
      <th>number</th>
      <th>pts</th>
      <th>gs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>O'Neal</td>
      <td>Lakers</td>
      <td>34</td>
      <td>250</td>
      <td>8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Kobe</td>
      <td>Lakers</td>
      <td>24</td>
      <td>280</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
</div>



### 生成新列

用`mutate`。

下面生成每场得分：


```python
df = df >> mutate(ppg=X.pts/X.gs)
print(df)
```

          name    team  number  pts  gs    ppg
    0   Jordan   Bulls      23  300  10  30.00
    1     Kobe  Lakers      24  280  10  28.00
    2   O'Neal  Lakers      34  250   8  31.25
    3   Pippen   Bulls      33  240  10  24.00
    4  Iverson   76ers       3  290  10  29.00
    5   Duncan   Spurs      21  270  10  27.00
    

### 分类汇总

用`group_by`和`summarize`。


```python
(df >>    
 group_by(X.team) >>
 summarize(sum_pts=X.pts.sum(), mean_pts=X.pts.mean())
)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>team</th>
      <th>sum_pts</th>
      <th>mean_pts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>76ers</td>
      <td>290</td>
      <td>290</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bulls</td>
      <td>540</td>
      <td>270</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lakers</td>
      <td>530</td>
      <td>265</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Spurs</td>
      <td>270</td>
      <td>270</td>
    </tr>
  </tbody>
</table>
</div>



### 排序

用`arrange`。


```python
(df >>    
 group_by(X.team) >>
 summarize(sum_pts=X.pts.sum(), mean_pts=X.pts.mean()) >>
 arrange(-X.mean_pts)
)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>team</th>
      <th>sum_pts</th>
      <th>mean_pts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>76ers</td>
      <td>290</td>
      <td>290</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bulls</td>
      <td>540</td>
      <td>270</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Spurs</td>
      <td>270</td>
      <td>270</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lakers</td>
      <td>530</td>
      <td>265</td>
    </tr>
  </tbody>
</table>
</div>



### 当列名有空格

当列名有空格或者特殊符号时，用`X["column name"]`。


```python
df["W/L pct"] = range(len(df))
print( df >> select(X.name, X["W/L pct"]) >> head(3) )
```

         name  W/L pct
    0  Jordan        0
    1    Kobe        1
    2  O'Neal        2
    

### 本位符号

`X._`相当于dplyr的`.`。


```python
print( df >> select(X.name, X.number) >> X._.T )
```

                 0     1       2       3        4       5
    name    Jordan  Kobe  O'Neal  Pippen  Iverson  Duncan
    number      23    24      34      33        3      21
    

### 传递给ggplot

需要用`dplython.DelayFunction`初始化`ggplot`函数。

```python
import ggplot as gg
from pandas import Timestamp
ggplot = DelayFunction(ggplot)
```

无法`import ggplot`，可参考[ImportError: cannot import name 'Timestamp'](https://stackoverflow.com/questions/50591982/importerror-cannot-import-name-timestamp)


## 参考资料

- [github.com/dodger487/dplython](https://github.com/dodger487/dplython)
- [另一个python下的dplyr：rpy2.robjects.lib.dplyr](https://rpy2.readthedocs.io/en/version_2.7.x/lib_dplyr.html)
