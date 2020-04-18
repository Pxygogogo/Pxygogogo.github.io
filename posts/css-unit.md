---
title: 'css常见单位'
date: 2020-04-16 11:27:08
tags: [css]
published: true
hideInList: false
feature: 
isTop: false
---
## 常见单位
1. px：像素相对长度单位，相对于显示器屏幕分辨率
1. em：相对长度单位基准点为父节点字体的大小，如果自身定义了font-size按自身来计算（浏览器默认字体是16px），整个页面内1em不是一个固定的值。
1. rem：相对单位可理解为”root em”, 相对根节点html的字体大小来计算，CSS3新加属性，chrome/firefox/IE9+支持。
1. vw：viewpoint width，视窗宽度，1vw等于视窗宽度的1%。
1. vh：viewpoint height，视窗高度，1vh等于视窗高度的1%。
1. vmin：vw和vh中较小的那个。
1. vmax：vw和vh中较大的那个。
1. %:百分比
1. in:寸
1. cm:厘米
1. mm:毫米
1. pt:point，大约1/72寸
1. pc:pica，大约6pt，1/6寸
1. ex：取当前作用效果的字体的x的高度，在无法确定x高度的情况下以0.5em计算(IE11及以下均不支持，firefox/chrome/safari/opera/ios safari/android browser4.4+等均需属性加么有前缀)
1. ch:以节点所使用字体中的“0”字符为基准，找不到时为0.5em(ie10+,chrome31+,safair7.1+,opera26+,ios safari 7.1+,android browser4.4+支持)

## 常用
其中用的最多的单位是**px、em、rem**，这三个的区别是：
px是固定的像素，一旦设置了就无法因为适应页面大小而改变。

em和rem相对于px更具有灵活性，他们是相对长度单位，意思是长度不是定死了的，更适用于响应式布局。

对于em和rem的区别一句话概括：em相对于父元素，rem相对于根元素。

rem中的r意思是root（根源），这也就不难理解了。

==em/rem==：用于做响应式页面，不过我更倾向于rem，因为em不同元素的参照物不一样（都是该元素父元素），所以在计算的时候不方便，相比之下rem就只有一个参照物（html元素），这样计算起来更清晰。

关于vh/vw与%区别：
vh和vw大小是相对于视口的高度和宽度，而不是父元素的，%单位是相对于包含它的最近的父元素的高度和宽度。

