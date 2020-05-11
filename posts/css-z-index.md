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

