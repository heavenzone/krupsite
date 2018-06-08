---
title: MySQL和MariaDB创建和操作表
author: Heaven Zone
date: '2018-06-09'
slug: mysql-table-instructions
categories:
  - MySQL
tags:
  - MySQL
  - MariaDB
  - sql
---

MySQL和MariaDB用命令行创建表和操作表。

### 创建表

- 创建表customers
- 三个字段：`cust_id`、`cust_name`、`cust_city`
- `cust_id`类型为整型，不能为空，自增值
- `cust_name`为varchar，不能为空
- `cust_country`，设置默认值为"China"
- `cust_city`为varchar，不设置是否为空，系统默认为空，
- 设置cust_id为主键
- 指定引擎为`InnoDB`

```
create table customers (
  cust_id int not null AUTO_INCREMENT,
  cust_name varchar(50) not null,
  cust_country varchar(50) default "China",
  cust_city varchar(50),
  PRIMARY KEY (cust_id)
) ENGINE=InnoDB;

Query OK, 0 rows affected
Time: 0.619s
```

```
show create table customers;

+-----------+--------------------------------------------------------------------+
| Table     | Create Table                                                       |
+-----------+--------------------------------------------------------------------+
| customers | CREATE TABLE `customers` (                                         |
|           |   `cust_id` int(11) NOT NULL AUTO_INCREMENT,                       |
|           |   `cust_name` varchar(50) NOT NULL,                                |
|           |   `cust_country` varchar(50) DEFAULT 'China',                      |
|           |   `cust_city` varchar(50) DEFAULT NULL,                            |
|           |   PRIMARY KEY (`cust_id`)                                          |
|           | ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci |
+-----------+--------------------------------------------------------------------+
```


### 更新(修改)表

#### 添加列

添加字段`cust_username`，并设置为唯一值。

```
ALTER TABLE customers
ADD cust_username VARCHAR(20) UNIQUE;

You're about to run a destructive command.
Do you want to proceed? (y/n): y
Your call!
Query OK, 0 rows affected
Time: 1.035s
```

#### 删除列

删除表`customers`中字段`cust_city`。

```
ALTER TABLE customers DROP COLUMN cust_city;

You're about to run a destructive command.
Do you want to proceed? (y/n): y
Your call!
Query OK, 0 rows affected
Time: 1.170s
```

### 删除表

```
DROP TABLE customers
```

### 重命名表

```
RENAME TABLE customers TO custs
             ordertable TO orders
```

### 关于引擎

MariaDB的引擎：

- InnoDB，默认引擎，事务安全的引擎，不支持全文本搜索。
- MEMORY与MyISAM功能一样，数据存储在内存，适合临时表。
- MyISAM，高性能引擎，支持全文本搜索，不支持事务处理。
- Aria，新的事务安全引擎，支持全文本搜索和重要的崩溃恢复特性。

在同一个数据库中，不同表可以使用不同的引擎，但是外键不能跨引擎。







