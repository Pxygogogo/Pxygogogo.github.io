---
title: 'this绑定'
date: 2019-12-21 14:05:50
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
## this的绑定规则主要有以下五种

- 1、默认绑定（严格/非严格模式）
- 2、隐式绑定    
- 3、显式绑定
- 4、new绑定
- 5、ES6箭头函数绑定

### 1、默认绑定
#### 1.1 严格模式
在严格模式下，不能将全局对象window作为默认绑定，此时this会绑定到undefined，但是在严格模式下调用函数则不会影响默认绑定。

```
(() => {
  "use strict"
  function foo() {
    console.log(this.name);
  };
  var name = "pxy";
  foo(); 
})();

// Uncaught TypeError: Cannot read property 'name' of undefined at foo
```

```
var name = 'pxy';
function foo() {
  console.log(this.name);
};

(() => {
  "use strict"
  foo();
})();

// pxy

```
#### 1.2 非严格模式
在非严格模式下，此时this就指向全局对象

var name = 'pxy';
function foo() {
  console.log(this.name);
}

foo(); // pxy

### 2、默认绑定
当函数作为对象的属性存在，通过对象属性执行函数时，此时隐式绑定规则会将this绑定到对象上；

```
var name = 'pxy';
function foo() {
  console.log(this.name);
}

var obj = {
  name: 'zl',
  foo,
}

obj.foo(); // zl

```
注意上面代码中函数执行方式是通过对象属性执行

```
var name = 'pxy';
function foo() {
  console.log(this.name);
}

var obj = {
  name: 'zl',
  foo,
}

foo(); // pxy

var foo1 = obj.foo; 

foo1(); // pxy

obj.foo(); // zl
```

由上面代码可以发现，通过赋值操作后执行函数，会应用默认绑定，此时在非严格模式下this会指向全局对象。

同样的，函数传参也是一种隐式赋值，此时在回调函数中会丢失this绑定。

```
function foo() {
  console.log(this.name);
}

function Foo(fn) {
  fn();
}

var obj = {
  name: 'zl',
  foo,
}

var name = 'pxy';

Foo(obj.foo); // pxy
```

### 3、显示绑定
通过 call apply bind绑定，主要以call举例

> 一句话介绍call：使用一个指定的this和若干个指定的参数调用某个函数或方法。

call做的事:
- 将函数设为对象的属性
- 指定函数的this，并进行传参
- 执行&删除函数
- 判定如果没有指定要绑定的this，非严格模式下默认指向全局对象

可以看到值调用call方法后，会将this绑定到指定对象，所以称为显示绑定

```
function foo() {
  console.log(this.name);
}

var obj = {
  name: 'pxy',
}

var obj1 = {
  name: 'pxy1'
}

var name = 'zl';

foo.call(obj); // pxy  调用call方法后强行将foo函数的this指向来obj对象上

foo.call(obj).call(obj1); // pxy  多次调用call方法，以第一次为准

foo.call(obj1).call(obj); // pxy1

foo.call();// zl 没有传入指定对象，所以this默认指向全局对象
```
> 如果call、apple、bind的绑定对象是null或者undefined，那么实际上在调用时这些值都会被忽略，所以使用的是默认绑定规则

### 4、通过new绑定
构造函数在使用new后，执行的操作：

- 它创建（构造）了一个全新的对象
- 它会被执行[[Prototype]]（也就是proto）链接
- 它使this指向新创建的对象
- 通过new创建的每个对象将最终被[[Prototype]]链接到这个函数的prototype对象上
- 如果函数没有返回对象类型Object(包含Functoin, Array, Date, RegExg, Error)，那么new表达式中的函数调用将返回该对象引用

所以在使用new调用构造函数后，会构造一个新对象并将函数调用中的this绑定到新对象上。


```
var name = 'zl';

function foo(name) {
  this.name = name;
}

var bar = {
  name: 'object',
  foo1: new foo('pxy'),
}

console.log(bar.foo1.name); // pxy
```
构造函数是存在返回值的，可以将函数的返回值分成三种情况：

- 返回一个对象
- 没有返回值，即默认返回undefined
- 返回基本数据类型

```
1、返回一个对象
function Foo(name,age) {
  this.name = name;
  return {
    age
  }
}

var bar = new Foo("pxy","18");

bar.name; // undefined

bar.age; // 18

2、没有返回值

function Foo(name,age) {
  this.name = name;
}

var bar = new Foo("pxy","18");

bar.name; // pxy

bar.age; // undefined

3、返回基本数据类型

function Foo(name,age) {
  this.name = name;
  return 123
}

var bar = new Foo("pxy","18");

bar.name; // pxy

bar.age; // undefined
```
所以使用new绑定时，需要判断函数返回的值是否为一个对象，如果是对象，那么this会绑定到返回的对象上。

## 5、ES6箭头函数绑定

ES6新增了一种函数类型：箭头函数，箭头函数调用时无法使用上面四种规则了，它和普通函数最不同的一点就是对于箭头函数的this指向，是根据它外层（函数/全局）作用域来决定。


```
function foo() {
  return (name) => {
    console.log(this.name);
  }
}

var obj = {
  name: 'pxy'
}

var obj1 = {
  name: 'text'
}

var name = 'zl';

var foo1 = foo();
foo1(); // zl

var foo2 = foo.call(obj);
foo2(); // pxy

foo2.call(obj1); // pxy 可以看到，箭头函数的`this`绑定后无法被修改
```
    
## 6、规则优先级


```
1、new绑定
var obj = new Foo();
this绑定新的对象上

2、显示绑定
var obj = foo.call(bar);
this绑定到指定对象上，若指定对象为null/undefined或着没传，则使用默认绑定规则

3、隐式绑定
var obj = bar.foo();
this绑定到调用方法的对象上

4、默认绑定
foo();
this在严格模式下绑定到undefined
在非严格模式下绑定到全局对象
```


