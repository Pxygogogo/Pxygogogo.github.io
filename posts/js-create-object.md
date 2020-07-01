---
title: 'js如何创建对象'
date: 2020-06-01 21:50:10
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
最近在学习红宝书的对象一章，红宝书中一共提到了7种创建对象的方式（这里所说的对象更偏向于面向对象编程中的对象）。7种方式分别是：

- 工厂模式
- 构造函数模式
- 原型模式
- 构造函数和原型组合模式
- 动态原型模式
- 寄生构造模式
- 稳妥构造模式


### 1.工厂模式

工厂模式是软件工程领域一种广为人知的设计模式，这种模式抽离了创建对象的具体过程。es6的class关键字出现之前，开发人员发明以一种函数，用函数来封装以特定接口创建对象的细节。

例：


```js
function Person(name, age){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.greet = function(){
        alert(`Hi, my name is ${this.name}`);
    };
    return o;
}

var person1 = Person("p1"，21);

var person2 = Person("p2"，22);
```

优点：解决了创建多个相似对象时，代码的复用问题

缺点：
1. 无法知道一个对象的具体类型是什么,即无法通过constructor识别对象，以为都是来自Object，无法得知来自Person
2. 每次通过createPerson创建对象的时候，所有的greet方法都是一样的，但是却存储了多次。

### 2.构造函数模式

创建自定义的构造函数，从而定义对象类型的属性和方法。

例：


```js
function createPerson(name, age){
    this.name = name;
    this.age = age;
    this.greet = function(){
        alert(`Hi, my name is ${this.name}`);
    };
    return o;
}

var person1 = createPerson("p1"，21);

var person2 = createPerson("p2"，22);
```

使用构造函数实例化一个对象的时候，对象中会包含一个 __proto__ 属性指向构造函数原型对象，而原型对象中则包含一个 constructor 属性指向构造函数。因此在实例对象中我们可以通过原型链来访问到 constructor 属性，从而判断对象的类型。

优点：
1. 通过constructor或者instanceof可以识别对象实例的类别  
2. 可以通过new 关键字来创建对象实例，更像传统的OO语言中创建对象实例的方式

缺点： 
1. js 中的函数是对象，在使用构造函数创建对象时，每个方法都会在实例对象中重新创建一遍。拿上面的例子举例，这意味着每创建一个对象，就会创建一个 greet 函数的实例，但它们其实做的都是同样的工作，因此这样便会造成内存的浪费。

注意：

1. 构造函数模式隐试的在最后返回return this 所以在缺少new的情况下，会将属性和方法添加给全局对象，浏览器端就会添加给window对象。
2. 也可以根据return this 的特性调用call或者apply指定this。这一点在后面的继承有很大帮助。

### 3.原型模式

我们知道，我们创建的每一个函数都有一个 prototype 属性，这个属性指向函数的原型对象，这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。我们通过使用原型对象可以让所有的对象实例共享它所包含的属性和方法，因此这样也解决了代码的复用问题。

例：

```js
function Person(){

}

Person.prototype.name = "person";
Person.prototype.age = 21;
Person.prototype.greet = function(){
    alert(`Hi, my name is ${this.name}`);
};

var person1 = new Person();
person1.greet(); // "Hi, my name is person"

var person2 = new Person();
person2.greet(); // "Hi, my name is person"


console.log(person1.greet === person2.greet) // true
```
与构造函数模式不同的是，原型对象上的属性和方法是有所有实例所共享的。也就是说，上面 person1 和 person2 访问的都是同一组属性和同一个 greet() 函数。

优点：
1. greet方法是共享的，所有的实例的greet方法都指向同一个。
2. 可以动态的添加原型对象的方法和属性，并直接反映在对象实例上

缺点：
1. 首先第一个问题是原型模式省略了构造函数模式传递初始化参数的过程，所有的实例在默认情况下都会取得默认的属性值。
2. 因为所有的实例都是共享一组属性，对于包含基本值的属性来说没有问题，但是对于包含引用类型的值来说（例如数组对象），所有的实例都是对同一个引用类型进行操作，那么属性的操作就不是独立的，最后导致读写的混乱。我们创建的实例一般都是要有属于自己的全部属性的，因此单独使用原型模式的情况是很少存在的。
3. 第一次调用greet方法或者name属性的时候会搜索两次，第一次是在实例上寻找greet方法，没有找到就去原型对象(Person.prototype)上找say方法，找到后就会在实例上添加这些方法或者属性。

注意：

优点2中存在一个问题就是直接通过对象字面量给Person.prototype进行赋值的时候会导致constructor改变，所以需要手动设置，其次就是通过对象字面量给Person.prototype进行赋值，会无法作用在之前创建的对象实例上


```
var person1 = new Person()
Person.prototype = {
	name: 'changed person',
	age: 22,
  	greet: function(name){
      console.log('changed');
  	}
}

```

这是因为对象实例和对象原型直接是通过一个指针链接的，这个指针是一个内部属性[[Prototype]]，可以通过__proto__访问。我们通过对象字面量修改了Person.prototype指向的地址，然而对象实例的__proto__，并没有跟着一起更新，所以这就导致，实例还访问着原来的Person.prototype，所以建议不要通过这种方式去改变Person.prototype属性


### 4.组合使用构造函数模式和原型模式

创建自定义类型的最常见方式，就是组合使用构造函数模式和原型模式。==构造函数模式用于定义实例属性==，==而原型模式用于定义方法和共享的属性==。使用这种模式的好处就是，每个实例都会拥有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存。而且这中混成模式还支持向构造函数传递参数，可以说是及两种模式之长。

例：


```js
function Person(name, age){
    this.name = name;
    this.age = age;
    this.country = 'China';
}

Person.prototype.greet = function () {
    alert(`Hi, my name is ${this.name}`);
}

var person1 = new Person('p1', 21);
person1.greet(); // "Hi, my name is p1"

var person2 = new Person('p2', 22);
person2.greet(); // "Hi, my name is p2"

console.log(person1.greet === person2.greet) // true
```

优点：

1. 解决了原型模式对于引用对象的缺点
2. 解决了原型模式没有办法传递参数的缺点
3. 解决了构造函数模式不能共享方法的缺点

缺点：
1. 和原型模式中注意1一样
2. 由于使用了两种模式，因此对于代码的封装性来说不是很好。


### 5.动态原型模式

由于上面混成模式存在封装性的问题，动态原型模式是解决这个问题的一个方案。这个方法把所有信息都封装到了构造函数中，而在构造函数中通过判断只初始化一次原型。

例：

```js
function Person(name, age){
    this.name = name;
    this.age = age;
    this.country = 'China';
    if(typeof this.greet !== "function" ){
        Person.prototype.greet = function(){
            alert(`Hi, my name is ${this.name}`);
        } 
    } 
}

var person1 = new Person("p1", 21);

person1.greet(); // "Hi, my name is p1"
```
优点：

1. 可以在初次调用构造函数的时候就完成原型对象的修改
2. 修改能体现在所有的实例中
3. 解决了组合模式中封装性的问题

缺点：
1. 红宝书都说这个方案完美了。。。。

注意：
1. 只用检查一个在执行后应该存在的方法或者属性就行了
2. 不能用对象字面量修改原型对象



### 6.寄生构造模式
如果在前面几种模式不适用的情况下，可以使用寄生构造函数模式。这种模式的基本思想是创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后返回新创建的对象。

例：
```js
function Person(name, age){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.greet = function(){
        alert(`Hi, my name is ${this.name}`);
    };
    return o;
}

var person1 = new Person("p1"，21);

var person2 = new Person("p2"，22);
```

优点：

1. 和工厂模式基本一样，除了多了个new操作符

缺点：

1. 和工厂模式一样，不能区分实例的类别


### 7.稳妥构造模式

Douglas Crockford 发明了 JavaScript 中的稳妥对象这个概念。所谓稳妥对象，指的就是，没有公共属性，而且其方法也不使用 this 的对象。稳妥对象最适合在一些安全的环境中（这些环境中会禁止使用 this 和 new），或者在防止数据被其他应用程序改动时使用。

稳妥构造函数遵循与寄生构造函数类似的模式，但有两点不同：

一是新创建的对象的实例方法不引用 this ；

二是不使用 new 操作符调用构造函数。

例：


```js
function Person(name, age){

    //创建要返回的对象
    var o = new Object();

    //可以在这里定义私有变量和函数

    //添加方法
    o.greet = function(){
        alert(`Hi, my name is ${this.name}`);
    }

    //返回对象
    return o;
} 

var person1 =  Person('safe name');

person1.greet(); // "Hi, my name is safe name"
```

优点：
1. 以上面为例，除了 greet 方法外，没有别的方法可以访问数据成员，这就是稳妥构造函数提供的安全性。

缺点：
2. 和寄生构造函数一样，没有办法使用 instanceof 操作符来判断对象的类型

> 参考：
JavaScript高级程序设计（第三版）