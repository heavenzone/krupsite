---
title: python pandas DataFrame笔记
author: Heaven Zone
date: '2018-07-25'
slug: python-pandas-dataframe-notes
categories:
  - python
tags:
  - python
  - pandas
  - DataFrame
simpletoc: true  
---


# python pandas DataFrame 笔记


```python
import os
import numpy as np
import pandas as pd

```

## 创建DataFrame


```python
nbadata = {"name": ["Kobe", "Jordan", "Iverson", "T-mac", "O'Neal", "Duncan", "Manu"], 
        "team": ["Lakers", "Bulls", "76ers", "Rockets", "Lakers", "Spurs", "Spurs"], 
        "number": [8,23,3,1,34,21,20]}

nbadf1 = pd.DataFrame(nbadata, columns=['name', 'number'])
print(nbadf1, "\n")

nbadf2 = pd.DataFrame(np.arange(9).reshape((3,3)), 
                      index = ['one', 'two', 'three'],
                      columns = ['pts','rebs','asts'])
print(nbadf2, "\n")

nbadf = pd.DataFrame(nbadata)
print(nbadf.values, "\n")
print(nbadf.columns, "\n")
```

          name  number
    0     Kobe       8
    1   Jordan      23
    2  Iverson       3
    3    T-mac       1
    4   O'Neal      34
    5   Duncan      21
    6     Manu      20 
    
           pts  rebs  asts
    one      0     1     2
    two      3     4     5
    three    6     7     8 
    
    [['Kobe' 8 'Lakers']
     ['Jordan' 23 'Bulls']
     ['Iverson' 3 '76ers']
     ['T-mac' 1 'Rockets']
     ["O'Neal" 34 'Lakers']
     ['Duncan' 21 'Spurs']
     ['Manu' 20 'Spurs']] 
    
    Index(['name', 'number', 'team'], dtype='object') 
    
    

## pandas的DataFrame列的选取


```python
# 选取DataFrame的列
print(nbadf.name, "\n")
print(nbadf["name"], "\n")
```

    0       Kobe
    1     Jordan
    2    Iverson
    3      T-mac
    4     O'Neal
    5     Duncan
    6       Manu
    Name: name, dtype: object 
    
    0       Kobe
    1     Jordan
    2    Iverson
    3      T-mac
    4     O'Neal
    5     Duncan
    6       Manu
    Name: name, dtype: object 
    
    


```python
print(nbadf[["name", "team"]], "\n")
print(nbadf[["name"]], "\n")
```

          name     team
    0     Kobe   Lakers
    1   Jordan    Bulls
    2  Iverson    76ers
    3    T-mac  Rockets
    4   O'Neal   Lakers
    5   Duncan    Spurs
    6     Manu    Spurs 
    
          name
    0     Kobe
    1   Jordan
    2  Iverson
    3    T-mac
    4   O'Neal
    5   Duncan
    6     Manu 
    
    


```python
print(nbadf.loc[:, "number"], "\n")
print(nbadf.loc[:, ["name","number"]])
```

    0     8
    1    23
    2     3
    3     1
    4    34
    5    21
    6    20
    Name: number, dtype: int64 
    
          name  number
    0     Kobe       8
    1   Jordan      23
    2  Iverson       3
    3    T-mac       1
    4   O'Neal      34
    5   Duncan      21
    6     Manu      20
    


```python
print(nbadf.iloc[:, 0:3], "\n")
```

          name  number     team
    0     Kobe       8   Lakers
    1   Jordan      23    Bulls
    2  Iverson       3    76ers
    3    T-mac       1  Rockets
    4   O'Neal      34   Lakers
    5   Duncan      21    Spurs
    6     Manu      20    Spurs 
    
    


```python
print(nbadf.ix[:, 1:3], "\n")
print(nbadf.ix[:, ["team", "name"]], "\n")
```

       number     team
    0       8   Lakers
    1      23    Bulls
    2       3    76ers
    3       1  Rockets
    4      34   Lakers
    5      21    Spurs
    6      20    Spurs 
    
          team     name
    0   Lakers     Kobe
    1    Bulls   Jordan
    2    76ers  Iverson
    3  Rockets    T-mac
    4   Lakers   O'Neal
    5    Spurs   Duncan
    6    Spurs     Manu 
    
    

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: DeprecationWarning: 
    .ix is deprecated. Please use
    .loc for label based indexing or
    .iloc for positional indexing
    
    See the documentation here:
    http://pandas.pydata.org/pandas-docs/stable/indexing.html#ix-indexer-is-deprecated
      """Entry point for launching an IPython kernel.
    

## 选取DataFrame的行


```python
# 选取DataFrame的行
print(nbadf[:], "\n")

print(nbadf[0:4], "\n")
print(nbadf[::-1], "\n")

print(nbadf[0:2], "\n")
```

          name  number     team
    0     Kobe       8   Lakers
    1   Jordan      23    Bulls
    2  Iverson       3    76ers
    3    T-mac       1  Rockets
    4   O'Neal      34   Lakers
    5   Duncan      21    Spurs
    6     Manu      20    Spurs 
    
          name  number     team
    0     Kobe       8   Lakers
    1   Jordan      23    Bulls
    2  Iverson       3    76ers
    3    T-mac       1  Rockets 
    
          name  number     team
    6     Manu      20    Spurs
    5   Duncan      21    Spurs
    4   O'Neal      34   Lakers
    3    T-mac       1  Rockets
    2  Iverson       3    76ers
    1   Jordan      23    Bulls
    0     Kobe       8   Lakers 
    
         name  number    team
    0    Kobe       8  Lakers
    1  Jordan      23   Bulls 
    
    


```python
print(nbadf.loc[0:4:2], "\n")
print(nbadf.iloc[0:4:2], "\n")
```

          name  number    team
    0     Kobe       8  Lakers
    2  Iverson       3   76ers
    4   O'Neal      34  Lakers 
    
          name  number    team
    0     Kobe       8  Lakers
    2  Iverson       3   76ers 
    
    


```python
print(nbadf.loc[0:4:2, ], "\n")

print(nbadf.loc[[0,2,3], ], "\n")
print(nbadf.iloc[[3,2,1], ], "\n")
print(nbadf.loc[1], "\n")
```

          name  number    team
    0     Kobe       8  Lakers
    2  Iverson       3   76ers
    4   O'Neal      34  Lakers 
    
          name  number     team
    0     Kobe       8   Lakers
    2  Iverson       3    76ers
    3    T-mac       1  Rockets 
    
          name  number     team
    3    T-mac       1  Rockets
    2  Iverson       3    76ers
    1   Jordan      23    Bulls 
    
    name      Jordan
    number        23
    team       Bulls
    Name: 1, dtype: object 
    
    


```python
print(nbadf.ix[2,], "\n")
print(nbadf.ix[[2,3]], "\n")
print(nbadf.ix[[2,0],], "\n")
```

    name      Iverson
    number          3
    team        76ers
    Name: 2, dtype: object 
    
          name  number     team
    2  Iverson       3    76ers
    3    T-mac       1  Rockets 
    
          name  number    team
    2  Iverson       3   76ers
    0     Kobe       8  Lakers 
    
    


```python
dftest = nbadf.copy()
dftest.index = ["one", "two", "three", "four", "five", "six", "seven"]

print(dftest)

print(dftest[0:5:3], "\n")
# 错误：print(dftest[["one"]], "\n")
```

              name  number     team
    one       Kobe       8   Lakers
    two     Jordan      23    Bulls
    three  Iverson       3    76ers
    four     T-mac       1  Rockets
    five    O'Neal      34   Lakers
    six     Duncan      21    Spurs
    seven     Manu      20    Spurs
           name  number     team
    one    Kobe       8   Lakers
    four  T-mac       1  Rockets 
    
    


```python
print(dftest.loc[["one", "two"]], "\n")
print(dftest.loc[["one", "two"],], "\n")
print(dftest.iloc[[0,1]], "\n")
print(dftest.iloc[[2,1], ], "\n")
```

           name  number    team
    one    Kobe       8  Lakers
    two  Jordan      23   Bulls 
    
           name  number    team
    one    Kobe       8  Lakers
    two  Jordan      23   Bulls 
    
           name  number    team
    one    Kobe       8  Lakers
    two  Jordan      23   Bulls 
    
              name  number   team
    three  Iverson       3  76ers
    two     Jordan      23  Bulls 
    
    


```python
print(dftest.ix[[2,3], ], "\n")
print(dftest.ix[["two", "three"], ], "\n")
print(dftest.ix[[2,"three"], ], "\n")
```

              name  number     team
    three  Iverson       3    76ers
    four     T-mac       1  Rockets 
    
              name  number   team
    two     Jordan      23  Bulls
    three  Iverson       3  76ers 
    
              name  number   team
    2          NaN     NaN    NaN
    three  Iverson     3.0  76ers 
    
    

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: DeprecationWarning: 
    .ix is deprecated. Please use
    .loc for label based indexing or
    .iloc for positional indexing
    
    See the documentation here:
    http://pandas.pydata.org/pandas-docs/stable/indexing.html#ix-indexer-is-deprecated
      """Entry point for launching an IPython kernel.
    

## 同时选取DataFrame的行列


```python
print(nbadf["name"][2:5], "\n")
print(nbadf[2:3]["name"], "\n")
```

    2    Iverson
    3      T-mac
    4     O'Neal
    Name: name, dtype: object 
    
    2    Iverson
    Name: name, dtype: object 
    
    


```python
print(nbadf.loc[0:2, ["name", "number"]], "\n")
print(nbadf.iloc[0:2, [1,0]], "\n")
```

          name  number
    0     Kobe       8
    1   Jordan      23
    2  Iverson       3 
    
       number    name
    0       8    Kobe
    1      23  Jordan 
    
    


```python
print(nbadf.iloc[1:2, 0:3], "\n")
print(nbadf.iloc[5:1:-1, 3::-1], "\n")
```

         name  number   team
    1  Jordan      23  Bulls 
    
          team  number     name
    5    Spurs      21   Duncan
    4   Lakers      34   O'Neal
    3  Rockets       1    T-mac
    2    76ers       3  Iverson 
    
    


```python
print(nbadf.ix[5:1:-1, ["name"]], "\n")
```

          name
    5   Duncan
    4   O'Neal
    3    T-mac
    2  Iverson
    1   Jordan 
    
    

## 删除列


```python
nbadf["pts"] = 30
print(nbadf, "\n")
del nbadf["pts"]
print(nbadf)
```

          name  number     team  pts
    0     Kobe       8   Lakers   30
    1   Jordan      23    Bulls   30
    2  Iverson       3    76ers   30
    3    T-mac       1  Rockets   30
    4   O'Neal      34   Lakers   30
    5   Duncan      21    Spurs   30
    6     Manu      20    Spurs   30 
    
          name  number     team
    0     Kobe       8   Lakers
    1   Jordan      23    Bulls
    2  Iverson       3    76ers
    3    T-mac       1  Rockets
    4   O'Neal      34   Lakers
    5   Duncan      21    Spurs
    6     Manu      20    Spurs
    

## 筛选


```python
print(nbadf[nbadf > 20])
nbadf[nbadf["number"]>20]
```

          name  number     team
    0     Kobe     NaN   Lakers
    1   Jordan    23.0    Bulls
    2  Iverson     NaN    76ers
    3    T-mac     NaN  Rockets
    4   O'Neal    34.0   Lakers
    5   Duncan    21.0    Spurs
    6     Manu     NaN    Spurs
    




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>number</th>
      <th>team</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Jordan</td>
      <td>23</td>
      <td>Bulls</td>
    </tr>
    <tr>
      <th>4</th>
      <td>O'Neal</td>
      <td>34</td>
      <td>Lakers</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Duncan</td>
      <td>21</td>
      <td>Spurs</td>
    </tr>
  </tbody>
</table>
</div>



## 添加行


```python
s = pd.Series({"name":"Cury", "number":30,"team": "Worrior"})

nbadf.append(s, True)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>number</th>
      <th>team</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kobe</td>
      <td>8</td>
      <td>Lakers</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jordan</td>
      <td>23</td>
      <td>Bulls</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iverson</td>
      <td>3</td>
      <td>76ers</td>
    </tr>
    <tr>
      <th>3</th>
      <td>T-mac</td>
      <td>1</td>
      <td>Rockets</td>
    </tr>
    <tr>
      <th>4</th>
      <td>O'Neal</td>
      <td>34</td>
      <td>Lakers</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Duncan</td>
      <td>21</td>
      <td>Spurs</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Manu</td>
      <td>20</td>
      <td>Spurs</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Cury</td>
      <td>30</td>
      <td>Worrior</td>
    </tr>
  </tbody>
</table>
</div>


