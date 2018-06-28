---
title: firefox无法连接到i-guangdong
author: Heaven Zone
date: '2018-06-28'
slug: firefox-cannot-connect-toi-guangdong
categories:
  - firefox
tags:
  - firefox
---

不知道为什么有时在firefox下无法打开连接i-guangdong wifi之后的登录网页，虽然chromium也是提示不安全，但是可以点击advance继续打开，firefox貌似不行。

针对错误信息：

> 的管理员没有正确配置好此网站。为避免您的信息失窃,Firefox 并未与此网站建立连接

进行搜索了一下，解决办法是：

在firefox地址栏输入`about:config`，在搜索栏输入`security.enterprise_roots.enabled`如果**存在**，就直接双击把它修改成`true`，如果不存在就右击，新建-布尔，名称输入`security.enterprise_roots.enabled`，值输入true。