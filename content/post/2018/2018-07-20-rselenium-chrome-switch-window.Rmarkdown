---
title: RSelenium Chrome切换窗口
author: Heaven Zone
date: '2018-07-20'
slug: rselenium-chrome-switch-window
categories:
  - R
tags:
  - R
  - RSelenium
---

因为一直用的是firefox的浏览器，所以玩RSelenium的时候也是优先选择firefox，但是发现测试切换窗口的时候，总是切换不了，`getFirefoxProfile`也出现错误：

```
profile = RSelenium::getFirefoxProfile("C:/Users/krup/AppData/Roaming/Mozilla/Firefox/Profiles/uew2kgfm.default", TRUE)

Error in file(tmpfile, "rb") : cannot open the connection
In addition: There were 14 warnings (use warnings() to see them)
```

貌似网上都是用chrome，于是尝试一下，切换窗口没有问题。

```r
system("java -jar C:/krupSoftware/bin/selenium-server-standalone-3.13.0.jar",wait = FALSE, invisible = FALSE)

require(RSelenium)
#profile = RSelenium::getChromeProfile("C:/Users/krup/AppData/Local/Google/Chrome/User Data", "default")
#remDr <- RSelenium::remoteDriver(browserName = "chrome", extraCapabilities = profile)

remDr <- RSelenium::remoteDriver(browserName = "chrome")

remDr$open()
remDr$navigate("http://www.baidu.com")
wins = remDr$getWindowHandles()

remDr$executeScript("window.open('', '_blank', 'location=yes,height=800,width=800');", list("dummy"))
wins = remDr$getWindowHandles()

remDr$switchToWindow(wins[[length(wins)]])
remDr$getCurrentWindowHandle()

remDr$executeScript("window.open('http://www.sogou.com', '_blank', 'toolbar=yes, location=yes, status=yes, menubar=yes, scrollbars=yes');", list("dummy"))

remDr$navigate("http://www.bing.com")
remDr$getCurrentWindowHandle()
wins = remDr$getWindowHandles()
wins
```

