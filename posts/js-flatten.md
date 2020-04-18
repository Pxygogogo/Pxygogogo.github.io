---
title: '数组扁平化'
date: 2020-02-14 15:22:29
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
## 扁平化

数组的扁平化，就是将一个嵌套多层的数组 array (嵌套可以是任何层数)转换为只有一层的数组。

举个例子，假设有个名为 flatten 的函数可以做到数组扁平化，效果就会如下：

```js
var arr = [1, [2, [3, 4]]];
console.log(flatten(arr)) // [1, 2, 3, 4]
```

知道了效果是什么样的了，我们可以去尝试着写这个 flatten 函数了

## 递归

我们最一开始能想到的莫过于循环数组元素，如果还是一个数组，就递归调用该方法：

```js
// 方法 1
var arr = [1, [2, [3, 4]]];

function flatten(arr) {
    var result = [];
    for (var i = 0,  i < arr.length; i++) {
        if (Array.isArray(arr[i])) {
            result = result.concat(flatten(arr[i]))
        }
        else {
            result.push(arr[i])
        }
    }
    return result;
}


console.log(flatten(arr))
```

## toString

如果数组的元素都是数字，那么我们可以考虑使用 toString 方法，因为：

```js
[1, [2, [3, 4]]].toString() // "1,2,3,4"
```

调用 toString 方法，返回了一个逗号分隔的扁平的字符串，这时候我们再 split，然后转成数字不就可以实现扁平化了吗？

```js
// 方法2
var arr = [1, [2, [3, 4]]];

function flatten(arr) {
    return arr.toString().split(',').map(function(item){
        return +item
    })
}

console.log(flatten(arr))
```

然而这种方法使用的场景却非常有限，如果数组是 [1, '1', 2, '2'] 的话，这种方法就会产生错误的结果。

## reduce

既然是对数组进行处理，最终返回一个值，我们就可以考虑使用 reduce 来简化代码：


```js
function flatten(arr){
    return arr.reduce((acc,cur)=>{
        return acc.concat(Array.isArray(cur) ? flatten(cur) : cur);
    },[])
}
```
```js
//简化
const flatten =  (arr) => arr.reduce((acc,cur)=> acc.concat(Array.isArray(cur) ? flatten(cur) : cur),[]);
```

## 运用es6 扩展运算符


```js
const flattenDeep = (arr) => Array.isArray(arr) ? arr.reduce( (a, b) => [...a, ...flattenDeep(b)] , []) : [arr];

flattenDeep([1, [[2], [3, [4]], 5]])
```