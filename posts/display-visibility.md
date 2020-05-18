---
title: 'display none与visibility hidden的区别'
date: 2020-05-18 22:30:42
tags: [css]
published: true
hideInList: false
feature: 
isTop: false
---
> 面试题常考，以为自己足够理解，但第一次在真实场景中使用还是
> 没有注意，记录之，自省！

## 联系：它们都能让元素不可见

## 区别：

###  其一
- display:none 会让元素完全从渲染树中消失，渲染的时候不占据任何空间；
- visibility: hidden;不会让元素从渲染树消失，渲染时元素继续占据空间，只是内容不可见

### 其二
- visibility:hidden; 使用该属性后，HTML元素（对象）仅仅是在视觉上看不见（完全透明），而它所占据的空间位置仍然存在，也即是说它仍具有高度、宽度等属性值。
- display:none; 使用该属性后，HTML元素（对象）的权度、高度等各种属性值都将“丢失”。


### 其三
- display: none;是非继承属性，子孙节点消失由于元素从渲染树消失造成，通过修改子孙节点属性无法显示；
- visibility: hidden;是继承属性，子孙节点消失由于继承了hidden，通过设置visibility: visible;可以让子孙节点显示

### 其四
修改常规流中元素的display通常会造成文档重排。修改visibility属性只会造成本元素的重绘。

### 其五
读屏器不会读取display: none;元素内容,会读取visibility: hidden;元素内容