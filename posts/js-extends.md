---
title: 'js如何实现继承'
date: 2020-06-01 21:51:16
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
### 写在前面
继承是OO语言中的一个最为人津津乐道的概念。许多OO语言都支持两种继承方式：接口继承和实现继承。接口继承只继承方法签名，而实现继承则继承实际的方法。如前所述，由于函数没有签名，在ECMAScript中无法实现接口继承。ECMAScript只支持实现继承，而且其实现继承主要是依靠原型链来实现的。

### 1.原型链
在 ECMAScript 中描述了原型链的概念，并将原型链作为实现继承的主要方法。其基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。这一部分另写一篇文章总结，在此不展开了。

缺点：

1. 包含引用类型的原型属性会被所有实例属性共享，容易造成属性的修改混乱。

2. 在创建子类型的实例时，不能向超类型的构造函数中传递参数。

基于以上问题，在实践中很少会单独使用原型链。

### 2.借用构造函数

借用构造函数的思想主要是在子类型的构造函数中调用父类型构造函数。

例：

```js

function SuperType(name){
    this.name = name;
}

function SubType(){
    // 集成了SuperType，同时还传递了参数
    SuperType.call(this,'pxy');
    
    //实例属性
    this.age = 22;
}
var instance = new SubType();
alert(instance.name); // "pxy"
alert(instance.age); // 22

```

优点：可以在子类型构造函数中向超类型构造函数添加参数

缺点：和构造函数模式一样的问题，所有的方法都在构造函数中定义，因此就无法做到函数的复用。而且超类型的原型中定义的方法，对于子类型而言也是不可见的。

基于以上问题，借用构造函数的技术也是很少单独使用的。

### 3.组合继承

```js
function SuperType(name){
    this.name = name
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function(){
    console.log(this.name);
}

function SubType(name, age){
    
    //继承属性
    SuperType.call(this,name);

    this.age = age;
}

//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    console.log(this.age);
}

var instance1 = new SubType("driss",9);
instance1.colors.push("black");
console.log(instance1.colors);  //"red,blue,green,black"
instance1.sayName(); // "driss"
instance1.sayAge(); // 9

var instance2 = new SubType("pan",10);
console.log(instance2.colors);  //"red,blue,green"
instance2.sayName(); // "pan"
instance2.sayAge(); // 10
```

优点：组合继承避免了原型链和借用构造函数的缺陷，融合了它们的优点，成为 JavaScript 中最常用的继承模式。而且，instanceof 和 isPropertyOf() 也能够用于识别基于组合继承创建的对象。

缺点：调用了两次超类的构造函数，导致基类的原型对象中增添了不必要的超类的实例对象中的所有属性。

### 4.原型式继承

原型式继承的主要思路是可以基于已有的对象创建新的对象，同时还不必因此创建自定义类型。

例:


```js
function object(o){
    function F(){};
    F.prototype = o;
    return new F();
}
```

简单来说这个函数的作用就是，传入一个对象，返回一个原型对象为该对象的新对象。

```js
function object(o){
    function F(){};
    F.prototype = o;
    return new F();
}
var person = {
    name: 'pxy',
    friends: ['tmk','cz','cyx']
}

var anotherPerson = object(person);
anotherPerson.name = 'whg';
anotherPerson.friends.push('wj');

var anotherPerson2 = object(person);
anotherPerson2.name = 'zsl';
anotherPerson2.friends.push('wdw');

console.log(person.friends); //[ 'tmk', 'cz', 'pyr', 'wj', 'wdw' ]

```

这种原型式继承，要求你必须有一个对象可以作为另一个对象的基础。如果有这么一个对象的话，可以把它传递给object()函数，然后再根据具体需求对得到的对象加以修改即可。在这个例子中，可以作为另一个对象基础的是person对象，于是我们把它传入到object()函数中，然后该函数就会返回一个新对象。这个新对象将person作为原型，所以它的原型中就包含一个基本类型值属性和一个引用类型值属性。这意味着person.friends不仅属于person所有，而且也会被anotherPerson以及anotherPerson2共享。实际上，这就相当于又创建了person对象的两个副本。

ECMAScript 5中新增了 Object.create() 方法规范了原型式继承。这个方法接收两个参数，一个是将被用作新对象原型的对象，一个是为新对象定义额外属性的对象（可选）。

注意第二个参数的格式与 Object.defineProperties() 方法的第二个参数格式相同。以这种方式指定的任何属性都会覆盖原型对象上的同名属性。在第二个参数为空的情况下，该方法与 object() 方法的行为相同。

在没有必要兴师动众地创建构造函数，而只想让一个对象与另一个对象保持类似的情况下，原型式继承是完全可以胜任的

优点：可以实现基于一个对象的简单继承，不必创建构造函数

缺点：与原型链中提到的缺点相同，一个是传参的问题，一个是属性共享的问题。

### 5. 寄生式继承

寄生式继承的思路与寄生构造函数和工厂模式类似，创建一个仅用于封装继承过程的函数，该函数在内部以某种方式增强对象，最后返回这个对象。

```js
function createAnother(original){
    
    var clone = object(original); //通过调用函数创建一个新对象
    
    clone.sayHi = function(){  // 某种方式增强这个对象
        console.log("hi");
    }

    return clone;  // 返回这个对象
}

var person = {
    name: "james"
}

var anotherPerson = createAnother(person);

anotherPerson.sayHi(); // "hi"

```
这个例子中的代码基于person返回了一个新对象——anotherPerson。新对象不仅具有person的所有属性和方法，而且还有自己的sayHi()方法

优点： 在主要考虑对象而不是自定义类型和构造函数的情况下，实现简单的继承。

缺点：使用该继承方式，在为对象添加函数的时候，没有办法做到函数的复用。

### 6.寄生式组合继承

前面说过，组合继承是JavaScript最常用的继承模式；不过，它也有自己的不足。组合继承最大的问题就是无论什么情况下，都会调用两次超类型构造函数：一次是在创建子类型原型的时候，另一次是在子类型构造函数内部。没错，子类型最终会包含超类型对象的全部实例属性，但我们不得不在调用子类型构造函数时重写这些属性。

寄生式组合继承就是用来解决这个问题，它与组合继承不同的地方主要是，在继承原型时，我们继承的不是超类的实例对象，而是原型对象是超类原型对象的一个实例对象，这样就解决了基类的原型对象中增添了不必要的超类的实例对象中的所有属性的问题。

```js
function inheritPrototype(subType, superType){

    var prototype = object(superType.prototype); // 创建原型对象是超类原型对象的一个实例对象
  
    prototype.constructor = subType; // 弥补因为重写原型而失去的默认的 constructor 属性。
 
    subType.prototype = prototype; // 实现原型继承
}
```

优点：效率高，避免了在 SubType.prototype 上创建不必要的属性。与此同时还能保持原型链不变，开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。

> 参考：
JavaScript高级程序设计（第三版）