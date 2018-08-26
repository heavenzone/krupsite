---
title: 清爽新浪NBA文章页面
author: Heaven Zone
date: '2018-08-26'
slug: clean-sina-nba-article-page
categories:
  - css
tags:
  - css
  - javascript
---

## 简介

习惯性要浏览一下新浪的NBA新闻（虽然新闻质量比较差），对页面上多余的元素实在是不爽，虽然adblock已经处理了很多东西，还是觉得不够简洁，于是尝试自己弄一下**stylish**和**Greasemonkey**，其实只用**stylish**就够了，只不过用**Greasemonkey**实现点击图片把图片放大居中，点击恢复的功能。


## stylish

尝试自己写一写css，主要就是把不想显示的东西`display: none;`就ok了，另外也把文章中的图片`float:right;`，代码如下：


```css
/* 新浪文章 */
 .path-search, #sina-header, .article-content-right, #tab_related, #top_bar .page-tools, #top_bar_wrap, .second-title, #videoList0, #article-bottom *, .img_descr:empty {
    display: none;
}
.op-bar-inner, .main-title {
    text-align: center;
}
#article_content {
    width: 100%;
    display: flex;
    align-items: center;
    flex-direction: column;
}
.article-content-left {
    width: 960px;
}

#artibody .img_wrapper {
    width: 480px;
    height: auto;
    float: right;
    margin: 0em 0em 1em 1em;
    padding: 0;
}
.img_wrapper img {
    width:100%;
    margin:0;
    padding: 0;
    box-shadow: 0 3px 3px #bbbbbb;
}
```

**应用对象**那里选择正则表达式，输入`http[s]?:\/\/sports.sina\.com\.cn\/basketball\/.*`

## Greasemonkey

不想依赖JQuery或者其他js框架，只好自己写了个`img_zoom`函数，主要思路就是为图片标签添加个`onclick`事件，触发该事件就在`body`标签添加一个临时div标签，然后这个标签设置成fixed定位上边和左边都为0，长度高度分别为100vw、100vh，然后把被点击的图片clone到该临时div标签，然后这个临时div标签也添加一个`onclick`事件，触发后就把此标签的`innerHTML`设为空，并设置css属性`display:none;`，由于此临时div标签是布满浏览器的，所以随便一点就恢复原样。

```js
// ==UserScript==
// @name     krup.sina
// @include  /http[s]?:\/\/sports.sina.com.cn\/basketball\/.*/
// @version  1
// @grant    none
// @run-at   document-end
// ==/UserScript==
function img_zoom(img_selector) {
  // 图片放大 //////////////////////
  // 添加div
  bd.insertAdjacentHTML('beforeend', '<div class="imgzoom"></div>');
  imgzoom = document.querySelector('.imgzoom');
  imgzoom.style.display = "none";

  var imgsObj = document.querySelectorAll(img_selector);
  if (imgsObj) {
    Array.prototype.forEach.call(imgsObj,
    function(item) {
      item.onclick = function() {
        imgzoom.appendChild(item.cloneNode());
        imgzoom.style.display = "flex";
        var styleelm = document.createElement('style');
        styleelm.innerHTML = '.imgzoom{ position:fixed; top:0;left:0; width:100vw; height: 100vh; background-color: #eee; background: rgba(240, 240, 240, 0.5);justify-content:center; align-items:center;}';
        document.querySelector('head').appendChild(styleelm);
      };
      console.log(item.nodeName);
    });
  };

  imgzoom.onclick = function() {
    imgzoom.innerHTML = '';
    imgzoom.style.display = "none";
  };
}

window.onload = function() {
  bd = document.querySelector('body');

  var maincontent = document.querySelector('.main-content');
  maincontent.classList.remove("w1240");

  img_zoom('.img_wrapper img');

  console.log("运行成功！");

}
```

## 对比效果

这是用了adblock的页面，仍然不够简洁（没用adblock更加不堪入目）：

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/2018/2018-08-26-sina-ori.jpg)

下面是应用了上面两个工具后的页面，清爽了很多：

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/2018/2018-08-26-sina-new.jpg)
