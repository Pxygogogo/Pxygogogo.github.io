---
title: 'js for in 和 for of 区别及遍历相关注意点'
date: 2020-01-02 11:02:41
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
- 例一

```
const obj = {
        a: 1,
        b: 2,
        c: 3
    }
    for (let i in obj) {
        console.log(i)
        // a
        // b
        // c
    }
    for (let i of obj) {
        console.log(i)
        // Uncaught TypeError: obj is not iterable 报错了
    }
```
以上代码通过 for in 和 for of 对一个obj对象进行遍历,for in 正常的获取了对象的 key值,分别打印 a、b、c,而 for of却报错了。

- 例二

以上是遍历对象,下面再看一个遍历数组的例子。

```
const arr = ['a', 'b', 'c']
    // for in 循环
    for (let i in arr) {
        console.log(i)
        // 0
        // 1
        // 2
    }
    
    // for of
    for (let i of arr) {
        console.log(i)
        // a
        // b
        // c
    }
```
以上代码是对一个数组进行遍历, for in 返回的值为 0、1、2,返回的是数组的下标，而 for of 返回的是 a、b、c,返回的是数组的值

结合上面两个例子，分析得出
## for in 特点
- for ... in 循环返回的值都是数据结构的 键值名。
遍历对象返回的对象的key值,遍历数组返回的数组的下标(key)。

- for ... in 循环不仅可以遍历数字键名,还会遍历原型上的值和手动添加的其他键。

- 特别情况下, for ... in 循环会以任意的顺序遍历键名

> 注：使用for in 遍历对象是无法直接获取属性值得，因为他实际上遍历的是对象中的可枚举属性，你需要手动获取属性值
> 
> 遍历数组下标时采用的是数组顺序（for循环或者其他迭代器），但是遍历对象属性时的顺序是不确定的，在不同的JavaScript引擎中可能不一样。因此在不同的环境中需要保证一致性时，不用相信任何观察到的顺序，它们是不可靠的
>
> （引用自你不知道的JavaScript上卷）

## for of 特点

- for of 循环用来获取一对键值对中的值,而 for in 获取的是 键名

- 一个数据结构只要部署了 Symbol.iterator 属性, 就被视为具有 iterator接口, 就可以使用 for of循环。

- 例1这个对象,没有 Symbol.iterator这个属性,所以使用 for of会报 obj is not iterable

- for of 不同与 forEach, 它可以与 break、continue和return 配合使用,也就是说 for of 循环可以随时退出循环。

- 提供了遍历所有数据结构的统一接口

> for of 循环首先会向被访问对象请求一个迭代器对象，然后通过调用迭代器对象的next()方法来手动遍历数组，看看它是怎么工作的

```
var arr = [1,2,3];
var it = arr[Symbpl.iterator]();

it.next(); // {value:1,done:false}
it.next(); // {value:2,done:false}
it.next(); // {value:3,done:false}
it.next(); // {done:true}
```

## 哪些数据结构部署了 Symbol.iteratoer属性了呢?
只要有 iterator 接口的数据结构,都可以使用 for of循环。

- 数组 Array
- Map
- Set
- String
- arguments对象
- Nodelist对象, 就是获取的dom列表集合
以上这些都可以直接使用 for of 循环。 凡是部署了 iterator 接口的数据结构也都可以使用数组的 扩展运算符(...)、和解构赋值等操作。

我也想让对象可以使用 for of循环怎么办?使用 Object.keys() 获取对象的 key值集合后,再使用 for of

以例1为例
```
 const obj = {
        a: 1,
        b: 2,
        c: 3
    }

    for (let i of Object.keys(obj)) {
        console.log(i)
        // 1
        // 2
        // 3
    }
```

也可以给一个对象部署 Symbol.iterator属性。

- 当你为对象添加myObject.toString()方法后，就可以将对象转化为字符串，同样地，当你向任意对象添加myObjectSymbol.iterator方法，就可以遍历这个对象了。

举个例子，假设你正在使用jQuery，尽管你非常钟情于里面的.each()方法，但你还是想让jQuery对象也支持for-of循环，你可以这样做：

```
jQuery.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];
```

所有拥有Symbol.iterator的对象被称为可迭代的。在接下来的文章中你会发现，可迭代对象的概念几乎贯穿于整门语言之中，不仅是for-of循环，还有Map和Set构造函数、解构赋值，以及新的展开操作符。


- for...of的步骤
for-of循环首先调用集合的Symbol.iterator方法，紧接着返回一个新的迭代器对象。迭代器对象可以是任意具有.next()方法的对象；for-of循环将重复调用这个方法，每次循环调用一次。

> 和组数不同，普通的对象没有内置的@@iterator，所以无法自动完成for of遍历。之所以要这样做，有许多非常复杂的原因，不过简单来说，这样做是为了避免影响未来的对象类型
> 当然，你可以给任何想遍历的对象定义@@iterator，举例来说：

```
var myObject = {
        a: { x: 2 },
        b: 3
    };
    Object.defineProperty(myObject, Symbol.iterator, {
        enumerable: false,
        writable: false,
        configurable: true,
        value: function () {
            var o = this;
            var idx = 0;
            var ks = Object.keys(o);
            return {
                next: function () {
                    return {
                        value: o[ks[idx++]],
                        done: (idx > ks.length)
                    };
                }
            };
        }
    });

    // 手动遍历myObject
    var it = myObject[Symbol.iterator]();
    console.log(it.next()); //{value:2,done:false}  
    console.log(it.next()); //{value:e,done:false}
    console.log(it.next()); //{value:undefined,done:true}

    //用 for of 遍历myObject
    for (var v of myObject) {
        console.log(v);
    }
    // 2
    // 3
```

参考文章：

> 链接：
> 你不知道的JavaScript上卷3.4节
> https://www.jianshu.com/p/c43f418d6bf0
> https://www.cnblogs.com/owenzh/p/11058708.html
