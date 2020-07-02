---
title: '原型和原型链'
date: 2020-05-31 14:15:18
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
js分为函数对象和普通对象，每个对象都有__proto__属性(null除外)，但是只有函数对象才有prototype属性。


## 什么是原型

每一个JavaScript对象(null除外)在创建的时候就会与之关联另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型"继承"属性。


## prototype
每个函数都有一个 prototype 属性
```js
function Person() {

}
// 虽然写在注释里，但是你要注意：
// prototype是函数才会有的属性
Person.prototype.name = 'Kevin';
var person1 = new Person();
var person2 = new Person();
console.log(person1.name) // Kevin
console.log(person2.name) // Kevin
```

函数的 prototype 属性指向了一个对象，这个对象正是调用该构造函数而创建的实例的原型，也就是这个例子中的 person1 和 person2 的原型。

## __ proto __

这是每一个JavaScript对象(除了 null )都具有的一个属性，叫__proto__，这个属性会指向该对象的原型。
```js
function Person() {

}
var person = new Person();
console.log(person.__proto__ === Person.prototype); // true
```


## constructor

constructor，每个原型都有一个 constructor 属性指向关联的构造函数。
```js
function Person() {

}
console.log(Person === Person.prototype.constructor); // true

```

## 实例与原型

当读取实例的属性时，如果找不到，就会查找与对象关联的原型中的属性，如果还查不到，就去找原型的原型，一直找到最顶层为止。

例：
```js
function Person() {

}

Person.prototype.name = 'Kevin';

var person = new Person();

person.name = 'Daisy';
console.log(person.name) // Daisy

delete person.name;
console.log(person.name) // Kevin
```
在这个例子中，我们给实例对象 person 添加了 name 属性，当我们打印 person.name 的时候，结果自然为 Daisy。

但是当我们删除了 person 的 name 属性时，读取 person.name，从 person 对象中找不到 name 属性就会从 person 的原型也就是 person.__proto__ ，也就是 Person.prototype中查找，幸运的是我们找到了 name 属性，结果为 Kevin。

但是万一还没有找到呢？原型的原型又是什么呢？

## 原型的原型

在前面，我们已经讲了原型也是一个对象，既然是对象，我们就可以用最原始的方式创建它，那就是：


```js
var obj = new Object();
obj.name = 'Kevin'
console.log(obj.name) // Kevin
```

构造函数-实例-原型-原型的原型关系图如下：

![原型关系图](https://github.com/mqyqingfeng/Blog/raw/master/Images/prototype4.png)

## 原型链

那 Object.prototype 的原型呢？

null，我们可以打印：
```js
console.log(Object.prototype.__proto__ === null) // true
```
然而 null 究竟代表了什么呢？

引用阮一峰老师的 《undefined与null的区别》 就是：

> null 表示“没有对象”，即该处不应该有值。

所以 Object.prototype.__proto__ 的值为 null 跟 Object.prototype 没有原型，其实表达了一个意思。

所以查找属性的时候查到 Object.prototype 就可以停止查找了。

图中由相互关联的原型组成的链状结构就是原型链，也就是蓝色的这条线


![原型链](https://github.com/mqyqingfeng/Blog/raw/master/Images/prototype5.png)


## 总结

在 js 中我们是使用构造函数来新建一个对象的，每一个构造函数的内部都有一个 prototype 属性值，这个属性值是一个对
象，这个对象包含了可以由该构造函数的所有实例共享的属性和方法。当我们使用构造函数新建一个对象后，在这个对象的内部
将包含一个指针，这个指针指向构造函数的 prototype 属性对应的值，在 ES5 中这个指针被称为对象的原型。一般来说我们
是不应该能够获取到这个值的，但是现在浏览器中都实现了__proto__ 属性来让我们访问这个属性，但是我们最好不要使用这
个属性，因为它不是规范中规定的。ES5 中新增了一个 Object.getPrototypeOf() 方法，我们可以通过这个方法来获取对
象的原型。

当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又
会有自己的原型，于是就这样一直找下去，也就是原型链的概念。原型链的尽头一般来说都是 Object.prototype 所以这就
是我们新建的对象为什么能够使用 toString() 等方法的原因。

特点：

JavaScript 对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与
之相关的对象也会继承这一改变。

>参考：
>
>[ JavaScript深入之从原型到原型链 ](https://github.com/mqyqingfeng/Blog/issues/2)
>
>[「前端料包」深入理解JavaScript原型和原型链](https://juejin.im/post/5e860b5e518825738d526a22)
>

