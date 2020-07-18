---
title: 'js上拉加载更多'
date: 2020-07-18 13:54:19
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
![image](https://image-static.segmentfault.com/205/199/2051991224-5bf3d43544997_articlex)

scrollTop: 滚动视窗的高度距离window顶部的距离，它会随着往上滚动而不断增加，初始值是0，它是一个变化的值；
clientHeight: 它是一个定值，表示屏幕可视区域的高度
scrollHeight: 页面不能滚动时是不存在的，body长度超过window时才会出现，所表示body所有元素的长度

由上可得：

```js
handleScroll = () => {
    const { clientHeight, scrollTop } = document.documentElement;
    const { scrollHeight } = document.body;
    if (scrollTop + clientHeight >= scrollHeight) {
      this.throttleHandleFetchMore();
    }
  };
```