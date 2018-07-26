---
title: 用RSelenium自动下载RStudio的cheatsheet
author: Heaven Zone
date: '2018-07-26'
slug: download-rstudio-cheatsheets-with-RSelenium
categories:
  - R
tags:
  - R
  - RSelenium
---

想下载rstudio这个页面的所有cheatSheet，虽然一个一个点也很快下完，不过最近在玩**RSelenium**，那么就试试让它来干这个事情吧(尽管有点大材小用)。

上码：

```r
hg_rs_server_selenium = function(filepath) {
  command = paste("java -jar", filepath)
  system(command,wait = FALSE, invisible = FALSE)
}

require(rvest)
require(xml2)
require(XML)
require(tidyverse)
require(RSelenium)
hg_rs_server_selenium("c:/krupSoftware/bin/selenium-server-standalone-3.7.1.jar")
remdr <- RSelenium::remoteDriver(browserName = "chrome")
remdr$open()
url = "https://www.rstudio.com/resources/cheatsheets/"
remdr$navigate(url)
psource = remdr$getPageSource()
page = read_html(psource[[1]])
anodes = rvest::html_nodes(page, "a.fusion-button")
links = xml2::xml_attr(anodes, "href")
links = links[grepl("pdf$", links)]

lapply(links, function(w) {
  remdr$navigate(w)
})
remdr$close()
```