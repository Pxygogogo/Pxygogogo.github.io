---
title: 'js中call、apply、bind理解'
date: 2019-12-22 10:37:05
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
在JavaScript中，call、apply和bind是Function对象自带的三个方法，这三个函数的存在意义是什么？答案是**改变函数执行时的上下文，再具体一点就是改变函数运行时的this指向**

## 一、方法定义

### call()

- 语法
```
fun.call(thisArg[, arg1[, arg2[, ...]]])
```
- 参数

**thisArg：fun函数运行时指定的this值，可能的值为：**

> - 不传，或者传null，undefined，this指向Window/Global对象
> - 传递另一个函数的函数名fun2，this指向函数fun2的引用
> - 值为原始值(数字，字符串，布尔值),this会指向该原始值的自动包装对象，如 String、Number、Boolean
> - 传递一个对象，函数中的this指向这个对象

**arg1, arg2, ...**

指定的参数列表。

### apply()

- 语法：
```
fun.apply([thisArg[,argArray]])
```
- 参数

**thisArg**

同上call 的thisArg参数。

**argArray** 

一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 fun 函数。如果该参数的值为null 或 undefined，则表示不需要传入任何参数。从ECMAScript 5 开始可以使用类数组对象。

语法与 call() 方法的语法几乎完全相同，唯一的区别在于，apply的第二个参数必须是一个包含多个参数的数组（或类数组对象）。apply的这个特性很重要，
在调用一个存在的函数时，你可以为其指定一个 this 对象。 this 指当前对象，也就是正在调用这个函数的对象。 使用 apply， 你可以只写一次这个方法然后在另一个对象中继承它，而不用在新对象中重复写该方法。

### bind()

bind() 函数会创建一个新函数（称为绑定函数）

> - bind是ES5新增的一个方法
> - 传参和call或apply类似
> - 不会执行对应的函数，call或apply会自动执行对应的函数
> - 返回对函数的引用


- 语法 

```
fun.bind(thisArg[, arg1[, arg2[, ...]]])
```

- 参数

**thisArg**


bind的thisArg参数也和call方法一样，thisArg如果未传，那么 Global/Window对象被用作 thisObj。

**arg1 … argN**

可传可不传。如果不传，可以在调用的时候再传。如果传了，调用的时候则可以不传，调用的时候如果你还是传了，则不生效

## 二、call、apply、bind的区别

- call的arg传参需一个一个传，apply则直接传一个数组


```
function hello(name,age){
 console.log(name);
  console.log(age);
  }
hello.call(this,"tsrot",24);
hello.apply(this,["tsrot",24]);
```


- call和apply直接执行函数，而bind需要再一次调用

```
var obj = {
    x: 81,
    };
var foo = {
    getX: function() {
        return this.x;
    }
    }
console.log(foo.getX.bind(obj)());  
console.log(foo.getX.call(obj));    
console.log(foo.getX.apply(obj));
```
## 三、运用场景

1. 实现继承

```
function Animal(name) {
    this.name = name;
    this.showName=function () {
        console.log(this.name)
    }
}

function Cat(name) {
    Animal.call(this,name);
}
var cat = new Cat('Black Cat');
cat.showName();


```

2. 数组追加

```
let arr1=[1,2,3];
let arr2= [4,5,6];
Array.prototype.push.apply(arr1,arr2); //或者[].push.apply(arr1, arr2);
console.log(arr1)
```

3. 获取数组中的最大值和最小值

```
let num = [1,3,5,7,2,-10,11];
let maxNum = Math.max.apply(Math, num);
let minNum = Math.min.apply(Math, num);
console.log(maxNum); 
console.log(minNum);

```

4. 将伪数组转化为数组

```
let fakeArr = {0:'a',1:'b',length:2};
let arr1 = Array.prototype.slice.call(fakeArr);
console.log(arr1[0]);
let arr2 = [].slice.call(fakeArr);
console.log(arr2[0]);
arr1.push("c");
console.log(arr1);
```

