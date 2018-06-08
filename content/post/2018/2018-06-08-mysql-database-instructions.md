---
title: MySQL数据库和表的操作
author: Heaven Zone
date: '2018-06-08'
slug: mysql-database-instructions
categories:
  - MySQL
tags:
  - MySQL
  - sql
  - MariaDB
---

MySQL和MariaDB的数据库和表的常规操作。

### 用mycli连接数据库

如果使用windows的`Power Shell`或者`cmd`来运行mycli，而且数据库的编码是utf8的话，在终端输入如下命令：

```
chcp 65001
```

安装mycli，详见[官网](http://www.mycli.net/)。

```
pip install mycli
```

连接数据库：

```
mycli -h db4free.net -u <username> -p<password>
```

### 显示所有数据库

```
mysql> show databases

+--------------------+
| Database           |
+--------------------+
| information_schema |
| krupdb             |
+--------------------+
```

### 使用数据库

```
mysql> use krupdb

You are now connected to database "krupdb" as user "zhg"
```

### 显示所有表

```
mysql> show tables

+------------------+
| Tables_in_krupdb |
+------------------+
| Customers        |
| OrderItems       |
| Orders           |
| Products         |
| Vendors          |
| log              |
| reg              |
+------------------+
```

### 显示表结构

```
mysql> show columns from log

+---------+-------------+------+-----+---------+----------------+
| Field   | Type        | Null | Key | Default | Extra          |
+---------+-------------+------+-----+---------+----------------+
| id      | int(11)     | NO   | PRI | <null>  | auto_increment |
| user    | varchar(50) | NO   |     | <null>  |                |
| logdate | datetime    | NO   |     | <null>  |                |
+---------+-------------+------+-----+---------+----------------+
```

或者用`describe`：

```
mysql> describe log

+---------+-------------+------+-----+---------+----------------+
| Field   | Type        | Null | Key | Default | Extra          |
+---------+-------------+------+-----+---------+----------------+
| id      | int(11)     | NO   | PRI | <null>  | auto_increment |
| user    | varchar(50) | NO   |     | <null>  |                |
| logdate | datetime    | NO   |     | <null>  |                |
+---------+-------------+------+-----+---------+----------------+
```

### 查看创建数据库所使用的sql语句

```
mysql> show create database krupdb

+----------+-----------------------------------------------------------------------------------------------+
| Database | Create Database                                                                               |
+----------+-----------------------------------------------------------------------------------------------+
| krupdb   | CREATE DATABASE `krupdb` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ |
+----------+-----------------------------------------------------------------------------------------------+
```

### 查看创建表的sql语句

```
mysql> show create table log

+-------+--------------------------------------------------------+
| Table | Create Table                                           |
+-------+--------------------------------------------------------+
| log   | CREATE TABLE `log` (                                   |
|       |   `id` int(11) NOT NULL AUTO_INCREMENT,                |
|       |   `user` varchar(50) NOT NULL,                         |
|       |   `logdate` datetime NOT NULL,                         |
|       |   PRIMARY KEY (`id`)                                   |
|       | ) ENGINE=InnoDB AUTO_INCREMENT=20 DEFAULT CHARSET=utf8 |
+-------+--------------------------------------------------------+
```

### 获取`show`命令的帮助文档

```
mysql> help show
```


### 显示数据库服务器状态

```
mysql> show status

# 显示服务器错误或者警告信息
mysql> show errors
mysql> show warnings
```




