---
title: 'react中获取属性的问题'
date: 2020-05-13 22:12:40
tags: [react]
published: true
hideInList: false
feature: 
isTop: false
---

react 获取属性的问题，currentTarget确实是可以的，target确实是有问题的

父节点有属性data-cc 这时候点击父节点获取属性

正确方式是 event.currentTarget.getAttribute(“data-cc”)

而不是event.target.getAttribute(“data-cc”)

ps:

currentTarget 返回其事件监听器触发该事件的元素。 target 返回触发此事件的元素（事件的目标节点）。
react添加自定义属性必须是 data-xx 形式
最简单直白理解event.target指向引起触发事件的元素，而event.currentTarget则是事件绑定的元素

关于currentTarget和target详细区别见下篇文章

[原文地址](https://blog.csdn.net/fortunegrant/article/details/83754945)

