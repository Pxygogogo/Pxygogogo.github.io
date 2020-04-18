---
title: 'typeof 和 instance'
date: 2020-03-28 14:40:13
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
## typeof

### typeof 对于基本类型，除了 null都可以显示正确的类型


```js
typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
typeof b // b 没有声明，但是还会显示 undefined
```

### typeof 对于对象，除了函数都会显示 object


```js
typeof [] // 'object'
typeof {} // 'object'
typeof console.log // 'function'
```
对于 null 来说，虽然它是基本类型，但是会显示 object，这是一个存在很久了的 Bug

```js
typeof null // 'object'
```

## instanceof

### instanceof 可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的 prototype


```js
试着实现一下 instanceof
function instanceof(left, right) {
    // 获得类型的原型
    let prototype = right.prototype
    // 获得对象的原型
    left = left.__proto__
    // 判断对象的类型是否等于类型的原型
    while (true) {
    	if (left === null)
    		return false
    	if (prototype === left)
    		return true
    	left = left.__proto__
    }
}
```
