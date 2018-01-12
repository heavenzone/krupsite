---
title: another toc solution for blogdown fixing the pure Chinese issue
author: Heaven Zone
date: '2018-01-12'
slug: another-toc-solution-for-blogdown-fixing-the-pure-chinese-issue
categories:
  - Heaven Zone
tags:
  - R
  - blogdown
  - toc
description: '为blogdown增加另外一个生成toc的方法'
simpletoc: true
---

## 简介

本文记录为R语言软件包blogdown增加另外一个生成toc的方法，修改自网上的一段代码。

我也把它放到了[github](https://github.com/heavenzone/simpletoc)，有相同需求的朋友也可以拿去用。

## 问题描述

用blogdown自带的生成toc功能发现如果headline全部是中文，toc目录就没有生成链接，toc就不能跳转，rmd生成html_document的时候也是同样的问题[【传送门】](https://github.com/rstudio/rmarkdown/issues/1241)，yihui回复是pandoc的问题，根据自己对javascript和jquery的一点点了解，觉得其实可以用js来生成toc的。

于是就有本文的出现了。

## 找解决方案

搜索一圈关于用javascript生成toc的方案，有最简单的几行代码就搞定可以生成toc的，例如如下（[原地址](https://gist.github.com/cloudsben/6059930)）：

```javascript
<script>
 $(document).ready(function(){
      $("h2,h3").each(function(i,item){
        var tag = $(item).get(0).localName;
        $(item).attr("id","wow"+i);
        $("#mytoc").append('<a class="new'+tag+'" href="#wow'+i+'">'+$(this).text()+'</a></br>');
        $(".newh2").css("margin-left",0);
        $(".newh3").css("margin-left",20);
      });
 });
</script>
```

后来发现还可以**自动生成标题号**的，感觉上面那段代码挺简洁的，尝试自己在上面修改增加自动生成标题功能，发现自己不是程序员，不懂算法，没弄成，看了另外一个也是带自动生成带编码toc的，看不懂。

还了解到rmarkdown貌似也用到了tocify，看了一下发现太多依赖，既jquery又jquery-ui还bootstrap，要链接好几个文件，于是想一个简单的。

还看了[这个](http://i5ting.github.io/i5ting_ztree_toc/build/jquery.plugin.html)（太复杂不适合）和[这个](https://github.com/yijian166/md-toc.js)（没弄成功），最后看到[**这里**](https://www.cnblogs.com/qiudeqing/p/3229583.html)，发现功能简单了一点，于是决定修改一下代码增加了几个功能：

- 可以修改在哪个标签下面进行搜索headlines（原来是body下的）
- 可以修改headlines的起始标题（原来是h1-h6，通常我们只需要h2-h4就够了）
- 一些小优化

## 应用到blogdown主题

下面简单说说怎么把这个弄到blogdown上去。

### 我的blogdown的主题

我的blogdown主题修改自[blackburn](https://github.com/yoshiharuyamashita/blackburn)。

### 修改js文件

修改[上面所说的文档](https://www.cnblogs.com/qiudeqing/p/3229583.html)办成了下面这个样子：

```javascript
 /**
 *    本代码修改自：https://www.cnblogs.com/qiudeqing/p/3229583.html
 *
 **/
 // =================添加变量控制标题深度
 // 默认是h2至h4;
// 定于全局divID，方便toc函数和diyStyle参数调用
function isInArray(value,arr){
	for(var i = 0; i < arr.length; i++){
		if(value === arr[i]){
			return true;
		}
	}
	return false;
}	

function toc(firstLevel = 2, lastLevel = 3, divID = "TOC", divToSearch = "" )
{

	// 如果参数设置不正确的调整
	if (firstLevel < 1 || firstLevel > 6 || firstLevel > lastLevel) firstLevel = 2;
	if (lastLevel < firstLevel || lastLevel > 6) lastLevel = 3;
	if (divToSearch == "") {
		elementToSearch = document.body;
	} 
	else {
		elementToSearch = document.getElementById(divToSearch);
	}
	var depth = lastLevel - firstLevel + 1;
	var searchHeadings = new Array();

	// Find the TOC container element.
	// If there isn't one, create one at the start of the document.
	var toc = document.getElementById(divID);
	if (!toc) {
		 toc = document.createElement("div");
		 toc.id = divID;
		 document.body.insertBefore(toc, document.body.firstChild);
	}
	// Find all section heading elements
	var headings;
	// =================生成要生成toc标题的标签
	for (var i=firstLevel;i<firstLevel+depth;i++)
	{
		searchHeadings[i] = "H"+i;
	}
	 //if (document.querySelectorAll) // Can we do it the easy way?
	 //    headings = document.querySelectorAll("h1,h2,h3,h4,h5,h6");
	 //else   // Otherwise, find the headings the hard way
	 // ==================用elementToSearch控制搜索范围
	headings = findHeadings(elementToSearch, [], searchHeadings);
	 // Recursively traverse the document body looking for headings
	 function findHeadings(root, sects) {
		 for(var c = root.firstChild; c != null; c = c.nextSibling) {
			 if (c.nodeType !== 1) continue;
			 //if (c.tagName.length == 2 && c.tagName.charAt(0) == "H")
			 // ================= 判断tagName是否在设置的标签里面
			 if (isInArray(c.tagName, searchHeadings))
				 sects.push(c);
			 else 
				 findHeadings(c, sects);
		 }
		 return sects;
	 }
	 // Initialize an array that keeps track of section numbers.
	 var sectionNumbers = [0,0,0,0,0,0];
	 // Now loop through the section header elements we found.
   for(var h = 0; h < headings.length; h++) {
		 var heading = headings[h];
		 // Skip the section heading if it is inside the TOC container.
		 if (heading.parentNode == toc) continue;
		 // Figure out what level heading it is.
		 var level = parseInt(heading.tagName.charAt(1));
		 if (isNaN(level) || level < 1 || level > 6) continue;
		 // Increment the section number for this heading level
		 // and reset all lower heading level numbers to zero.
		 sectionNumbers[level-1]++;
		 for(var i = level; i < 6; i++) sectionNumbers[i] = 0;
		 // Now combine section numbers for all heading levels
		 // to produce a section number like 2.3.1.
		 //=================修改成从firstLevel开始生成toc 
		 var sectionNumber = sectionNumbers.slice(firstLevel-1,level).join(".")
		 // Add the section number to the section header title.
		 // We place the number in a <span> to make it styleable.
		 var span = document.createElement("span");
		 span.className = "TOCSectNum";            
		 span.innerHTML = sectionNumber;
		 heading.insertBefore(span, heading.firstChild);
		 //===============改成直接在headlines上添加id用于链接跳转
		 heading.setAttribute("id", divID+sectionNumber);
		 
		 // Wrap the heading in a named anchor so we can link to it.
		 //var anchor = document.createElement("a");
		 //anchor.name = "TOC"+sectionNumber; 
		 //heading.parentNode.insertBefore(anchor, heading);
		 //anchor.appendChild(heading);
		 
		 // Now create a link to this section.
		 var link = document.createElement("a");
		 link.href = "#"+divID+ sectionNumber; // Link destination
		 link.innerHTML = heading.innerHTML; // Link text is same as heading
		 // Place the link in a div that is styleable based on the level.
		 var entry = document.createElement("div");
		 //=================修改成firstLevel的标题在生成cssClass的名称的level为1开始递增
		 level = level-firstLevel+1;
		 entry.className = "TOCEntry TOCLevel" + level ; 
		 entry.appendChild(link);
		 // And add the div to the TOC container.
		 toc.appendChild(entry);
	 }
 }

// 添加样式函数
function addNewStyle(newStyle) {
	var styleElement = document.getElementById('styles_js');

	if (!styleElement) {
		styleElement = document.createElement('style');
		styleElement.type = 'text/css';
		styleElement.id = 'styles_js';
		document.getElementsByTagName('head')[0].appendChild(styleElement);
	}
	styleElement.appendChild(document.createTextNode(newStyle));
}
// 自定义样式，用不同的divid也可以有默认样式
function diyStyle (divID) {
	addNewStyle('#'+divID+' .TOCSectNum { padding-right: 0.3em; }');
	addNewStyle('#'+divID+' a { text-decoration: none; color: #0077bb !important; }');
	addNewStyle('#'+divID+' .TOCLevel1 { padding-left: 0.2em; border-left:#0077bb solid 0.2em;}');
	for (i = 2; i<=6; i++) {
		k = i-1;
		addNewStyle('#'+divID+' .TOCLevel'+i+' { margin-left: '+k+'em;}');
	}
	//addNewStyle('#'+divID+' { width: 200px;max-height: 100%;overflow: auto;margin-left: 50%;position: fixed;border: 1px solid #ccc;webkit-border-radius: 6px;moz-border-radius: 6px;border-radius: 12px;padding: 0.8em;');
	addNewStyle('#'+divID+' {float:right;margin:0 0 1em 1em;position:fixed;top:100px;right:50px;border: 1px solid #ccc;webkit-border-radius: 6px;moz-border-radius: 6px;border-radius: 12px;padding: 0.8em;}');
}
```

把这个代码保存好，我把它改了个名字叫`simpletoc.js`，然后把它放到`themes/<theme-name>/static/js/simpletoc.js`。

### 添加partials文件**simpletoc.html**

接着在`themes/<theme-name>/layouts/partials/`下面添加文件`simpletoc.html`，内容如下：

```javascript
<script src="/js/krup-toc.js"></script>
<script>
	var firstLvl = 2; // h2开始
	var lastLvl = 4;  // h4结束
	var divID = "TOC";
	var divToSearch = "post-content";
	window.onload=function(){
		//参数控制
		// 参数一：起始level，默认为h2
		// 参数二：末位Level，默认为h3
		// 参数三：用于生成toc的divID，默认为TOC
		// 参数四：搜索headlines的divID
		toc(firstLvl, lastLvl, divID, divToSearch);
		diyStyle(divID);
		addNewStyle('@media (max-width: 48em) {#'+divID+'{display: none;} }');
	}
</script>
<div id="TOC"></div>
```

### 修改`single.html`文件

接着修改`themes/<theme-name>/layouts/post/single.html`文件，其实就是增加了带simpletoc那三行代码：

```html
{{ partial "header.html" . }}
<div class="header">
  <h1>{{ .Title }}</h1>
  <h2>{{ .Description }}</h2>
</div>
<div id="post-content" class="content">

  {{ partial "post_meta.html" . }}

{{ if .Params.simpletoc }}
  {{ partial "simpletoc.html" . }}
{{ end }}
  
  {{ .Content }}
  {{ partial "prev_next_post.html" . }}
  {{ partial "disqus.html" . }}
</div>
{{ partial "footer.html" . }}
```


而其中的`.Params.simpletoc`是用来在**rmd文件**里面添加参数`simpletoc`来控制要不要显示toc，例如下面这个rmd头部：

```
---
title: Test toc
author: Heaven Zone
date: '2018-01-11'
slug: test-toc
categories:
  - R
tags:
  - R
  - blogdown
simpletoc: true
---
```

### 调整样式

由于不懂美工，外观有点简陋。

默认样式主要是在`simpletoc.js`文件通过js添加了，由于[blackburn](https://github.com/yoshiharuyamashita/blackburn)主题用的是[pure](https://purecss.io)框架(其实blackburn也是基于该主页的默认主题)，所以是响应式的，于是在partials文件`simpletoc.html`里面添加了下面这个样式：

```
addNewStyle('@media (max-width: 48em) {#'+divID+'{display: none;} }');
```

也就是当浏览器宽度缩小到一定程度就不显示toc了，这个是针对pure框架的，其他框架css可能不知道是不是也是一样。

另外也针对blackburn主题做了一个小调整，当浏览器宽度缩小但不至于消失的情况下，调整了标签layout的padding属性，为toc预留空间，避免遮挡了文章内容，修改文件为`themes/<theme-name>/css/side-menu.css`，在倒数第二段，也就是`@media (min-width: 48em) {`里面的`#layout`里面增加了`padding-right: 200px; `。

没有作高度缩小的时候自动折叠滚动的处理，现在这样基本都满足需要了，有空闲再做研究-_-!

## 效果图

最后上一个效果图吧。

![](https://gitee.com/heavenzone/picturebed/raw/master/zhonghaoguang.com/20180112-1-simpletoc.png)




