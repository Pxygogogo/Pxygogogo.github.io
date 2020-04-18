---
title: '回流与重绘'
date: 2020-04-12 18:24:09
tags: [浏览器相关]
published: true
hideInList: false
feature: 
isTop: false
---

==一句话：回流必将引起重绘，重绘不一定会引起回流。==

想要真正的了解回流和重绘，我们首先需要搞清楚浏览器的渲染过程。

## 浏览器的渲染过程
浏览器渲染过程如下：

1. 解析HTML，生成DOM树，解析CSS，生成CSSOM树
2. 将DOM树和CSSOM树结合，生成渲染树(Render Tree)
3. Layout(回流):根据生成的渲染树，进行回流(Layout)，得到节点的几何信息（位置，大小）
4. Painting(重绘):根据渲染树以及回流得到的几何信息，得到节点的绝对像素
5. Display:将像素发送给GPU，展示在页面上。（这一步其实还有很多内容，比如会在GPU将多个合成层合并为同一个层，并展示在页面中。而css3硬件加速的原理则是新建合成层,在这里先不做展开）

渲染过程看起来很简单，让我们来具体了解下每一步具体做了什么。

### 生成渲染树

为了构建渲染树，浏览器主要完成了以下工作：

1. 从DOM树的根节点开始遍历每个可见节点。
2. 对于每个可见的节点，找到CSSOM树中对应的规则，并应用它们。
3. 根据每个可见节点以及其对应的样式，组合生成渲染树。

第一步中，既然说到了要遍历可见的节点，那么我们得先知道，什么节点是不可见的。不可见的节点包括：

- 一些不会渲染输出的节点，比如script、meta、link等。
- 一些通过css进行隐藏的节点。比如display:none。注意，利用visibility和opacity隐藏的节点，还是会显示在渲染树上的。只有display:none的节点才不会显示在渲染树上。

**注意：渲染树只包含可见的节点**

### 回流
当Render Tree中部分或全部元素的尺寸、结构、或某些属性发生改变时，浏览器重新渲染部分或全部文档的过程称为回流。

会导致回流的操作：

1. 页面首次渲染
2. 浏览器窗口大小发生改变（因为回流是根据视口的大小来计算元素的位置和大小的）
3. 元素尺寸或位置发生改变（包括外边距、内边距、边框大小、高度和宽度等）
1. 元素内容变化（文字数量或图片大小等等）
1. 元素字体大小变化
1. 添加或者删除可见的DOM元素
1. 激活CSS伪类（例如：:hover）
1. 查询某些属性或调用某些方法

一些常用且会导致回流的属性和方法：

- clientWidth、clientHeight、clientTop、clientLeft
- offsetWidth、offsetHeight、offsetTop、offsetLeft
- scrollWidth、scrollHeight、scrollTop、scrollLeft
- scrollIntoView()、scrollIntoViewIfNeeded()
- getComputedStyle()
- getBoundingClientRect()
- scrollTo()

### 重绘 
当页面中元素样式的改变并不影响它在文档流中的位置时（例如：==color、background-color、visibility==等），浏览器会将新样式赋予给元素并重新绘制它，这个过程称为重绘。

## 性能影响
回流比重绘的代价要更高。

有时即使仅仅回流一个单一的元素，它的父元素以及任何跟随它的元素也会产生回流。

浏览器会维护一个队列，把所有引起回流和重绘的操作放入队列中，如果队列中的任务数量或者时间间隔达到一个阈值的，浏览器就会将队列清空，进行一次批处理，这样可以把多次回流和重绘变成一次。
当你访问以下属性或方法时，浏览器会立刻清空队列：


- clientWidth、clientHeight、clientTop、clientLeft
- offsetWidth、offsetHeight、offsetTop、offsetLeft
- scrollWidth、scrollHeight、scrollTop、scrollLeft
- width、height
- getComputedStyle()
- getBoundingClientRect()


因为队列中可能会有影响到这些属性或方法返回值的操作，即使你希望获取的信息与队列中操作引发的改变无关，浏览器也会强行清空队列，确保你拿到的值是最精确的。

## 如何避免
### CSS

1. 避免使用table布局。
1. 尽可能在DOM树的最末端改变class。
1. 避免设置多层内联样式。
1. 将动画效果应用到position属性为absolute或fixed的元素上。
1. 避免使用CSS表达式（例如：calc()）。

### JavaScript

1. 避免频繁操作样式，最好一次性重写style属性，或者将样式列表定义为class并一次性更改class属性。
1. 避免频繁操作DOM，创建一个documentFragment，在它上面应用所有DOM操作，最后再把它添加到文档中。
1. 也可以先为元素设置display: none，操作结束后再把它显示出来。因为在display属性为none的元素上进行的DOM操作不会引发回流和重绘。
1. 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。
1. 对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流。





### 参考文章：

[你真的了解回流和重绘吗](https://juejin.im/post/5c0f104551882509a7683d63#heading-7)

[浏览器的回流与重绘 (Reflow & Repaint)](https://juejin.im/post/5a9923e9518825558251c96a)