---
title: '防抖'
date: 2020-02-12 10:48:21
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
## 前言
在前端开发中会遇到一些频繁的事件触发，比如：

1. window 的 resize、scroll
2. mousedown、mousemove
3. keyup、keydown
    ......

## 引例

index.html文件如下


```
<!DOCTYPE html>
<html lang="zh-cmn-Hans">

<head>
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=edge, chrome=1">
    <title>debounce</title>
    <style>
        #container{
            width: 100%; height: 200px; line-height: 200px; text-align: center; color: #fff; background-color: #444; font-size: 30px;
        }
    </style>
</head>

<body>
    <div id="container"></div>
    <script>
        var count = 1;
        var container = document.getElementById('container');
    
        function getUserAction() {
            container.innerHTML = count++;
        };
    
        container.onmousemove = getUserAction;
    </script>
</body>

</html>
```

效果如下：
![演示效果](https://i.loli.net/2020/03/25/U3I84RAEVWqksZN.gif)

从左边滑到右边就触发了 129 次 getUserAction 函数！

因为这个例子很简单，所以浏览器完全反应的过来，可是如果是复杂的回调函数或是 ajax 请求呢？假设 1 秒触发了 60 次，每个回调就必须在 1000 / 60 = 16.67ms 内完成，否则就会有卡顿出现。

为了解决这个问题，一般有两种解决方案：

1. debounce 防抖
2. throttle 节流

下面重点介绍防抖的实现。

## 防抖

防抖的原理就是：你尽管触发事件，但是我一定在事件停止触发 n 秒后才执行。

这意味着如果你在一个事件触发的 n 秒内又触发了这个事件，那我就以新的事件触发的时间为准，在此时间 n 秒后才执行。

总之，就是要等你触发完事件 n 秒内不再触发事件，我才执行!

## 第一版

根据这段表述，我们可以轻松写出第一版的代码：

```js
//第一版
function debounce(func,wait){
    var timeout;
    return function(){
        clearTimeout(timeout);
        timeout = setTimeOut(func,wait)
    }
}
```

如果我们要使用它，以最一开始的例子为例：

```js
container.onmousemove = debounce(getUserAction, 1000);
```

现在随你怎么移动，反正你移动完 1000ms 内不再触发，我才执行事件。

## this

如果我们在 `getUserAction` 函数中 `console.log(this)`，在不使用 `debounce` 函数的时候，`this` 的值为：

```html
<div id="container"></div>
```

但是如果使用我们的 debounce 函数，this 就会指向 Window 对象！

所以我们需要将 this 指向正确的对象。

我们修改下代码：

```js
//第二版
function debounce(func,wait){
    var timeout;
    return function(){
        var context = this;
        clearTimeout(timeout);
        timeout = setTimeOut(function(){
            func.apply(context)
        },wait)
    }
}
```

现在 this 已经可以正确指向了。让我们看下个问题：

## event 对象

JavaScript 在事件处理函数中会提供事件对象 event，我们修改下 getUserAction 函数：


```js
function getUserAction(e) {
    console.log(e);
    container.innerHTML = count++;
};
```
如果我们不使用 debouce 函数，这里会打印 MouseEvent 对象


但是在我们实现的 debounce 函数中，却只会打印 undefined!

所以我们再修改一下代码：

```js
//第三版
function debounce(func.wait){
    var timeout;
    return function(){
        var context = this;
        vat args = arguments;
        clearTimeOut(timeout);
        timeout = setTimeOut(function(){
            func.apply(context,args)
        },wait)
    }
}
```

到此为止，我们修复了两个小问题：

1. this 指向
2. event 对象

## 立刻执行

这个时候，代码已经很是完善了，但是为了让这个函数更加完善，我们接下来思考一个新的需求。

这个需求就是：

我不希望非要等到事件停止触发后才执行，我希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行。

想想这个需求也是很有道理的嘛，那我们加个 immediate 参数判断是否是立刻执行。


```js
// 第四版
function debounce(func, wait, immediate) {

    var timeout;

    return function () {
        var context = this;
        var args = arguments;

        if (timeout) clearTimeout(timeout);
        if (immediate) {
            // 如果已经执行过，不再执行
            var callNow = !timeout;
            timeout = setTimeout(function(){
                timeout = null;
            }, wait)
            if (callNow) func.apply(context, args)
        }
        else {
            timeout = setTimeout(function(){
                func.apply(context, args)
            }, wait);
        }
    }
}
```
## 返回值

此时注意一点，就是 getUserAction 函数可能是有返回值的，所以我们也要返回函数的执行结果，但是当 immediate 为 false 的时候，因为使用了 setTimeout ，我们将 func.apply(context, args) 的返回值赋给变量，最后再 return 的时候，值将会一直是 undefined，所以我们只在 immediate 为 true 的时候返回函数的执行结果。 


```js
// 第五版
function debounce(func, wait, immediate) {

    var timeout, result;

    return function () {
        var context = this;
        var args = arguments;

        if (timeout) clearTimeout(timeout);
        if (immediate) {
            // 如果已经执行过，不再执行
            var callNow = !timeout;
            timeout = setTimeout(function(){
                timeout = null;
            }, wait)
            if (callNow) result = func.apply(context, args)
        }
        else {
            timeout = setTimeout(function(){
                func.apply(context, args)
            }, wait);
        }
        return result;
    }
}
```

