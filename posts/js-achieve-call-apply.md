---
title: 'js中call、apply实现'
date: 2019-12-22 20:11:27
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
## 实现call()

- 实现思路

> 1. 改变this指向：可以将目标函数作为这个对象的属性
> 2. 利用arguments类数组对象实现参数不定长
> 3. 不能增加对象的属性，所以在结尾需要delete

```
Function.prototype.myCall = function(context){
    var context = context || window;   
    context.fn = this;
    let args = [...arguments].slice(1);
    context.fn(...args);
    delete context.fn;
    return result;
}
```

## 实现apply()

- 与call（）只有一个区别，apply第二个参数为数组

```
Function.prototype.myApply = function(context){
    var context = context || window;
    contex.fn = this;
    // 需要判断是否存储第二个参数
    // 如果存在，就将第二个参数展开
    if(argument[1]){
        context.fn(...argument[1]);
    }else{
        context.fn();
    }
    delete context.fn;
    return result;
}
```

## 实现bind()

返回一个与被调函数具有相同函数体的新函数，且这个新函数也能使用new操作符。

**实现思路**

> 1. 返回一个新函数，可以使用闭包
> 2. bind（）传入的参数长度不定，使用函数内置的arguments对象数组，可利用Array.prototype.slice.call(arguments,1 )将其转化为数组
> 3. 返回的新函数中，使用apply改变被调用函数的this指向，将arguments转化成的数组作为apply的第二个参数
> 4. 因为返回的新函数也可以使用new操作符，所以在新函数内部需要判断是否使用了new操作符（为什么需要判断，后面会讲解到），如果使用则将apply的第一个参数设置为新创建的对象，如果没有则设置为在调用bind（）时所传入的对象（不传的话默认为window）。

需要注意的是怎么去判断是否使用了new操作符呢？在解决这个问题之前，我们先看使用new操作符时具体干了些什么，下面是new操作符的简单实现过程：



```
//简洁版的new操作符实现过程
 
function newFunc(constructor){
      //第一步：创建一个空对象obj 
        var obj = {};
       //第二步：将构造函数 constructor的原型对象赋给obj的原型
        obj.__proto__ = constructor.prototype;
      //第三步：将构造函数 constructor中的this指向obj,并立即执行构造函数内部的操作
        constructor.apply(obj);
      //第四步：返回这个对象
        return obj;
}
```



