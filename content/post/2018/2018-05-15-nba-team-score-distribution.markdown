---
title: nba历年球队比赛得分分布
author: Heaven Zone
date: '2018-05-15'
slug: nba-team-score-distribution
categories:
  - R
tags:
  - R
  - ggridges
  - NBA
---

其实现在很少真正去看nba比赛了，因为人称**东艾西科南麦北卡**的年代已经过去了，不过打开电脑还是不时的第一时间打开nba网页看看新闻，有种感觉就是看现在的比分，发现很多都是120+，130+的比分，就算是季后赛也如此，所以一直很想对比一下，究竟Jordan年代的比分是否跟现在有明显差别？

前段时间在统计之都看到有介绍叠嶂图，发现蛮好看，也在Y叔的订阅号看到画法，也不小心在[这里](https://github.com/fivethirtyeight/data/tree/master/nba-carmelo)得到一个关于nba的数据集，包含nba历年所有比赛的比分，于是就想到试试用叠嶂图对比一下不同时期的比分情况。


```r
library(tidyverse)
library(ggthemr)
library(ggridges)
library(showtext)
ggthemr('dust')
df <- read.csv("c:/data/nba_elo.csv")

df.new = df %>% select(season, score1, score2) %>%
  gather("result", "score", -season) %>% 
  mutate(season = as.factor(season)) %>%
  mutate(decade = paste0(substring(season, 1, 3), "0s")) %>% 
  dplyr::filter(!is.na(score)) %>%
  group_by(season) %>%
  mutate(minscore = min(score)) %>%
  group_by(decade) %>%
  mutate(minscore = mean(minscore)+5)

maxdate <- max(as.Date(as.character(subset(df, !is.na(score1), select = date)[, 1])))

pnba =  ggplot(df.new) +
  aes(score, season, fill = decade) +
  geom_density_ridges(scale = 4, alpha = 0.75) +
  theme_ridges(grid = FALSE, center = TRUE) +
  scale_y_discrete("", breaks = NULL, expand = c(0.005, 0)) +
  geom_vline(xintercept = 100, alpha = 0.8) +
  scale_x_continuous(expand = c(0, 0), 
                     limits = c(30, 160),
                     breaks = c(40, 60, 80, 100, 120,140)) +
    geom_text(aes(minscore, as.numeric(season)+0.4, label = season)) +
  labs(title = "NBA球队历年比赛得分分布",
       subtitle = paste0("数据包含1947-11-01至", maxdate, "常规赛季后赛")) +
  xlab("得分") + 
  guides(fill=guide_legend(reverse=TRUE, title = "年代"))

pnba
```

<img src="/post/2018/2018-05-15-nba-team-score-distribution_files/figure-html/nba-1.png" width="672" />


# 参考资料

- <https://cosx.org/2018/04/ridgeline-story/>
- <https://mp.weixin.qq.com/s/TtSuT0d4j80A7NeNIl12Qg>
- 数据来源：<https://github.com/fivethirtyeight/data/tree/master/nba-carmelo>
