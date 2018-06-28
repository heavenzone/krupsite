---
title: mysql笔记-乱
author: Heaven Zone
date: '2018-06-05'
slug: mysql-notes-messy
categories:
  - mysql
tags:
  - mysql
simpletoc: true
---

# 创建和使用数据库

## mysql数据类型



```
MariaDB [(none)]> show character set
    -> ;
+----------+-----------------------------+---------------------+--------+
| Charset  | Description                 | Default collation   | Maxlen |
+----------+-----------------------------+---------------------+--------+
| big5     | Big5 Traditional Chinese    | big5_chinese_ci     |      2 |
| dec8     | DEC West European           | dec8_swedish_ci     |      1 |
| cp850    | DOS West European           | cp850_general_ci    |      1 |
| hp8      | HP West European            | hp8_english_ci      |      1 |
| koi8r    | KOI8-R Relcom Russian       | koi8r_general_ci    |      1 |
| latin1   | cp1252 West European        | latin1_swedish_ci   |      1 |
| latin2   | ISO 8859-2 Central European | latin2_general_ci   |      1 |
| swe7     | 7bit Swedish                | swe7_swedish_ci     |      1 |
| ascii    | US ASCII                    | ascii_general_ci    |      1 |
| ujis     | EUC-JP Japanese             | ujis_japanese_ci    |      3 |
| sjis     | Shift-JIS Japanese          | sjis_japanese_ci    |      2 |
| hebrew   | ISO 8859-8 Hebrew           | hebrew_general_ci   |      1 |
| tis620   | TIS620 Thai                 | tis620_thai_ci      |      1 |
| euckr    | EUC-KR Korean               | euckr_korean_ci     |      2 |
| koi8u    | KOI8-U Ukrainian            | koi8u_general_ci    |      1 |
| gb2312   | GB2312 Simplified Chinese   | gb2312_chinese_ci   |      2 |
| greek    | ISO 8859-7 Greek            | greek_general_ci    |      1 |
| cp1250   | Windows Central European    | cp1250_general_ci   |      1 |
| gbk      | GBK Simplified Chinese      | gbk_chinese_ci      |      2 |
| latin5   | ISO 8859-9 Turkish          | latin5_turkish_ci   |      1 |
| armscii8 | ARMSCII-8 Armenian          | armscii8_general_ci |      1 |
| utf8     | UTF-8 Unicode               | utf8_general_ci     |      3 |
| ucs2     | UCS-2 Unicode               | ucs2_general_ci     |      2 |
| cp866    | DOS Russian                 | cp866_general_ci    |      1 |
| keybcs2  | DOS Kamenicky Czech-Slovak  | keybcs2_general_ci  |      1 |
| macce    | Mac Central European        | macce_general_ci    |      1 |
| macroman | Mac West European           | macroman_general_ci |      1 |
| cp852    | DOS Central European        | cp852_general_ci    |      1 |
| latin7   | ISO 8859-13 Baltic          | latin7_general_ci   |      1 |
| utf8mb4  | UTF-8 Unicode               | utf8mb4_general_ci  |      4 |
| cp1251   | Windows Cyrillic            | cp1251_general_ci   |      1 |
| utf16    | UTF-16 Unicode              | utf16_general_ci    |      4 |
| utf16le  | UTF-16LE Unicode            | utf16le_general_ci  |      4 |
| cp1256   | Windows Arabic              | cp1256_general_ci   |      1 |
| cp1257   | Windows Baltic              | cp1257_general_ci   |      1 |
| utf32    | UTF-32 Unicode              | utf32_general_ci    |      4 |
| binary   | Binary pseudo charset       | binary              |      1 |
| geostd8  | GEOSTD8 Georgian            | geostd8_general_ci  |      1 |
| cp932    | SJIS for Windows Japanese   | cp932_japanese_ci   |      2 |
| eucjpms  | UJIS for Windows Japanese   | eucjpms_japanese_ci |      3 |
+----------+-----------------------------+---------------------+--------+
40 rows in set (0.00 sec)

MariaDB [(none)]>
```

## 创建数据库

创建默认字符集为utf8的数据库foreign_sales

```
create database foreign_sales character set utf8;

```

使用数据库

```
mariadb root@localhost:(none)> use foreign_sales
You are now connected to database "foreign_sales" as user "root"
Time: 0.016s
```


## 创建表

创建`person`表，并把`person_id`设为主键

```
create table person (
-> person_id smallint unsigned,
->     fname varchar(20),
->     lname varchar(20),
->     gender ENUM('M', 'F'),
->     birth_date date,
->     street varchar(20),
->     city varchar(20),
->     state varchar(20),
->     country varchar(20),
->     postal_code varchar(20),
->     constraint pk_person primary key (person_id)
-> );
Query OK, 0 rows affected
Time: 0.031s
```

查看刚创建的表

```
mysql>desc person;


+-------------+----------------------+------+-----+---------+-------+
| Field       | Type                 | Null | Key | Default | Extra |
+-------------+----------------------+------+-----+---------+-------+
| person_id   | smallint(5) unsigned | NO   | PRI | <null>  |       |
| fname       | varchar(20)          | YES  |     | <null>  |       |
| lname       | varchar(20)          | YES  |     | <null>  |       |
| gender      | enum('M','F')        | YES  |     | <null>  |       |
| birth_date  | date                 | YES  |     | <null>  |       |
| street      | varchar(20)          | YES  |     | <null>  |       |
| city        | varchar(20)          | YES  |     | <null>  |       |
| state       | varchar(20)          | YES  |     | <null>  |       |
| country     | varchar(20)          | YES  |     | <null>  |       |
| postal_code | varchar(20)          | YES  |     | <null>  |       |
+-------------+----------------------+------+-----+---------+-------+
10 rows in set
Time: 0.000s
```

创建`favorite_food`表，将`person_id, food`设为主键，`person_id`设为外键，并关联到`person`表的`person_id`

```
mysql> create table favorite_food
   ->     (person_id smallint unsigned,
   ->     food varchar(20),
   ->     constraint pk_favorite_food primary key (person_id, food),
   ->     constraint fk_fav_food_person foreign key (person_id)
   ->     references person (person_id)
   ->     );
   
Query OK, 0 rows affected
Time: 0.031s
```

## 为主键增加自增属性

```
# 貌似需先设置foreign_key_checks
mysql> set foreign_key_checks = 0;
Query OK, 0 rows affected
Time: 0.000s

mysql> alter table person modify person_id smallint unsigned auto_increment;
You're about to run a destructive command.
Do you want to proceed? (y/n): y
Your call!
Query OK, 0 rows affected
Time: 0.050s

mysql> desc person
   -> ;
+-------------+----------------------+------+-----+---------+----------------+
| Field       | Type                 | Null | Key | Default | Extra          |
+-------------+----------------------+------+-----+---------+----------------+
| person_id   | smallint(5) unsigned | NO   | PRI | <null>  | auto_increment |
| .
| .
| .
+-------------+----------------------+------+-----+---------+----------------+
```


## 插入语句

```
mysql> insert into person
   ->  (person_id, fname, lname, gender, birth_date)
   ->  values(null, "William", "Turner", "M", "1982-05-27");
   
Query OK, 1 row affected
Time: 0.016s


mysql> insert into favorite_food (person_id, food)
   ->  values(1, "pizza");
   
Query OK, 1 row affected
Time: 0.000s
```

## 更新数据

```
mysql> update person
   -> set street = "122 Tremont St.",
   -> city = "Boston",
   -> state = "ma",
   -> country = "USA",
   -> postal_code = "021381"
   -> where person_id = 1;
   
Query OK, 1 row affected
Time: 0.016s
```


## 删除数据

```
mysql> delete from person where person_id = 2;
```


# 查询

## 测试数据


```
mysql> select * from player ;
+-----------+-----------+----------+--------+---------+
| player_id | firstname | lastname | number | team_id |
+-----------+-----------+----------+--------+---------+
| 1         | Michael   | Jordan   | 23     | 1       |
| 2         | Kobe      | Bryant   | 8      | 2       |
| 3         | Allen     | Iverson  | 3      | 5       |
| 4         | Tracy     | McGrady  | 1      | 4       |
+-----------+-----------+----------+--------+---------+
```

```
mysql> select * from team;
+---------+----------+--------------+----------+
| team_id | teamname | city         | coach_id |
+---------+----------+--------------+----------+
| 1       | Bulls    | Chicago      | 1        |
| 2       | Lakers   | LA           | 2        |
| 3       | Celtics  | Boston       | 3        |
| 4       | Rockets  | Houston      | 4        |
| 5       | Sixers   | Philadelphia | 5        |
+---------+----------+--------------+----------+
```

```
mysql> select * from coach;
+----------+-----------+
| coach_id | coachname |
+----------+-----------+
| 1        | Phil      |
| 2        | Jackson   |
| 3        | Mike      |
+----------+-----------+
```

## select 

```
mysql> select firstname, lastname,  teamname from player, team;

+-----------+----------+----------+
| firstname | lastname | teamname |
+-----------+----------+----------+
| Michael   | Jordan   | Bulls    |
| Kobe      | Bryant   | Bulls    |
| Allen     | Iverson  | Bulls    |
| Tracy     | McGrady  | Bulls    |
| Michael   | Jordan   | Lakers   |
| Kobe      | Bryant   | Lakers   |
| Allen     | Iverson  | Lakers   |
| Tracy     | McGrady  | Lakers   |
| Michael   | Jordan   | Celtics  |
| Kobe      | Bryant   | Celtics  |
| Allen     | Iverson  | Celtics  |
| Tracy     | McGrady  | Celtics  |
| Michael   | Jordan   | Rockets  |
| Kobe      | Bryant   | Rockets  |
| Allen     | Iverson  | Rockets  |
| Tracy     | McGrady  | Rockets  |
| Michael   | Jordan   | Sixers   |
| Kobe      | Bryant   | Sixers   |
| Allen     | Iverson  | Sixers   |
| Tracy     | McGrady  | Sixers   |
+-----------+----------+----------+
```

注意上面输出结果是两个表的积。

## where

```
mysql> select firstname, lastname,  teamname from player, team
   -> where player.team_id = team.team_id;
+-----------+----------+----------+
| firstname | lastname | teamname |
+-----------+----------+----------+
| Michael   | Jordan   | Bulls    |
| Kobe      | Bryant   | Lakers   |
| Tracy     | McGrady  | Rockets  |
| Allen     | Iverson  | Sixers   |
+-----------+----------+----------+
```

添加`where`输出就是我们想要的结果。



```
mysql> select firstname, lastname, number, teamname, city, coachname
   -> from player, team, coach
   -> where player.team_id = team.team_id and team.coach_id = coach.coach_id
   -> ;
+-----------+----------+--------+----------+---------+-----------+
| firstname | lastname | number | teamname | city    | coachname |
+-----------+----------+--------+----------+---------+-----------+
| Michael   | Jordan   | 23     | Bulls    | Chicago | Phil      |
| Kobe      | Bryant   | 8      | Lakers   | LA      | Jackson   |
+-----------+----------+--------+----------+---------+-----------+
```

上面结果并没有把全部球员的数据输出来，这是因为这里的`where`求出来的是几个表中的`交集`，而`coach`表里面只有Phil和Jackson两个教练，而Mike并没有在另外两个表中出现，所以只输出了两行。


那么如何输出所有球员的相关数据呢？





## 列的别名

```
mysql> select database() data;
+------+
| data |
+------+
| nba  |
+------+
```

# 表的概念

- 永久表：用create table语句创建的表
- 临时表：子查询返回的表
- 虚拟表：create view子句所创建的视图


## 子查询产生的表

```
mysql> select * 
from (select firstname, lastname,  teamname from player, team) as newtable 
where teamname = "Bulls";

+-----------+----------+----------+
| firstname | lastname | teamname |
+-----------+----------+----------+
| Michael   | Jordan   | Bulls    |
| Kobe      | Bryant   | Bulls    |
| Allen     | Iverson  | Bulls    |
| Tracy     | McGrady  | Bulls    |
+-----------+----------+----------+
```

## 视图

```
# 创建视图
mysql>create view player_team as
   -> select firstname, lastname, teamname from team, player where team.team_id = player_id;
   
Query OK, 0 rows affected
Time: 0.016s

# 查询视图
mysql>select * from player_team;

+-----------+----------+----------+
| firstname | lastname | teamname |
+-----------+----------+----------+
| Michael   | Jordan   | Bulls    |
| Kobe      | Bryant   | Lakers   |
| Allen     | Iverson  | Celtics  |
| Tracy     | McGrady  | Rockets  |
+-----------+----------+----------+
```


## 表连接

```
mysql>select * from team inner join coach on team.coach_id = coach.coach_id;
+---------+----------+---------+----------+----------+-----------+
| team_id | teamname | city    | coach_id | coach_id | coachname |
+---------+----------+---------+----------+----------+-----------+
| 1       | Bulls    | Chicago | 1        | 1        | Phil      |
| 2       | Lakers   | LA      | 2        | 2        | Jackson   |
| 3       | Celtics  | Boston  | 3        | 3        | Mike      |
+---------+----------+---------+----------+----------+-----------+
3 rows in set
Time: 0.000s
mysql>select * from team left join coach on team.coach_id = coach.coach_id;
+---------+----------+--------------+----------+----------+-----------+
| team_id | teamname | city         | coach_id | coach_id | coachname |
+---------+----------+--------------+----------+----------+-----------+
| 1       | Bulls    | Chicago      | 1        | 1        | Phil      |
| 2       | Lakers   | LA           | 2        | 2        | Jackson   |
| 3       | Celtics  | Boston       | 3        | 3        | Mike      |
| 4       | Rockets  | Houston      | 4        | <null>   | <null>    |
| 5       | Sixers   | Philadelphia | 5        | <null>   | <null>    |
+---------+----------+--------------+----------+----------+-----------+
5 rows in set
Time: 0.016s
mysql>select * from team right join coach on team.coach_id = coach.coach_id;
+---------+----------+---------+----------+----------+-----------+
| team_id | teamname | city    | coach_id | coach_id | coachname |
+---------+----------+---------+----------+----------+-----------+
| 1       | Bulls    | Chicago | 1        | 1        | Phil      |
| 2       | Lakers   | LA      | 2        | 2        | Jackson   |
| 3       | Celtics  | Boston  | 3        | 3        | Mike      |
+---------+----------+---------+----------+----------+-----------+
3 rows in set
Time: 0.000s
```

对比上面三个输出，可以看到`inner join`的输出跟前面的where输出结果一样，求的是**交集**，而`left join`是保证左边的表格全部输出，`right join`保证右边的表格完整输出。


输出所有球员的名字、号码、队名、教练：

```
mysql> select firstname, lastname, number, teamname, coachname 
from player 
left join team on player.team_id = team.team_id 
left join coach on team.coach_id = coach.coach_id ;
+-----------+----------+--------+----------+-----------+
| firstname | lastname | number | teamname | coachname |
+-----------+----------+--------+----------+-----------+
| Michael   | Jordan   | 23     | Bulls    | Phil      |
| Kobe      | Bryant   | 8      | Lakers   | Jackson   |
| Allen     | Iverson  | 3      | Sixers   | <null>    |
| Tracy     | McGrady  | 1      | Rockets  | <null>    |
| Shaquille | O'Neal   | 34     | Lakers   | Jackson   |
| Scottie   | Pippen   | 33     | Bulls    | Phil      |
+-----------+----------+--------+----------+-----------+
6 rows in set
Time: 0.020s
```



# 分组与聚集


在此处添加了O'neal和Pippen两个player。


## group by和having

查询出球员人数大于等于2的队伍。

```
mysql> select team_id, count(player_id) 
from player 
group by team_id 
having count(player_id) >= 2;

+---------+------------------+
| team_id | count(player_id) |
+---------+------------------+
| 1       | 2                |
| 2       | 2                |
+---------+------------------+
```

查询出每个队伍号码最大的球员：

```
mysql> select firstname, lastname, number, team_id from player,
   -> (select max(number) as maxnum from player group by team_id) as t2
   -> where player.number = t2.maxnum order by number;
+-----------+----------+--------+---------+
| firstname | lastname | number | team_id |
+-----------+----------+--------+---------+
| Tracy     | McGrady  | 1      | 4       |
| Allen     | Iverson  | 3      | 5       |
| Scottie   | Pippen   | 33     | 1       |
| Shaquille | O'Neal   | 34     | 2       |
+-----------+----------+--------+---------+
```

原来MySQL中的group by并没有R语言的`dplyr`包的`group_by`函数那么方便，这里的思路是：先通过`group by`把每个队伍最大的号码找出来，作为子表，然后通过where把原来的表的号码与子表找出来的号码相等，于是就找出每个队伍号码最大的球员信息。


聚集函数：

- max
- min
- avg
- sum
- count


