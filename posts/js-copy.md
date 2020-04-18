---
title: 'js实现深浅拷贝'
date: 2020-01-01 21:01:14
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
## 实现浅拷贝

- 一、...实现
```
let copy = {...{a:1}};
```

- 二、(ES6)Object.assign实现
```
let copy = Object.assign({},{a:1});
```

## 实现深拷贝

- 一、JSON.stringify()/JSON.parse()实现

但是该方法也是有局限性的：

1. 会忽略 undefined
2. 会忽略 symbol
3. 不能序列化函数
4. 不能解决循环引用的对象

```
let obj = {a: 1, b: {x: 3}};
let deepCopy = JSON.parse(JSON.stringify(obj));
```
==注：这种方法实施的前提必须是实施的对象是JSON安全的==
- 二、递归拷贝

```
function deepClone(obj) {
    let copy = obj instanceof Arrary ? [] : {};
    for (let i in obj) {
        if(obj.hasOwnProperty(i)) {
            copy[i] = typeof obj[i] === 'object' ? deepClone(obj[i]) : obj[i];
        }
    }
    return copy
}

```