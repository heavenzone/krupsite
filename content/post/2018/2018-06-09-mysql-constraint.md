---
title: MySQL和MariaDB的约束
author: Heaven Zone
date: '2018-06-09'
slug: mysql-constraint
categories:
  - MySQL
tags:
  - MySQL
  - MariaDB
  - sql
simpletoc: true
---

MySQL和MariaDB的约束一共有如下几种：

- 主键
- 外键
- 唯一约束
- 非空约束
- 检查约束


# 主键

## 适合做主键的条件

满足以下几个条件的字段适合用于主键：

- 每行的值**唯一**
- 没有空值行，**非空**
- 日后不需要修改
- 某行被删除，主键值不再复用

## 创建主键

### 方式一

```sql
DROP TABLE IF EXISTS testTable;
CREATE TABLE testTable
(
  t_id int PRIMARY KEY,
  t_name VARCHAR(50)
);

```

用show查看

```sql
show create table testtable

+-----------+--------------------------------------+
| Table     | Create Table                         |
+-----------+--------------------------------------+
| testtable | CREATE TABLE `testtable` (           |
|           |   `t_id` int(11) NOT NULL,           |
|           |   `t_name` varchar(50) DEFAULT NULL, |
|           |   PRIMARY KEY (`t_id`)               |
|           | ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+-----------+--------------------------------------+
```


### 方式二

```sql
DROP TABLE IF EXISTS testTable;
CREATE TABLE testTable
(
  t_id int,
  t_name VARCHAR(50),
  PRIMARY KEY (t_id)
);
```

用show查看结果

```sql
show create table testtable

+-----------+--------------------------------------+
| Table     | Create Table                         |
+-----------+--------------------------------------+
| testtable | CREATE TABLE `testtable` (           |
|           |   `t_id` int(11) NOT NULL,           |
|           |   `t_name` varchar(50) DEFAULT NULL, |
|           |   PRIMARY KEY (`t_id`)               |
|           | ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+-----------+--------------------------------------+
```

结果一致，`t_id`字段自动设置为`NOT NULL`。

### 方式三

```sql
DROP TABLE IF EXISTS testTable;
CREATE TABLE testTable
(
t_id int,
t_name VARCHAR(50)
);

show create table testtable

+-----------+--------------------------------------+
| Table     | Create Table                         |
+-----------+--------------------------------------+
| testtable | CREATE TABLE `testtable` (           |
|           |   `t_id` int(11) DEFAULT NULL,       |
|           |   `t_name` varchar(50) DEFAULT NULL  |
|           | ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+-----------+--------------------------------------+
```

上面结果显示没有设置主键，现在把`t_id`添加为主键：

```sql
ALTER TABLE testtable
ADD CONSTRAINT PRIMARY KEY (t_id);

show create table testtable

+-----------+--------------------------------------+
| Table     | Create Table                         |
+-----------+--------------------------------------+
| testtable | CREATE TABLE `testtable` (           |
|           |   `t_id` int(11) NOT NULL,           |
|           |   `t_name` varchar(50) DEFAULT NULL, |
|           |   PRIMARY KEY (`t_id`)               |
|           | ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+-----------+--------------------------------------+
```

## 删除主键

```sql
ALTER TABLE testtable drop PRIMARY KEY;
```


# 外键

## 方式一

创建表的同时添加外键

```sql
DROP TABLE IF EXISTS testtable2;
CREATE TABLE testtable2
(
t2_id int PRIMARY KEY,
t2_name VARCHAR(50),
t_id int,
CONSTRAINT fk_t_id FOREIGN KEY (t_id) REFERENCES testtable (t_id)
);

show create table testtable2
+------------+-----------------------------------------------------------------------------+
| Table      | Create Table                                                                |
+------------+-----------------------------------------------------------------------------+
| testtable2 | CREATE TABLE `testtable2` (                                                 |
|            |   `t2_id` int(11) NOT NULL,                                                 |
|            |   `t2_name` varchar(50) DEFAULT NULL,                                       |
|            |   `t_id` int(11) DEFAULT NULL,                                              |
|            |   PRIMARY KEY (`t2_id`),                                                    |
|            |   KEY `fk_t_id` (`t_id`),                                                   |
|            |   CONSTRAINT `fk_t_id` FOREIGN KEY (`t_id`) REFERENCES `testtable` (`t_id`) |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8                                        |
+------------+-----------------------------------------------------------------------------+
```

也可以不用`constraint`关键字，系统会自动生成一个外键名称。

```sql
DROP TABLE IF EXISTS testtable2;
CREATE TABLE testtable2
(
t2_id int PRIMARY KEY,
t2_name VARCHAR(50),
t_id int,
FOREIGN KEY (t_id) REFERENCES testtable (t_id)
);

show create table testtable2;

+------------+---------------------------------------------------------------------------------------+
| Table      | Create Table                                                                          |
+------------+---------------------------------------------------------------------------------------+
| testtable2 | CREATE TABLE `testtable2` (                                                           |
|            |   `t2_id` int(11) NOT NULL,                                                           |
|            |   `t2_name` varchar(50) DEFAULT NULL,                                                 |
|            |   `t_id` int(11) DEFAULT NULL,                                                        |
|            |   PRIMARY KEY (`t2_id`),                                                              |
|            |   KEY `t_id` (`t_id`),                                                                |
|            |   CONSTRAINT `testtable2_ibfk_1` FOREIGN KEY (`t_id`) REFERENCES `testtable` (`t_id`) |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8                                                  |
+------------+---------------------------------------------------------------------------------------+
```

## 方式二

```sql
DROP TABLE IF EXISTS testtable2;
CREATE TABLE testtable2
(
t2_id int PRIMARY KEY,
t2_name VARCHAR(50),
t_id int
);

show create table testtable2;

+------------+---------------------------------------+
| Table      | Create Table                          |
+------------+---------------------------------------+
| testtable2 | CREATE TABLE `testtable2` (           |
|            |   `t2_id` int(11) NOT NULL,           |
|            |   `t2_name` varchar(50) DEFAULT NULL, |
|            |   `t_id` int(11) DEFAULT NULL,        |
|            |   PRIMARY KEY (`t2_id`)               |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8  |
+------------+---------------------------------------+
```

新建一个表，没有设置外键，下面通过`alter table`来添加外键

```sql
ALTER TABLE testtable2 
add CONSTRAINT fk_t_id FOREIGN KEY (t_id) REFERENCES testtable (t_id);

show create table testtable2;

+------------+-----------------------------------------------------------------------------+
| Table      | Create Table                                                                |
+------------+-----------------------------------------------------------------------------+
| testtable2 | CREATE TABLE `testtable2` (                                                 |
|            |   `t2_id` int(11) NOT NULL,                                                 |
|            |   `t2_name` varchar(50) DEFAULT NULL,                                       |
|            |   `t_id` int(11) DEFAULT NULL,                                              |
|            |   PRIMARY KEY (`t2_id`),                                                    |
|            |   KEY `fk_t_id` (`t_id`),                                                   |
|            |   CONSTRAINT `fk_t_id` FOREIGN KEY (`t_id`) REFERENCES `testtable` (`t_id`) |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8                                        |
+------------+-----------------------------------------------------------------------------+
```

## 删除外键

```sql
ALTER TABLE testtable2 drop FOREIGN KEY fk_t_id;
```

## 外键的级联关系

外键中的级联关系有以下几种情况：

- 默认，删除主表中的数据前需先删除从表中的数据，否则主表数据不会被删除
- ON DELETE CASCADE，删除主表中的数据时，从表中的数据随之删除
- ON UPDATE CASCADE，更新主表中的数据时，从表中的数据随之更新
- ON DELETE SET NULL，删除主表中的数据时，从表中的数据置为空

```sql
DROP TABLE IF EXISTS testtable2;
CREATE TABLE testtable2
(
t2_id int PRIMARY KEY,
t2_name VARCHAR(50),
t_id int,
CONSTRAINT fk_t_id FOREIGN KEY (t_id) REFERENCES testtable (t_id) ON DELETE CASCADE
);

show create table testtable2;

+------------+-----------------------------------------------------------------------------------------------+
| Table      | Create Table                                                                                  |
+------------+-----------------------------------------------------------------------------------------------+
| testtable2 | CREATE TABLE `testtable2` (                                                                   |
|            |   `t2_id` int(11) NOT NULL,                                                                   |
|            |   `t2_name` varchar(50) DEFAULT NULL,                                                         |
|            |   `t_id` int(11) DEFAULT NULL,                                                                |
|            |   PRIMARY KEY (`t2_id`),                                                                      |
|            |   KEY `fk_t_id` (`t_id`),                                                                     |
|            |   CONSTRAINT `fk_t_id` FOREIGN KEY (`t_id`) REFERENCES `testtable` (`t_id`) ON DELETE CASCADE |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8                                                          |
+------------+-----------------------------------------------------------------------------------------------+
```

# 唯一约束

唯一约束与主键的区别：

- 唯一约束列**可以包含NULL值**
- 唯一约束列可以修改
- 唯一约束列的值在某行被删除后可重复利用

## 方式一

```sql
DROP TABLE IF EXISTS testtable2;
CREATE TABLE testtable2
(
  t2_id int PRIMARY KEY,
  t2_name VARCHAR(50) unique,
  t_id int
);

show create table testtable2;

+------------+---------------------------------------+
| Table      | Create Table                          |
+------------+---------------------------------------+
| testtable2 | CREATE TABLE `testtable2` (           |
|            |   `t2_id` int(11) NOT NULL,           |
|            |   `t2_name` varchar(50) DEFAULT NULL, |
|            |   `t_id` int(11) DEFAULT NULL,        |
|            |   PRIMARY KEY (`t2_id`),              |
|            |   UNIQUE KEY `t2_name` (`t2_name`)    |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8  |
+------------+---------------------------------------+
```

只需要在后面添加`unique`关键字就可以了。


## 方式二

现在通过关键字`alter table`把`testtable2`的`t_id`字段也设置为唯一约束

```sql
ALTER TABLE testtable2 add CONSTRAINT UNIQUE (t_id);

show create table testtable2;

+------------+---------------------------------------+
| Table      | Create Table                          |
+------------+---------------------------------------+
| testtable2 | CREATE TABLE `testtable2` (           |
|            |   `t2_id` int(11) NOT NULL,           |
|            |   `t2_name` varchar(50) DEFAULT NULL, |
|            |   `t_id` int(11) DEFAULT NULL,        |
|            |   PRIMARY KEY (`t2_id`),              |
|            |   UNIQUE KEY `t2_name` (`t2_name`),   |
|            |   UNIQUE KEY `t_id` (`t_id`)          |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8  |
+------------+---------------------------------------+
```

## 方式三

```sql
DROP TABLE IF EXISTS testtable2;
CREATE TABLE testtable2
(
  t2_id int PRIMARY KEY,
  t2_name VARCHAR(50) unique,
  t_id int,
  CONSTRAINT uni_t_id unique (t_id)
);

show create table testtable2;

+------------+---------------------------------------+
| Table      | Create Table                          |
+------------+---------------------------------------+
| testtable2 | CREATE TABLE `testtable2` (           |
|            |   `t2_id` int(11) NOT NULL,           |
|            |   `t2_name` varchar(50) DEFAULT NULL, |
|            |   `t_id` int(11) DEFAULT NULL,        |
|            |   PRIMARY KEY (`t2_id`),              |
|            |   UNIQUE KEY `t2_name` (`t2_name`),   |
|            |   UNIQUE KEY `uni_t_id` (`t_id`)      |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8  |
+------------+---------------------------------------+
```

## 删除唯一约束

```sql
alter table testtable2 drop index uni_t_id;
```

删除唯一约束这里用的关键字是`index`，而名称使用的是`unique key`的名称而不是字段名称。



# 非空约束

重建表`testtable2`，并把t2_name设置为`not null`约束

```sql
DROP TABLE IF EXISTS testtable2;
CREATE TABLE testtable2
(
  t2_id int PRIMARY KEY,
  t2_name VARCHAR(50) not null,
  t_id int
);

show create table testtable2;

+------------+--------------------------------------+
| Table      | Create Table                         |
+------------+--------------------------------------+
| testtable2 | CREATE TABLE `testtable2` (          |
|            |   `t2_id` int(11) NOT NULL,          |
|            |   `t2_name` varchar(50) NOT NULL,    |
|            |   `t_id` int(11) DEFAULT NULL,       |
|            |   PRIMARY KEY (`t2_id`)              |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+------------+--------------------------------------+
```

把`t2_name`修改为可以为空值

```sql
ALTER TABLE testtable2 MODIFY t2_name varchar(50);
```

再把它改为非空约束

```sql
ALTER TABLE testtable2 MODIFY t2_name varchar(50) not NULL;
```

# 检查约束

百度了一下，关于MySQL的检查约束，MySQL的手册里面有这么一句话：

> The CHECK clause is parsed but **ignored** by all storage engines. 

也就是写上约束语句不会报错，但是不起作用，下面尝试验证一下。

```sql
DROP TABLE IF EXISTS testtable3;
CREATE TABLE testtable3
(
	id int PRIMARY KEY,
	name VARCHAR(50) not null,
	age int check( age >0),
	sex varchar(2) check(sex in ("F", "M"))
);

show create TABLE testtable3;

+------------+--------------------------------------+
| Table      | Create Table                         |
+------------+--------------------------------------+
| testtable3 | CREATE TABLE `testtable3` (          |
|            |   `id` int(11) NOT NULL,             |
|            |   `name` varchar(50) NOT NULL,       |
|            |   `age` int(11) DEFAULT NULL,        |
|            |   `sex` varchar(2) DEFAULT NULL,     |
|            |   PRIMARY KEY (`id`)                 |
|            | ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+------------+--------------------------------------+
```

尝试插入数据

```sql
insert into testtable3 (id, name, age, sex) values(1, "Jordan", -10, "F");

select * from testtable3;

+----+--------+-----+-----+
| id | name   | age | sex |
+----+--------+-----+-----+
| 1  | Jordan | -10 | F   |
+----+--------+-----+-----+
```

上面的`age`字段check约束没有起到作用，再看看`sex`字段的约束：

```sql
insert into testtable3 (id, name, age, sex) values(2, "Kobe", 24, "SG");

select * from testtable3;

+----+--------+-----+-----+
| id | name   | age | sex |
+----+--------+-----+-----+
| 1  | Jordan | -10 | F   |
| 2  | Kobe   | 24  | SG  |
+----+--------+-----+-----+
```

也没有起到约束的作用。

测试环境：

- 服务器版本： 10.1.32-MariaDB - mariadb.org binary distribution
- 表的引擎均是：InnoDB


# 约束的修改

所有约束都可以通过`ALTER TABLE <table name> MODIFY ...`来进行修改。










