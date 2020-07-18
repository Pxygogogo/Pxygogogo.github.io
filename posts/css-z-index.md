---
title: 'css踩坑之z-index'
date: 2020-05-11 22:36:28
tags: [css]
published: true
hideInList: false
feature: 
isTop: false
---


z-index 属性指定一个元素的堆叠顺序
拥有更高堆叠顺序的元素总是会处于堆叠顺序较低的元素的前面

==注意！！！： z-index 只适用于已定位的元素(position:absolute, position:relative, or position:fixed)==

所以z-index是一种依赖属性，依赖position属性


使用的相对性

z-index值只决定同一父元素中的同级子元素的堆叠顺序。父元素的z-index值（如果有）为子元素定义了堆叠顺序（css版堆叠“拼爹”）。向上追溯找不到含有z-index值的父元素的情况下，则可以视为自由的z-index元素，它可以与父元素的同级兄弟定位元素或其他自由的定位元素来比较z-index的值，决定其堆叠顺序。同级元素的z-index值如果相同，则堆叠顺序由元素在文档中的先后位置决定，后出现的会在上面。

所以如果当你发现一个z-index值较大的元素被值较小的元素遮挡了，请先检查它们之间的dom结点关系，多半是因为其父结点含有激活并设置了z-index值的position定位元素。

也因为这个相对性，还会引发浏览器表现不一致出现兼容问题。原因是ie6、7下面position值为非static的元素在未设置z-index值的情况下都会被隐含添加z-index:0，而Firefox/Chrome等现代浏览器会遵循标准默认z-index:auto不会产生值。

还有一点需要注意，负值的z-index也依照大小比较的原理，但一般来说负值的z-index会被透明的body覆盖导致点击等事件响应出现问题，请谨慎使用。

>参考
>
>[浅析CSS——元素重叠及position定位的z-index顺序](https://www.cnblogs.com/mind/archive/2012/04/01/2198995.html)
>
>
>[CSS的position和z-index有关](https://godbasin.github.io/2016/06/25/about-position/)