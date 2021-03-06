---
title: mysql常用语句
author: Heaven Zone
date: '2018-06-06'
slug: mysql-common-sentences
categories:
  - mysql
tags:
  - mysql
  - sql
---


# 自建数据库

## 数据库结构

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/2018/2018-06-06-mysql-testdb-design.png)


## user表

```
mysql>select * from user;
+--------+----------+-----+-----+
| userid | name     | age | sex |
+--------+----------+-----+-----+
| 1      | Jim      | 20  | M   |
| 2      | Jack     | 21  | M   |
| 3      | Tom      | 30  | M   |
| 4      | Mike     | 31  | M   |
| 5      | Joe      | 18  | M   |
| 6      | Betty    | 23  | F   |
| 7      | Rose     | 25  | F   |
| 8      | Shirley  | 19  | F   |
| 9      | Barbie   | 17  | F   |
| 10     | Nancy    | 28  | F   |
+--------+----------+-----+-----+
```

## log表

```
mysql> select * from log;
+-------+------------+--------+
| logid | date       | userid |
+-------+------------+--------+
| 1     | 2018-05-02 | 1      |
| 2     | 2018-05-03 | 5      |
| 3     | 2018-05-05 | 3      |
| 4     | 2018-05-05 | 9      |
| 5     | 2018-05-06 | 5      |
| 6     | 2018-05-07 | 3      |
| 7     | 2018-05-08 | 1      |
| 8     | 2018-05-08 | 2      |
| 9     | 2018-05-08 | 4      |
| 10    | 2018-05-09 | 9      |
| 11    | 2018-05-10 | 3      |
| 12    | 2018-05-11 | 1      |
| 13    | 2018-05-12 | 2      |
| 14    | 2018-05-13 | 4      |
+-------+------------+--------+
```

## reg表

```
mysql> select * from reg;
+-------+------------+--------+
| regid | date       | userid |
+-------+------------+--------+
| 1     | 2018-05-01 | 1      |
| 2     | 2018-05-02 | 3      |
| 3     | 2018-05-02 | 6      |
| 4     | 2018-05-03 | 5      |
| 5     | 2018-05-03 | 9      |
| 6     | 2018-05-05 | 2      |
| 7     | 2018-05-05 | 7      |
| 8     | 2018-05-06 | 8      |
| 9     | 2018-05-08 | 4      |
| 10    | 2018-05-09 | 10     |
+-------+------------+--------+
```


## item表

```
mysql> select * from item;
+--------+----------+------+-------+--------+
| itemid | name     | cate | price | shopid |
+--------+----------+------+-------+--------+
| 1      | SSD硬盘  | 电脑 | 500   | 1      |
| 2      | 内存     | 电脑 | 380   | 1      |
| 3      | 哈密瓜   | 水果 | 20    | 2      |
| 4      | 苹果     | 水果 | 15    | 2      |
| 5      | 瘦肉蒸面 | 早餐 | 6     | 3      |
| 6      | 蛋肉肠   | 早餐 | 5     | 3      |
| 7      | 外套     | 服装 | 100   | 4      |
| 8      | T-shirt  | 服装 | 80    | 4      |
| 9      | 叉鹅饭   | 主菜 | 22    | 5      |
| 10     | 红烧茄子 | 主菜 | 18    | 5      |
| 11     | 香蕉     | 水果 | 8     | 2      |
+--------+----------+------+-------+--------+
```

## shop表

```
mysql> select * from shop;
+--------+----------------+---------+
| shopid | name           | ownerid |
+--------+----------------+---------+
| 1      | 深蓝电脑店     | 1       |
| 2      | 天天水果店     | 3       |
| 3      | 西关肠粉早餐店 | 5       |
| 4      | 美人伊装       | 7       |
| 5      | 满华烧鹅店     | 9       |
+--------+----------------+---------+
```

## orders表


```
mysql> select * from orders;
+---------+--------+--------+----------+---------------------+
| orderid | userid | itemid | quantity | datetime            |
+---------+--------+--------+----------+---------------------+
| 1       | 1      | 1      | 1        | 2018-05-02 00:00:00 |
| 2       | 5      | 2      | 2        | 2018-05-03 00:00:00 |
| 3       | 3      | 3      | 2        | 2018-05-05 00:00:00 |
| 4       | 9      | 4      | 1        | 2018-05-05 00:00:00 |
| 5       | 5      | 5      | 2        | 2018-05-06 00:00:00 |
| 6       | 3      | 6      | 3        | 2018-05-07 00:00:00 |
| 7       | 1      | 7      | 5        | 2018-05-08 00:00:00 |
| 8       | 2      | 8      | 2        | 2018-05-08 00:00:00 |
| 9       | 4      | 9      | 3        | 2018-05-08 00:00:00 |
| 10      | 9      | 10     | 2        | 2018-05-09 00:00:00 |
| 11      | 3      | 6      | 3        | 2018-05-10 00:00:00 |
| 12      | 1      | 7      | 5        | 2018-05-11 00:00:00 |
| 13      | 2      | 8      | 2        | 2018-05-12 00:00:00 |
| 14      | 4      | 9      | 3        | 2018-05-13 00:00:00 |
+---------+--------+--------+----------+---------------------+
```

# 常规查询


### 查询有开店的user

```
mysql> select user.name username, age, sex, shop.name shopname from user, shop 
where user.userid = shop.ownerid;

+----------+-----+-----+----------------+
| username | age | sex | shopname       |
+----------+-----+-----+----------------+
| Jim      | 20  | M   | 深蓝电脑店     |
| Tom      | 30  | M   | 天天水果店     |
| Joe      | 18  | M   | 西关肠粉早餐店 |
| Rose     | 25  | F   | 美人伊装       |
| Barbie   | 17  | F   | 满华烧鹅店     |
+----------+-----+-----+----------------+
```

上面也可以用：

```
select user.name username, age, sex, shop.name shopname from user
inner join shop on user.userid = shop.ownerid
```

结果一样。


把所有user都输出，并输出开的店铺：

```
mysql> select user.name username, age, sex, shop.name shopname from user
   -> left join shop on user.userid = shop.ownerid
   -> order by age;
   
+----------+-----+-----+----------------+
| username | age | sex | shopname       |
+----------+-----+-----+----------------+
| Barbie   | 17  | F   | 满华烧鹅店     |
| Joe      | 18  | M   | 西关肠粉早餐店 |
| Shirley  | 19  | F   | <null>         |
| Jim      | 20  | M   | 深蓝电脑店     |
| Jack     | 21  | M   | <null>         |
| Betty    | 23  | F   | <null>         |
| Rose     | 25  | F   | 美人伊装       |
| Nancy    | 28  | F   | <null>         |
| Tom      | 30  | M   | 天天水果店     |
| Mike     | 31  | M   | <null>         |
+----------+-----+-----+----------------+
```


## 查询orders

查询orders表，并输出item名称、店铺名称、并计算出总额：

```
mysql> select user.name username, age, sex, item.name itemName,
   -> cate, price, quantity, price*quantity as total, shop.name
   -> from orders, user, item, shop
   -> where user.userid = orders.userid
   -> and orders.itemid = item.itemid
   -> and item.shopid = shop.shopid
   -> order by total desc;
+----------+-----+-----+----------+------+-------+----------+-------+----------------+
| username | age | sex | itemName | cate | price | quantity | total | name           |
+----------+-----+-----+----------+------+-------+----------+-------+----------------+
| Joe      | 18  | M   | 内存     | 电脑 | 380   | 2        | 760   | 深蓝电脑店     |
| Jim      | 20  | M   | 外套     | 服装 | 100   | 5        | 500   | 美人伊装       |
| Jim      | 20  | M   | 外套     | 服装 | 100   | 5        | 500   | 美人伊装       |
| Jim      | 20  | M   | SSD硬盘  | 电脑 | 500   | 1        | 500   | 深蓝电脑店     |
| Jack     | 21  | M   | T-shirt  | 服装 | 80    | 2        | 160   | 美人伊装       |
| Jack     | 21  | M   | T-shirt  | 服装 | 80    | 2        | 160   | 美人伊装       |
| Mike     | 31  | M   | 叉鹅饭   | 主菜 | 22    | 3        | 66    | 满华烧鹅店     |
| Mike     | 31  | M   | 叉鹅饭   | 主菜 | 22    | 3        | 66    | 满华烧鹅店     |
| Tom      | 30  | M   | 哈密瓜   | 水果 | 20    | 2        | 40    | 天天水果店     |
| Barbie   | 17  | F   | 红烧茄子 | 主菜 | 18    | 2        | 36    | 满华烧鹅店     |
| Barbie   | 17  | F   | 苹果     | 水果 | 15    | 1        | 15    | 天天水果店     |
| Tom      | 30  | M   | 蛋肉肠   | 早餐 | 5     | 3        | 15    | 西关肠粉早餐店 |
| Tom      | 30  | M   | 蛋肉肠   | 早餐 | 5     | 3        | 15    | 西关肠粉早餐店 |
| Joe      | 18  | M   | 瘦肉蒸面 | 早餐 | 6     | 2        | 12    | 西关肠粉早餐店 |
+----------+-----+-----+----------+------+-------+----------+-------+----------------+
```

## 查询出user表男女年龄最大的user

```
mysql> select * from user where age in ( select max(age) from user group by sex );
+--------+-------+-----+-----+
| userid | name  | age | sex |
+--------+-------+-----+-----+
|      4 | Mike  |  31 | M   |
|     10 | Nancy |  28 | F   |
+--------+-------+-----+-----+
```







