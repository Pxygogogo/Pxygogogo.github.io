---
title: 'window.location.href跳转问题'
date: 2020-07-02 22:14:44
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
window.location.href的赋值，并不会中断Javascript的执行。

因为 LocationChange 行为在浏览器内核中是起定时器异步执行的。
异步执行的好处是为了防止代码调用过深，导致栈溢出，另外也是为了防止递归进入加载逻辑，导致状态紊乱，保证导航请求是顺序执行的。

所以最好在后面加上return这样的语句结束掉。但是return 也只是退出该语句所在的函数，而不会中断整个js文件的执行。
throw new Error()可以中断后面同步代码的执行。






最近在做新项目上线后的异常治理，发现了一个频发的异常
```JS 
Cannot read property 'XXX' of undefined 
```
而这些XXX其实都是解构接口返回的数据，项目中对于HTTP请求做了一层封装，在SSO认证时，没有认证的用户会跳转到SSO登录页。

```js
if (data.status === 401) {
    window.location.href = ssoTargetUrl;
    return Promise.resolve();
}
```

由于是SPA应用，location.href并不会阻塞打包出来的单一js文件的继续执行，return语句也只会结束其所在函数。导致后续react的渲染进程实际上是在继续的，所以报了一堆类似的无法读取值的错误。


思考到了三个解决方案:

#### 方案一：
    在页面最开始进行渲染的地方预先发送一个请求以判断其是否登录，未登录则选择不渲染children的元素内容从而避免异常抛出。
    
优点： 统一拦截，如果没有登录，不会抛出异常

缺点： 比较考验接口返回的时间，可能会影响首屏时间，几十到几百ms的波动代价

#### 方案二：
    throw一个error并自定义成关注前端项目质量时相对没有那么注意的一种异常类别

优点：统一拦截，不会影响首屏时间

缺点：以抛出另一种异常的形式来解决问题并不是一种完美的方式

#### 方案三：
    在业务代码中做空值的判断处理
优点：

缺点：改动量大，不能统一处理

如果是你，你会选择哪一种呢？或者有何更好的办法？欢迎与我联系。


> 参考: [window.location.href跳转页面详细过程是怎么样的？](https://www.zhihu.com/question/29890952)
