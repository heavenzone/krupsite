---
title: RMySQL使用方法以及中文乱码问题
author: Heaven Zone
date: '2018-01-25'
slug: rmysql-usage-and-chinese-issue
categories:
  - R
tags:
  - R
  - RMySQL
  - 中文问题
description: 'RMySQL使用方法以及中文乱码问题'
simpletoc: true
---

## 连接MySql

```{r}
#install.packages("RMySQL")
library(RMySQL)
drv <- dbDriver("MySQL") 
con <- RMySQL::dbConnect(drv, dbname = "blog", host="127.0.0.1", port=3306, username="root", password="password")
dbSendQuery(con,'SET NAMES utf8')

res <- RMySQL::dbSendQuery(con,"show variables like 'character_set_%'")
dbFetch(res)

# 删除所有连接
# lapply(dbListConnections(dbDriver("MySQL")), dbDisconnect)
# 查看所有连接
# dbListConnections(dbDriver("MySQL"))
```




## 把data.frame数据框数据写入MySql数据库

### 方法一

测试环境：

- mysql: 数据库默认编码为：utf8_general_ci
- 操作系统：win10
- R:
  - > Sys.getlocale()
[1] "LC_COLLATE=Chinese (Simplified)_China.936;LC_CTYPE=Chinese (Simplified)_China.936;LC_MONETARY=Chinese (Simplified)_China.936;LC_NUMERIC=C;LC_TIME=Chinese (Simplified)_China.936"

这里自定义了一个函数`hg_dbWriteTable`用以处理在windows操作系统下写入mysql数据库时的中文问题。

该函数主要是处理编码的问题，用到了`iconv`函数，对数据框转换编码的时候比较怪的地方是，需要转换两次，写入mysql数据库是才能显示，如果只转换一次，写入mysql后为空，但是数据框的列标题则只需转换一次就可以了。

```{r}
library(RMySQL)
drv <- dbDriver("MySQL") 
options(encoding="utf8")
con <- RMySQL::dbConnect(drv, dbname = "blog", host="127.0.0.1", port=3306, username="root", password="password")
dbSendQuery(con,'SET NAMES utf8')

v1 <- c("测试111", "测试222", "测试2", "测试") 
v2 <- c(1, 2, 3, 4)
v3 <- c("测试", "测试", "测试2", "测试") 
v4 <- c(4, 2, 3, 4)
df <- data.frame(测试 = v1, v2, 测试2 = as.factor(v3), v4, stringsAsFactors = FALSE)

hg_dbWriteTable <- function(conn, name, df, encode_from = "gbk", encode_to = "UTF-8", ...) {
  names(df) <- iconv(names(df), encode_from, encode_to)
  x_char_columns <- sapply(df, class)
  x_cols <- names(x_char_columns[x_char_columns != "numeric"])
  # 这里需要用iconv转换两次，奇怪-_-!
  df <- mutate_at(df, x_cols,funs(iconv(., encode_from, encode_to))) 
  df <- mutate_at(df, x_cols,funs(iconv(., encode_from, encode_to)))
  dbWriteTable(conn = conn, name = name, value = df, ...)
}

hg_dbWriteTable(con,"test", df, overwrite = T, row.names = FALSE)
dbDisconnect(con)
```


### 方法二

把数据框保存为utf-8编码的csv文件，然后用dbWriteTable直接读取文件来写入mysql数据库，从而解决中文问题。

```{r}
library(RMySQL)
drv <- dbDriver("MySQL") 
con <- RMySQL::dbConnect(drv, dbname = "blog", host="127.0.0.1", port=3306, username="root", password="123qweasdzxc")
dbSendQuery(con,'SET NAMES utf8')

v1 <- c("测试", "测试", "测试2", "测试") 
v2 <- c(1, 2, 3, 4)
df <- data.frame(测试 = v1, 测试2 = v2, stringsAsFactors = FALSE)

write.csv(df, file = "tmp.csv", fileEncoding = "utf8", quote = FALSE, row.names = FALSE)

dbWriteTable(con, value = "tmp.csv", name = "students", overwrite = TRUE, row.names = FALSE, sep = ",", quote='\"', eol="\r\n")
dbDisconnect(con)
```

## 从MySql数据库获取数据

```{r}
library(RMySQL)
drv <- dbDriver("MySQL") 
con <- RMySQL::dbConnect(drv, dbname = "blog", host="127.0.0.1", port=3306, username="root", password="password")
# 这里设置成gbk，读取到的数据就不会乱码
dbSendQuery(con,'SET NAMES gbk')

# 方法一：dbReadTable
df.mysql.1 <- dbReadTable(con, "test")
df.mysql.1
df.mysql.1$测试 %>% Encoding


# 方法二：dbGetQuery
dbGetQuery(con, "select * from test")


# 方法三：dbSendQuery
# 当数据表很大时可以用这种方法切片获取数据
result <- dbSendQuery(con, "select * from test")
result
# 切片提取
while(!dbHasCompleted(result)) {
  fetch(result, n = 2) %>% print
}
dbClearResult(result)
dbDisconnect(con)
# lapply(dbListConnections(dbDriver("MySQL")), dbDisconnect)
# dbListConnections(dbDriver("MySQL"))
```


## 插入数据

```{r}
drv <- dbDriver("MySQL") 
con <- RMySQL::dbConnect(drv, dbname = "blog", host="127.0.0.1", port=3306, username="root", password="password")
# 这里设置成gbk，插入数据就不会乱码（数据库编码为utf8）
dbSendQuery(con,'SET NAMES gbk')

strsql <- "INSERT INTO `test`(`测试`) VALUES ('测试中文')"
dbSendQuery(con, strsql)
```


