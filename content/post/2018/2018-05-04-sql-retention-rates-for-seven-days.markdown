---
title: sql提取7天留存率数据
author: Heaven Zone
date: '2018-05-04'
slug: "sql-retention-rates-for-seven-days"
categories:
  - sql
tags:
  - sql
description: 'sql如何如何提取七天留存率'
keywords: ['7天留存率', '七天留存']
simpletoc: true
---

# 目标

用sql提取7天留存数据（此处7天留存率定义为如果客户在1日注册，在2日至7日存在登录行为的客户被定义为留存客户）。

# 数据表

用mysql创建了一个tysql数据库，有两个表，`reg`表是用户注册表，`log`表是用户登录表。

## reg表结构

```
SELECT * FROM `reg`

+----+------+---------------------+
| id | user | regdate             |
+----+------+---------------------+
| 0  | nn1  | 2018-04-01 00:00:00 |
| 1  | bb1  | 2018-04-01 00:00:00 |
| 2  | bb2  | 2018-04-01 00:00:00 |
| 3  | a2   | 2018-04-02 00:00:00 |
| 4  | a3   | 2018-04-03 00:00:00 |
| 5  | a4   | 2018-04-04 00:00:00 |
| 6  | bb3  | 2018-04-05 00:00:00 |
| 7  | a6   | 2018-04-05 00:00:00 |
| 8  | a7   | 2018-04-06 00:00:00 |
| 9  | a8   | 2018-04-07 00:00:00 |
| 10 | a9   | 2018-04-08 00:00:00 |
| 11 | a10  | 2018-04-09 00:00:00 |
| 12 | a11  | 2018-04-10 00:00:00 |
| 13 | a12  | 2018-04-11 00:00:00 |
| 14 | a13  | 2018-04-12 00:00:00 |
| 15 | a14  | 2018-04-13 00:00:00 |
| 16 | a15  | 2018-04-14 00:00:00 |
| 17 | a16  | 2018-04-15 00:00:00 |
| 18 | a17  | 2018-04-16 00:00:00 |
| 19 | a18  | 2018-04-17 00:00:00 |
| 20 | a19  | 2018-04-18 00:00:00 |
| 21 | a20  | 2018-04-19 00:00:00 |
| 22 | a21  | 2018-04-20 00:00:00 |
| 23 | nn2  | 2018-04-03 00:00:00 |
+----+------+---------------------+
```

## log表结构

```
SELECT * FROM `log`

+----+------+---------------------+
| id | user | logdate             |
+----+------+---------------------+
| 1  | bb1  | 2018-04-02 00:00:00 |
| 2  | bb1  | 2018-04-06 00:00:00 |
| 3  | bb1  | 2018-04-07 00:00:00 |
| 4  | bb2  | 2018-04-08 00:00:00 |
| 5  | ccc4 | 2018-04-09 00:00:00 |
| 6  | nn1  | 2018-04-09 00:00:00 |
| 7  | ccc5 | 2018-04-11 00:00:00 |
| 8  | bb3  | 2018-04-12 00:00:00 |
| 9  | bb1  | 2018-04-13 00:00:00 |
| 10 | bb2  | 2018-04-14 00:00:00 |
| 11 | bb1  | 2018-04-15 00:00:00 |
| 12 | bb2  | 2018-04-16 00:00:00 |
| 13 | bb1  | 2018-04-17 00:00:00 |
| 14 | bb2  | 2018-04-18 00:00:00 |
| 15 | nn2  | 2018-04-03 00:00:00 |
| 16 | bb1  | 2018-04-04 00:00:00 |
| 17 | bb1  | 2018-04-04 00:00:00 |
+----+------+---------------------+
```

- 其中bb1、bb2、bb3是1-7日的7天留存用户
- ccc4在reg表里面并没有出现，当作是4月1日之前注册的用户
- nn1用户在4月1日注册，不过他的登录时间是在4月9日，超过7天，因此不属于4月1日的7天留存客户
- nn2用户在3日注册，也只在3日存在登录，也不属于3日的7天留存客户

# sql提取7天留存率

目标是提取2018年4月1日-2018年4月7日的7天留存率。

## reg提取1日至7日的注册用户

```
select date(regdate) as regdate, reg.user 
from reg 
where regdate >= "2018-04-01" and regdate <= "2018-04-07" 
```

结果输出：

```
+------------+------+
| regdate    | user |
+------------+------+
| 2018-04-01 | nn1  |
| 2018-04-01 | bb1  |
| 2018-04-01 | bb2  |
| 2018-04-02 | a2   |
| 2018-04-03 | a3   |
| 2018-04-04 | a4   |
| 2018-04-05 | bb3  |
| 2018-04-05 | a6   |
| 2018-04-06 | a7   |
| 2018-04-07 | a8   |
| 2018-04-03 | nn2  |
+------------+------+
```


## 从log表过滤出最小登录日期记录

```
select min(date(logdate)) as logdate, user from log group by user 
```

结果输出：

```
+------------+------+
| logdate    | user |
+------------+------+
| 2018-04-02 | bb1  |
| 2018-04-08 | bb2  |
| 2018-04-09 | ccc4 |
| 2018-04-09 | nn1  |
| 2018-04-11 | ccc5 |
| 2018-04-12 | bb3  |
| 2018-04-03 | nn2  |
+------------+------+
```

## 用left outer join合并上面两个表

```
select * 
from ( 
  select date(regdate) as regdate, reg.user from reg 
  where regdate >= "2018-04-01" and regdate <= "2018-04-07" ) as reg 
left outer join (select min(date(logdate)) as logdate, user from log group by user ) as log 
on log.user = reg.user 
```

结果输出：

```
+------------+------+------------+--------+
| regdate    | user | logdate    | user   |
+------------+------+------------+--------+
| 2018-04-01 | nn1  | 2018-04-09 | nn1    |
| 2018-04-01 | bb1  | 2018-04-02 | bb1    |
| 2018-04-01 | bb2  | 2018-04-08 | bb2    |
| 2018-04-02 | a2   | <null>     | <null> |
| 2018-04-03 | a3   | <null>     | <null> |
| 2018-04-04 | a4   | <null>     | <null> |
| 2018-04-05 | bb3  | 2018-04-12 | bb3    |
| 2018-04-05 | a6   | <null>     | <null> |
| 2018-04-06 | a7   | <null>     | <null> |
| 2018-04-07 | a8   | <null>     | <null> |
| 2018-04-03 | nn2  | 2018-04-03 | nn2    |
+------------+------+------------+--------+
```

此时发现nn1, nn2用户也出现在留存结果中，添加条件把它过滤掉：

```
select * 
from ( 
  select date(regdate) as regdate, reg.user from reg 
  where regdate >= "2018-04-01" and regdate <= "2018-04-07" ) as reg 
left outer join (select min(date(logdate)) as logdate, user from log group by user ) as log 
on log.user = reg.user 
and datediff(log.logdate, reg.regdate) <=7 
and datediff(log.logdate, reg.regdate) > 0
```

结果输出：

```
+------------+------+------------+--------+
| regdate    | user | logdate    | user   |
+------------+------+------------+--------+
| 2018-04-01 | nn1  | <null>     | <null> |
| 2018-04-01 | bb1  | 2018-04-02 | bb1    |
| 2018-04-01 | bb2  | 2018-04-08 | bb2    |
| 2018-04-02 | a2   | <null>     | <null> |
| 2018-04-03 | a3   | <null>     | <null> |
| 2018-04-04 | a4   | <null>     | <null> |
| 2018-04-05 | bb3  | 2018-04-12 | bb3    |
| 2018-04-05 | a6   | <null>     | <null> |
| 2018-04-06 | a7   | <null>     | <null> |
| 2018-04-07 | a8   | <null>     | <null> |
| 2018-04-03 | nn2  | <null>     | <null> |
+------------+------+------------+--------+
```

此时，这就是我们想要的结果。

## 计算7天留存率

现在可以通过对regdate分组对logdate列和regdate列计数分别得到留存客户数和新增客户数，然后相除就可以得到留存率。

```
select regdate, count(regdate) as newuser, count(logdate) as stayuser, count(logdate)*1.0/count(regdate) as stayrate
from (
select date(regdate) as regdate, reg.user from reg 
      where regdate >= "2018-04-01" and regdate <= "2018-04-07"
	  ) as reg
left outer join (select min(date(logdate)) as logdate, user from log group by user ) as log 
on log.user = reg.user 
and datediff(log.logdate, reg.regdate) <=7 
and datediff(log.logdate, reg.regdate) > 0
group by regdate
```

结果输出：

```
+------------+---------+----------+----------+
| regdate    | newuser | stayuser | stayrate |
+------------+---------+----------+----------+
| 2018-04-01 | 3       | 2        | 0.66667  |
| 2018-04-02 | 1       | 0        | 0.00000  |
| 2018-04-03 | 2       | 0        | 0.00000  |
| 2018-04-04 | 1       | 0        | 0.00000  |
| 2018-04-05 | 2       | 1        | 0.50000  |
| 2018-04-06 | 1       | 0        | 0.00000  |
| 2018-04-07 | 1       | 0        | 0.00000  |
+------------+---------+----------+----------+
```

# SQL计算**第七天**留存率

```
select * from ( select date(regdate) as regdate, reg.user from reg 
where regdate >= "2018-04-01" and regdate <= "2018-04-07" ) as reg 
  left outer join (select date(logdate) as logdate, user from log ) as log 
      on log.user = reg.user and datediff(log.logdate, reg.regdate) =7
order by regdate
      
+------------+------+------------+--------+
| regdate    | user | logdate    | user   |
+------------+------+------------+--------+
| 2018-04-01 | bb2  | 2018-04-08 | bb2    |
| 2018-04-01 | nn1  | <null>     | <null> |
| 2018-04-01 | bb1  | <null>     | <null> |
| 2018-04-02 | a2   | <null>     | <null> |
| 2018-04-03 | a3   | <null>     | <null> |
| 2018-04-03 | nn2  | <null>     | <null> |
| 2018-04-04 | a4   | <null>     | <null> |
| 2018-04-05 | a6   | <null>     | <null> |
| 2018-04-05 | bb3  | 2018-04-12 | bb3    |
| 2018-04-06 | a7   | <null>     | <null> |
| 2018-04-07 | a8   | <null>     | <null> |
+------------+------+------------+--------+
```

# 用R提取7天留存

```
library(RMySQL)
library(tidyverse)
drv <- dbDriver("MySQL") 

con <- RMySQL::dbConnect(drv, dbname = "krupdb", host="localhost", port=3306, username="username", password="*********")

dbSendQuery(con,'SET NAMES gbk')

df.reg <- dbGetQuery(con, "select user, regdate from reg")
df.log <- dbGetQuery(con, "select user, logdate from log")
df.reg$regdate <- as.Date(df.reg$regdate)
df.log$logdate <- as.Date(df.log$logdate)
df.log

df.log.f <- df.log %>% 
  group_by(user) %>%
  dplyr::filter(logdate == min(logdate))
df.log.f

df.reg %>% 
  dplyr::filter(regdate>="2018-04-01", regdate<="2018-04-07") %>%
  left_join(df.log.f, by = "user") %>%
  mutate(isold = ifelse(!is.na(logdate) & logdate <= regdate+7 & logdate > regdate, 1, 0)) %>%
  group_by(regdate) %>%
  summarise(留存 = sum(isold), 新增 = n()) %>%
  mutate(留存率 = round(留存/新增, 3))
```

最后得到**七天留存率**结果：

```
# A tibble: 7 x 4
  regdate     留存  新增 留存率
  <date>     <dbl> <int>  <dbl>
1 2018-04-01    2.     3  0.667
2 2018-04-02    0.     1  0.   
3 2018-04-03    0.     2  0.   
4 2018-04-04    0.     1  0.   
5 2018-04-05    1.     2  0.500
6 2018-04-06    0.     1  0.   
7 2018-04-07    0.     1  0.
```


## 插曲

上面代码在本地mysql服务器测试没问题，然后尝试在**db4free.net**上建立连接，发现出现下面错误：

```
> con <- RMySQL::dbConnect(drv, dbname = "krupdb", host="db4free.net", port=3306, username="user", password="*******")

Error in .local(drv, ...) : 
  Failed to connect to database: Error: Can't initialize character set unknown (path: compiled_in)
```

后来查找了一下，发现应该是RMySQL不支持最新版的`mysql 8`，详情可参考[这里](https://stackoverflow.com/questions/50053961/error-r-cannot-connect-to-mysql)，该帖子建议可以使用包**[RMariaDB](https://cran.r-project.org/web/packages/RMariaDB/)**。

好，试试看吧，这次我们尝试获取**第七天留存率**。

```
library(DBI)
library(RMariaDB)
library(tidyverse)
con <- dbConnect(RMariaDB::MariaDB(), 
                 dbname = "krupdb", 
                 port=3306,
                 host = "db4free.net",
                 user = "username", 
                 password = "******")


dbSendQuery(con,'SET NAMES gbk')

df.reg <- dbGetQuery(con, "select user, regdate from reg")
df.log <- dbGetQuery(con, "select user, logdate from log")
df.reg$regdate <- as.Date(df.reg$regdate)
df.log$logdate <- as.Date(df.log$logdate)

# 求出注册用户的第七天
df.reg.7 <- df.reg %>% 
  dplyr::filter(regdate>="2018-04-01", regdate<="2018-04-07") %>%
  transmute(user = user, logdate = regdate + 7) %>% unique() 
df.reg.7

# 求出注册后第七天有登录的用户
df.log.7 <- df.log %>% semi_join(df.reg.7)

# 得出1-7日登录的用户和第7天有登录的用户
df.7 <- df.reg %>% 
  dplyr::filter(regdate>="2018-04-01", regdate<="2018-04-07") %>%
  left_join(df.log.7, by = "user") %>%
  mutate(isold = ifelse(!is.na(logdate), 1, 0)) 
df.7

```

```
   user    regdate    logdate isold
1   nn1 2018-04-01       <NA>     0
2   bb1 2018-04-01       <NA>     0
3   bb2 2018-04-01 2018-04-08     1
4    a2 2018-04-02       <NA>     0
5    a3 2018-04-03       <NA>     0
6    a4 2018-04-04       <NA>     0
7   bb3 2018-04-05 2018-04-12     1
8    a6 2018-04-05       <NA>     0
9    a7 2018-04-06       <NA>     0
10   a8 2018-04-07       <NA>     0
11  nn2 2018-04-03       <NA>     0
```

计算出近7天留存率

```
df.7 %>%
  group_by(regdate) %>%
  summarise(留存 = sum(isold), 新增 = n()) %>%
  mutate(留存率 = round(留存/新增, 3))
```

```
# A tibble: 7 x 4
  regdate     留存  新增 留存率
  <date>     <dbl> <int>  <dbl>
1 2018-04-01    1.     3  0.333
2 2018-04-02    0.     1  0.   
3 2018-04-03    0.     2  0.   
4 2018-04-04    0.     1  0.   
5 2018-04-05    1.     2  0.500
6 2018-04-06    0.     1  0.   
7 2018-04-07    0.     1  0.  
```


# 群晖的MariaDB插曲

原来群晖的MariaDB默认端口是**3307**，一直试3306连不成功。

```
root@DiskStation:/volume1/@appstore/MariaDB10/usr/local/mariadb10/etc/mysql# cat my.cnf | head
[client]
port = 3307
socket = /run/mysqld/mysqld10.sock

[mysqld]
bind-address = 0.0.0.0
port = 3307
socket = /run/mysqld/mysqld10.sock
pid-file = /run/mysqld/mysqld10.pid
skip-external-locking
```




# 网上资料

留待参考。

## 留存用户数

```
select final.regdate, newuser, stay1, stay3, stay7
FROM
(
    select regdate, sum(stay1) as stay1, sum(stay3) as stay3, sum(stay7) as stay7
    from (select 
        reg.regdate, reg.user, DATE(logdate) as logdate, DATEDIFF(DATE(logdate), regdate) as diff,
            if(datediff(date(logdate), reg.regdate) = 1, 1, 0) as stay1,
            if(DATEDIFF(DATE(logdate),reg.regdate)= 3,1,0) as stay3,
            if(DATEDIFF(DATE(logdate),reg.regdate)= 7,1,0) as stay7
        from 
          (
          	select log.user, min(log.logdate) as logdate from log group by log.logdate
          ) as log
          right outer join (select date(reg.regdate) as regdate, user
                                   from reg 
                                   where date(reg.regdate) between "2018-04-01" and "2018-04-07"
                    group by date(reg.regdate), reg.user
                    order by date(reg.regdate)) as reg
        on log.user = reg.user
     ) as logdiff
    group by regdate
) as final right outer join (
select min(date(reg.regdate)) as regdate, count(reg.regdate) as newuser
    from reg
    where date(reg.regdate) between "2018-04-01" and "2018-04-07"
    group by date(reg.regdate)
) as reg2 
on reg2.regdate = final.regdate
```

## 留存率

```
select final.regdate, stay1/newuser as stay1, stay3/newuser as stay3, stay7/newuser as stay7
FROM
(
    select regdate, sum(stay1) as stay1, sum(stay3) as stay3, sum(stay7) as stay7
    from (select 
        reg.regdate, reg.user, DATE(logdate) as logdate, DATEDIFF(DATE(logdate), regdate) as diff,
            if(datediff(date(logdate), reg.regdate) = 1, 1, 0) as stay1,
            if(DATEDIFF(DATE(logdate),reg.regdate)= 3,1,0) as stay3,
            if(DATEDIFF(DATE(logdate),reg.regdate)= 7,1,0) as stay7
        from 
          (
          	select log.user, min(log.logdate) as logdate from log group by log.logdate
          ) as log
          right outer join (select date(reg.regdate) as regdate, user
                                   from reg 
                                   where date(reg.regdate) between "2018-04-01" and "2018-04-07"
                    group by date(reg.regdate), reg.user
                    order by date(reg.regdate)) as reg
        on log.user = reg.user
     ) as logdiff
    group by regdate
) as final right outer join (
select min(date(reg.regdate)) as regdate, count(reg.regdate) as newuser
    from reg
    where date(reg.regdate) between "2018-04-01" and "2018-04-07"
    group by date(reg.regdate)
) as reg2 
on reg2.regdate = final.regdate
```
