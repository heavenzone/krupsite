---
title: 用R语言mailR包发邮件
author: Heaven Zone
date: '2018-07-24'
slug: sendmail-with-mailR
categories:
  - R
tags:
  - R
  - mailR
---

用**RSelenium**包写了个自动登录图书馆网站的脚本，获取借书记录信息，提醒自己哪些书准备还，没想到写这个脚本的过程中，发现有一本书过期19天了，原来这本书藏在第二页，自己平时看还有那些书没还的时候，并没有留意是否还有第二页，看来写这个脚本还是有点用处。

现在想把处理好的信息自动发送到手机上，发邮件是最好的选择了，于是找到了一个R的扩展包**mailR**，这个包依赖**rJava**，所以要安装jdk或者jre，下面记录一下代码：

```
hg_lib_sendmail = function(file, bodystr) {
  sender <- "发送邮件@163.com"
  recipients <- c("接受邮件@qq.com")
  bd = paste0(stringi::stri_c(stringi::stri_c(bodystr), "\n"), collapse = "")
  hg_try({
    mailR::send.mail(
      from = sender,
      to = recipients,
      subject = paste(Sys.Date(), "借书记录"),
      #邮件主题
      body = paste0("亲.这是您的图书馆借书记录，请查看！\n\n", bd ,"\n\n",Sys.time()),
      smtp = list(
        host.name = "smtp.163.com",
        port = 465,
        user.name = "163邮箱@163.com",
        passwd = "<163的授权码>",
        ssl = TRUE
      ),
      authenticate = TRUE,
      send = TRUE,
      encoding = "utf-8",
      attach.files = c(file)#添加附件
    )
  })
  print(bodystr)
}
```

测试163邮箱没问题，可以使用认证，为了与电子邮箱的登录密码区分开来，设置了一个授权码。