---
title: 把R语言的dplyr语句转化成sql语句
author: Heaven Zone
date: '2018-06-07'
slug: convert-dplyr-to-sql
categories:
  - R
tags:
  - R
  - sql
  - dplyr
  - tidyverse
---

之前看到过一个可以将dplyr语句转化成sql语句的扩展包，搜索了一下，找到了，是`dbplyr`，看名字就知道它是什么家伙了。

# 把dplyr语句转化成sql语句

测试一下：

```
library(DBI)
library(RMariaDB)
library(tidyverse)
library(dbplyr)

con <- dbConnect(RMariaDB::MariaDB(), 
                 dbname = "krupdb", 
                 port=3306,
                 host = "db4free.net",
                 user = "username", 
                 password = "password")

df.log <- dbGetQuery(con, "select user, logdate from log")
df.log$logdate <- as.Date(df.log$logdate)
df.log %>% head

# 需要把数据转化成mysql类型
df.log.sql <- dplyr::copy_to(con, df.log)
class(df.log.sql)
```

```
[1] "tbl_dbi"  "tbl_sql"  "tbl_lazy" "tbl"
```

试试生成新的列，

```
df.log.sql %>% 
  mutate(tmp = ifelse(logdate>"2018-04-10", 1, 0))
```

```
# Source:   lazy query [?? x 3]
# Database: mysql 8.0.11
#   [heavenzone@db4free.net:/krupdb]
   user  logdate      tmp
   <chr> <date>     <dbl>
 1 bb1   2018-04-02    0.
 2 bb1   2018-04-06    0.
 3 bb1   2018-04-07    0.
 4 bb2   2018-04-08    0.
 5 ccc4  2018-04-09    0.
 6 nn1   2018-04-09    0.
 7 ccc5  2018-04-11    1.
 8 bb3   2018-04-12    1.
 9 bb1   2018-04-13    1.
10 bb2   2018-04-14    1.
# ... with more rows
```

现在来看一下用dbplyr把上面语句转换成sql语句：

```
df.log.sql %>% 
  mutate(tmp = ifelse(logdate>"2018-04-10", 1, 0)) %>%
  show_query()
```

```
<SQL>
SELECT `user`, `logdate`, 
  CASE WHEN (`logdate` > '2018-04-10') 
  THEN (1.0) WHEN NOT(`logdate` > '2018-04-10') 
  THEN (0.0) END AS `tmp`
FROM `df.log`
```

把上面sql语句复制到sql命令行输出（需要把表名`df.log`修改为正确的表名`log`）：

```
mysql> SELECT `user`, `logdate`,
   ->   CASE WHEN (`logdate` > '2018-04-10')
   ->   THEN (1.0) WHEN NOT(`logdate` > '2018-04-10')
   ->   THEN (0.0) END AS `tmp`
   -> FROM `log`
   
+------+---------------------+-----+
| user | logdate             | tmp |
+------+---------------------+-----+
| bb1  | 2018-04-02 00:00:00 | 0.0 |
| bb1  | 2018-04-06 00:00:00 | 0.0 |
| bb1  | 2018-04-07 00:00:00 | 0.0 |
| bb2  | 2018-04-08 00:00:00 | 0.0 |
| ccc4 | 2018-04-09 00:00:00 | 0.0 |
| nn1  | 2018-04-09 00:00:00 | 0.0 |
| ccc5 | 2018-04-11 00:00:00 | 1.0 |
| bb3  | 2018-04-12 00:00:00 | 1.0 |
| bb1  | 2018-04-13 00:00:00 | 1.0 |
| bb2  | 2018-04-14 00:00:00 | 1.0 |
| bb1  | 2018-04-15 00:00:00 | 1.0 |
| bb2  | 2018-04-16 00:00:00 | 1.0 |
| bb1  | 2018-04-17 00:00:00 | 1.0 |
| bb2  | 2018-04-18 00:00:00 | 1.0 |
| nn2  | 2018-04-03 00:00:00 | 0.0 |
| bb1  | 2018-04-04 00:00:00 | 0.0 |
| bb1  | 2018-04-04 00:00:00 | 0.0 |
| ccc5 | 2018-04-12 00:00:00 | 1.0 |
| ccc5 | 2018-04-13 00:00:00 | 1.0 |
+------+---------------------+-----+
```

输出结果跟R中输出一样。


# 获取最近登录的用户和时间

## dplyr实现

用dplyr很容易就可以把分组的最大值找出来，可是sql就不是那样子了。

把最近一次登录的用户找出来：

```
df.log %>% 
  dplyr::filter(logdate == max(logdate))
```

```
  user    logdate
1  bb2 2018-04-18
```

用R来实现很容易，直接`logdate == max(logdate)`就可以了。


## sql实现

看看用sql是怎么实现的：

```
select max(logdate) from log
```

上面语句可以把最近一次登录的时间找出来，

```
+---------------------+
| max(logdate)        |
+---------------------+
| 2018-04-18 00:00:00 |
+---------------------+
```

但是并没有用户名字打出来，如果在select后面增加`user`字段，

```
select user, max(logdate) from log

(1140, "In aggregated query without GROUP BY, expression
#1 of SELECT list contains nonaggregated column 'krupdb.l
og.user'; this is incompatible with sql_mode=only_full_gr
oup_by")
```

系统提示错误，正确的做法：

```
select user, logdate from log where logdate= (select max(logdate) from log)
   
+------+---------------------+
| user | logdate             |
+------+---------------------+
| bb2  | 2018-04-18 00:00:00 |
+------+---------------------+
```

# 获取每个用户最近登录的时间

## R实现

```
df.log %>%
  group_by(user) %>%
  dplyr::filter(logdate == max(logdate)) %>%
  arrange(logdate)
```

```
# A tibble: 7 x 2
# Groups:   user [7]
  user  logdate   
  <chr> <date>    
1 nn2   2018-04-03
2 ccc4  2018-04-09
3 nn1   2018-04-09
4 bb3   2018-04-12
5 ccc5  2018-04-13
6 bb1   2018-04-17
7 bb2   2018-04-18
```

## sql实现

这次并不需要用到sql的子查询语句，思路跟dplyr差不多。

```
mysql>select user, max(logdate) from log group by user
+------+---------------------+
| user | max(logdate)        |
+------+---------------------+
| bb1  | 2018-04-17 00:00:00 |
| bb2  | 2018-04-18 00:00:00 |
| ccc4 | 2018-04-09 00:00:00 |
| nn1  | 2018-04-09 00:00:00 |
| ccc5 | 2018-04-13 00:00:00 |
| bb3  | 2018-04-12 00:00:00 |
| nn2  | 2018-04-03 00:00:00 |
+------+---------------------+
```

## dbplyr转换

```
df.log.sql %>%
  group_by(user) %>%
  dplyr::filter(logdate == max(logdate)) %>%
  arrange(logdate) %>%
  show_query()
```

结果出错了，

```
Error: Window function `max()` is not supported by this database
```

看来dbplyr并不能把每个dplyr语句都转化成sql语句。

用这个来学习sql貌似不错，一些较复杂的也可以尝试用dbplyr来转换，例如上面的ifelse，用dplyr来实现容易，写sql相对复杂一点。

# 参考资料

- <http://dbplyr.tidyverse.org/articles/sql-translation.html>
