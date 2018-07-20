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
```

```
## [1] "Connecting to remote server"
## $acceptInsecureCerts
## [1] FALSE
## 
## $acceptSslCerts
## [1] FALSE
## 
## $applicationCacheEnabled
## [1] FALSE
## 
## $browserConnectionEnabled
## [1] FALSE
## 
## $browserName
## [1] "chrome"
## 
## $chrome
## $chrome$chromedriverVersion
## [1] "2.40.565498 (ea082db3280dd6843ebfb08a625e3eb905c4f5ab)"
## 
## $chrome$userDataDir
## [1] "C:\\Users\\krup\\AppData\\Local\\Temp\\scoped_dir7692_5551"
## 
## 
## $cssSelectorsEnabled
## [1] TRUE
## 
## $databaseEnabled
## [1] FALSE
## 
## $handlesAlerts
## [1] TRUE
## 
## $hasTouchScreen
## [1] FALSE
## 
## $javascriptEnabled
## [1] TRUE
## 
## $locationContextEnabled
## [1] TRUE
## 
## $mobileEmulationEnabled
## [1] FALSE
## 
## $nativeEvents
## [1] TRUE
## 
## $networkConnectionEnabled
## [1] FALSE
## 
## $pageLoadStrategy
## [1] "normal"
## 
## $platform
## [1] "Windows NT"
## 
## $rotatable
## [1] FALSE
## 
## $setWindowRect
## [1] TRUE
## 
## $takesHeapSnapshot
## [1] TRUE
## 
## $takesScreenshot
## [1] TRUE
## 
## $unexpectedAlertBehaviour
## [1] ""
## 
## $version
## [1] "67.0.3396.62"
## 
## $webStorageEnabled
## [1] TRUE
## 
## $webdriver.remote.sessionid
## [1] "1472dea1e5bfe170900ef618a61f1534"
## 
## $id
## [1] "1472dea1e5bfe170900ef618a61f1534"
```

```r
remDr$navigate("http://www.baidu.com")
wins = remDr$getWindowHandles()

remDr$executeScript("window.open('', '_blank', 'location=yes,height=800,width=800');", list("dummy"))
```

```
## list()
```

```r
wins = remDr$getWindowHandles()

remDr$switchToWindow(wins[[length(wins)]])
remDr$getCurrentWindowHandle()
```

```
## [[1]]
## [1] "CDwindow-0A5079621F5B3C138577A8EF45784B41"
```

```r
remDr$executeScript("window.open('http://www.sogou.com', '_blank', 'toolbar=yes, location=yes, status=yes, menubar=yes, scrollbars=yes');", list("dummy"))
```

```
## list()
```

```r
remDr$navigate("http://www.bing.com")
remDr$getCurrentWindowHandle()
```

```
## [[1]]
## [1] "CDwindow-0A5079621F5B3C138577A8EF45784B41"
```

```r
wins = remDr$getWindowHandles()
wins
```

```
## [[1]]
## [1] "CDwindow-D2A74741990DF703B350698C876FF3D3"
## 
## [[2]]
## [1] "CDwindow-0A5079621F5B3C138577A8EF45784B41"
## 
## [[3]]
## [1] "CDwindow-A71E2FE6096B65ECAA767DFAC0175B0F"
```
