---
title: 关于debian stretch出现hash sum mismatch错误
author: Heaven Zone
date: '2018-01-03'
slug: debian-stretch-apt-packages-hash-sum-mismatch
categories:
  - Linux
tags:
  - Linux
  - debian
description: '关于debian stretch出现hash sum mismatch错误'
---


## 问题描述

在debian docker里面安装default-jdk的时候出现错误，大概就是说下载下来的文件的md5和预期的不一致，导致安装不成功。

错误关键信息：

```
hash sum mismatch
```

## 解决办法

首先尝试了下面的代码：

```
apt-get clean
rm -rf /var/lib/apt/lists/*
apt-get clean
apt-get update
apt-get upgrade
```

无果，再尝试：

创建文件：`/etc/apt/apt.conf.d/99fixbadproxy`，添加如下内容：

```
Acquire::http::Pipeline-Depth 0;
Acquire::http::No-Cache true;
Acquire::BrokenProxy    true;
```

成功！

## 未试方法

未试这个办法，不知道apt-key update能否解决。

```
sudo apt-key update -y
sudo apt-get update -y
sudo apt-get upgrade -y
```


## 参考资料

- <https://stackoverflow.com/questions/15505775/debian-apt-packages-hash-sum-mismatch>