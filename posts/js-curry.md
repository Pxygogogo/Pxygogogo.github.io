---
title: '函数柯里化'
date: 2020-03-14 21:01:13
tags: [js]
published: true
hideInList: true
feature: 
isTop: false
---
## 函数柯里化
**柯里化（Currying）,维基百科上的解释是，把接受多个参数的函数转换成接受一个单一参数的函数**

先看一个简单的例子：
```
    // 柯里化
    var foo = function(x) {
        return function(y) {
            return x + y
        }
    }
    
    foo(3)(4)       // 7
    
    // 普通方法
    var add = function(x, y) {
        return x + y;
    }
    
    add(3, 4)       //7

```

本来应该一次传入两个参数的add函数，柯里化方法，变成每次调用都只用传入一个参数，调用两次后，得到最后的结果。

再看看，一道经典的面试题。

```
编写一个sum函数，实现如下功能：
console.log(sum(1)(2)(3)) // 6.
```

直接套用上面柯里化函数，多加一层return

```
function sum(a) {
        return function(b) {
            return function(c) {
                return a + b + c;
            }
        }
    }
```

    
当然，柯里化不是为了解决面试题，它是应函数式编程而生，
 
 
 ## 如何实现
 还是看看上面的经典面试题。
如果想实现 ==sum(1)(2)(3)(4)(5)...(n)== 就得嵌套 ==n-1==个匿名函数，

看起来并不优雅，如果我们预先知道有多少个参数要传入，可以利用递归方法解决

伪代码：

```
var curry = function(fn) {
    return function() {
        // 假设柯里化的函数叫 curry_fn
        // if "curry_fn接受到的参数数量等于fn接受参数的数量"
        // return "fn的执行结果"
        // else return "一个柯里化函数"
    };
};
```


- 递归出口：curry_fn接受到的参数数量等于fn接受参数的数量
- 重复逻辑：return "一个柯里化函数"

```
var add = function(num1, num2) {
        return num1 + num2;
    }
// 假设 sum 函数调用时，传入参数都是标准的数字
    function curry(add, n) {
       var count = 0, //接受的参数个数
           arr = []; // 存储递归过程的所有参数，用于递归出口计算值
           
       return function reply(arg) {
           arr.push(arg); // 收集递归参数
           
           if ( ++count >= n) {
               //这里也可以在外面定义变量，保存每次计算后结果
               return arr.reduce(function(p, c) {
               // 返回函数执行结果
                   return p = add(p, c);
               }, 0) 
           } else {
           // 返回一个柯里化函数
               return reply;
           }
       }
    }
    var sum = curry(add, 4);
    
    sum(4)(3)(2)(1)  // 10    
    
```

根据上述的例子，可以得到，柯里化后的函数如果接受到全部参数则返回函数执行结果，否则返回一个柯里化函数。


这里还有一个小问题，如果调用次数多于约定数量，sum 就会报错，我们就可以设计成类似这样

```
sum(1)(2)(3)(4)(); // 最后传入空参数，标识调用结束，
```

只需要简单修改下curry 函数

```
function curry(add) {
       var arr = [];
       
       return function reply() {
         var arg = Array.prototype.slice.call(arguments);
         arr = arr.concat(arg);
         
          if (arg.length === 0) { // 递归结束条件，修改为 传入空参数
              return arr.reduce(function(p, c) {
                  return p = add(p, c);
              }, 0)
          } else {
              return reply;
          }
      }
    }
  
  console.log(sum(4)(3)(2)(1)(5)())   // 15
  ```
  
## 简洁版实现

上面针对具体问题，引入柯里化方法解答，回到如何实现创建柯里化函数的通用方法。
同样先看简单版本的方法，以add方法为例，代码来自《JavaScript高级程序设计》


```
function curry(fn) {
    var args = Array.prototype.slice.call(arguments, 1);
    return function() {
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return fn.apply(null, finalArgs);
    };
}

function add(num1, num2) {
    return num1 + num2;
}
var curriedAdd = curry(add, 5);

var curriedAdd2 = curry(add, 5, 12);

alert(curriedAdd(3))    // 8
alert(curriedAdd2())    // 17
```

## 加强版实现

上面add函数，可以换成任何其他函数，经过curry函数处理，都可以转成柯里化函数。
这里在调用curry初始化时，就传入了一个参数，而且返回的函数 curriedAdd , curriedAdd2也没有被柯里化。要想实现更加通用的方法，在柯里化函数真正调用时，再传参数，

```
function curry(fn) {
     ...
 }

function add(num1, num2) {
    return num1 + num2;
}

var curriedAdd = curry(add);

curriedAdd(3)(4) // 7
```
每次调用curry返回的函数，也被柯里化，可以继续传入一个或多个参数进行调用，

跟上面sum(1)(2)(3)(4) 非常类似,利用递归就可以实现。 关键是递归的出口，这里不能是传入一个空参数的调用， 而是原函数定义时，参数的总个数，柯里化函数调用时，满足了原函数的总个数，就返回计算结果，否则，继续返回柯里化函数。

原函数的入参总个数，可以利用length 属性获得


```
function add(num1, num2) {
    return num1 + num2;
}

add.length // 
```

结合上面的代码，


```
var curry = function(f) {
      var len = f.length;
      
        return function t() {
          var innerLength = arguments.length,
            args = Array.prototype.slice.call(arguments);
            
          if (innerLength >= len) {   // 递归出口，f.length
             return f.apply(undefined, args)
          } else {
            return function() {
              var innerArgs = Array.prototype.slice.call(arguments),
                allArgs = args.concat(innerArgs);
                
              return t.apply(undefined, allArgs)
            }
          }
        }
    }
    
   // 测试一下
  function add(num1, num2) {
    return num1 + num2;
  }

   var curriedAdd = curry(add);
   add(2)(3);     //5

  // 一个参数
  function identity(value) {
     return value;
 }

   var curriedIdentify = curry(identify);
   curriedIdentify(4) // 4
```

到此，柯里化通用函数可以满足大部分需求了。

**在使用 apply 递归调用的时候，默认传入 undefined, 在其它场景下，可能需要传入 context， 绑定指定环境**

实际开发，推荐使用 [lodash.curry](https://lodash.com/docs/4.17.15#curry) , 具体实现，可以参考下[curry源码](https://github.com/lodash/lodash/blob/4.3.0-npm-packages/lodash.curry/index.js)

## 使用场景

讲了这么多curry函数的不同实现方法，那么实现了通用方法后，在那些场景下可以使用，或者说使用柯里化函数是否可以真实的提高代码质量，下面总结一下使用场景

1. 参数复用

```
// 正常正则验证字符串 reg.test(txt)

// 函数封装后
function check(reg, txt) {
    return reg.test(txt)
}

check(/\d+/g, 'test')       //false
check(/[a-z]+/g, 'test')    //true

// Currying后
function curryingCheck(reg) {
    return function(txt) {
        return reg.test(txt)
    }
}

var hasNumber = curryingCheck(/\d+/g)
var hasLetter = curryingCheck(/[a-z]+/g)

hasNumber('test1')      // true
hasNumber('testtest')   // false
hasLetter('21212')      // false
```
上面的示例是一个正则的校验，正常来说直接调用check函数就可以了，但是如果我有很多地方都要校验是否有数字，其实就是需要将第一个参数reg进行复用，这样别的地方就能够直接调用hasNumber，hasLetter等函数，让参数能够复用，调用起来也更方便。


2. 提前确认


```
var on = function(element, event, handler) {
    if (document.addEventListener) {
        if (element && event && handler) {
            element.addEventListener(event, handler, false);
        }
    } else {
        if (element && event && handler) {
            element.attachEvent('on' + event, handler);
        }
    }
}

var on = (function() {
    if (document.addEventListener) {
        return function(element, event, handler) {
            if (element && event && handler) {
                element.addEventListener(event, handler, false);
            }
        };
    } else {
        return function(element, event, handler) {
            if (element && event && handler) {
                element.attachEvent('on' + event, handler);
            }
        };
    }
})();

//换一种写法可能比较好理解一点，上面就是把isSupport这个参数给先确定下来了
var on = function(isSupport, element, event, handler) {
    isSupport = isSupport || document.addEventListener;
    if (isSupport) {
        return element.addEventListener(event, handler, false);
    } else {
        return element.attachEvent('on' + event, handler);
    }
}
```

我们在做项目的过程中，封装一些dom操作可以说再常见不过，上面第一种写法也是比较常见，但是我们看看第二种写法，它相对一第一种写法就是自执行然后返回一个新的函数，这样其实就是提前确定了会走哪一个方法，避免每次都进行判断。

3. 延迟运行


```
Function.prototype.bind = function (context) {
    var _this = this
    var args = Array.prototype.slice.call(arguments, 1)
 
    return function() {
        return _this.apply(context, args)
    }
}
```
像我们js中经常使用的bind，实现的机制就是Currying.

4. 函数式编程中，作为compose, functor, monad 等实现的基础

有人说柯里化是应函数式编程而生，它在里面出现的概率就非常大了，在JS 函数式编程指南中，开篇就介绍了柯里化的重要性。

## 柯里化的性能
从上面实现部分的代码中，可以看到，使用柯里化函数，离不开闭包， arguments， 递归。

- 闭包，函数中的变量都保存在内存中，内存消耗大，有可能导致内存泄漏。
- 递归，效率非常差，
- arguments, 变量存取慢，访问性很差,存取arguments对象通常要比存取命名参数要慢一点
- 使用fn.apply( … ) 和 fn.call( … )通常比直接调用fn( … ) 稍微慢点


## 参考资料：
- https://www.jianshu.com/p/2975c25e4d71
- https://segmentfault.com/a/1190000018265172
- https://segmentfault.com/a/1190000018203637