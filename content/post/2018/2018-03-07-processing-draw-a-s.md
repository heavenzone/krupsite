---
title: 用processing画了个S
author: Heaven Zone
date: '2018-03-07'
slug: processing-draw-a-s
categories:
  - processing
tags:
  - processing
description: '用processing画图'
keywords: "processing"
showtoc: true
---

## 简介

最近有朋友叫我帮忙设计个logo，晕，我都不是搞设计的，怎么设计logo，她就叫我从几幅图里面各取一部分特点合成一幅图，其中有一个字母**S**的形状大概如下图所示，虽然自己也能用photoshop晚一些移花接木的小玩意，或者用lightroom将数码照片做一些润色，但是设计logo是需要创造力的，是从无到有，不是搞设计是很难的，也弄了一下怎么弄都不好看。

我又想，photoshop里面怎么才能把字母画的绝对对称呢？就是要精确的那种，我不会，其他AutoCAD等专业做图软件也没玩过，虽然我相信自己那本教科书看一下也是可以做出来，但是考虑到装那些商业软件有点困难，于是就想到了之前稍微了解过的**processing**，免费软件，安装也是相当简单，到市图书馆借了本书参考参考，于是就写了下面的代码把这个**S**画了出来，下面的代码加了一些动画，可以动态调整S上面的夹角看不同大小的夹角，字母S的整体效果。

画这个**S**还是花了一点点功夫，主要是用到了高中的一些三角函数知识和初中已知四个点求交点坐标等知识。


## 图片

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/2018/20180307-001-processing-s.png)


## 代码


```
int x= 0;
int speed = 1;
float aspeed = 0.5;
float angle = 60;

void setup() {
  size(600, 600);
  background(255);

}

void draw() {
  background(255);
  fill(175);
  
  if(x > width) {
    speed = speed * -1;
  } else if (x < 0) {
    speed = speed * -1;
  }
  if(angle > 70) {
    aspeed = aspeed * -1;
  } else if (angle < 50) {
    aspeed = aspeed * -1;
  }
  x = x + speed;
  angle = angle + aspeed;
  mys(x ,20 ,300, 80, angle , angle);
  line(x, 0, x, height);
}


void mys (float ax, float ay, float w, float hbc, float anglea, float angleb) {
  float sw = w;  float hc = hbc;
  float alpha = radians(anglea);
  float beta = radians(angleb);
  //a
  
  float sr = ax + sw/2;  float sl = ax - sw/2;
  
  //b
  float bx = sr;
  float by = sw/2/tan(alpha)+ay;
  
  //c
  float cx = sr;  float cy = by + hc;
  
  //d
  float dx = ax;  float dy = ay + hc;
  
  //n
  float nx = sl;  float ny = sw/2/tan(beta)+ay;
  
  //m
  float mx = sl;  float my = ny + hc;
  
  //f
  float fx = sr;  float fy = ny + sw/tan(alpha);
  
  //g
  float gx = sr;  float gy = fy + hc;
  
  //h
  float hx = ax; float hy = gy + sw/2/tan(beta);
  
  float sh = hy - ay;
  
  //i
  float ix = sl; float iy = ay + hy - by;
  //j
  float jx = sl; float jy = ay + hy - cy;
  //k 
  float kx = ax; float ky = ay + hy - dy;
  
  //e
  float ex;  float ey;
  float dmk = (dy - my)/(dx - mx);
  float dmb = dy - dx*(dy-my)/(dx-mx);
  float nfk = (ny - fy)/(nx - fx);
  float nfb = ny - nx * (ny -fy)/(nx - fx);
  ex = (nfb - dmb)/(dmk - nfk);
  ey = ex * dmk + dmb;
  
  //l
  float lx = sw-(ex-sl)+sl;
  float ly = ay + hy - ey;
  
  print("ex =", ex, "\n ey =", ey , "\n");
  print("dmk=",dmk,"\n");
  print("dmb=",dmb,"\n");
  print("nfk=",nfk,"\n");
  print("nfb=",nfb,"\n");
  
  for(int i = 0; i<500;i = i + 5) {
    point(i, i*nfk + nfb);
  }
  for(int i = 0; i<500;i = i + 5) {
    point(i, i*dmk + dmb);
  }
  
  beginShape();
  vertex(ax,ay);
  vertex(bx,by);
  vertex(cx,cy);
  vertex(dx,dy);
  vertex(ex,ey);
  vertex(fx,fy);
  vertex(gx,gy);
  vertex(hx,hy);
  vertex(ix,iy);
  vertex(jx,jy);
  vertex(kx,ky);
  vertex(lx,ly);
  vertex(mx,my);
  vertex(nx,ny);
  vertex(ax,ay);
  endShape();
}


```