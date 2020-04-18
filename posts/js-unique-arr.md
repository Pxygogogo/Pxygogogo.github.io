---
title: 'js数组去重'
date: 2020-02-13 10:26:31
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
## 一、利用ES6 Set去重（ES6中最常用）


```js
function unique (arr) {
  return Array.from(new Set(arr))
}
```
甚至可以再简化下：

```js
function unique(array) {
    return [...new Set(array)];
}
```

还可以再简化下：

```js
var unique = (a) => [...new Set(a)]
```

此外，如果用 Map 的话：

```js
function unique (arr) {
    const seen = new Map()
    return arr.filter((a) => !seen.has(a) && seen.set(a, 1))
}
```

## 二、利用for嵌套for，然后splice去重（ES5中最常用）



```js
function unique(arr){
    for(let i = 0 ; i < arr.length ; i++){
        for(let j = i+1 ; j < arr.length ; j++){
            if(arr[i] === arr[j]){
                arr.splice(j,1);
                j--;
            }
        }
    }
    return arr;
}
```
双层循环，外层循环元素，内层循环时比较值。值相同时，则删去这个值。

## 三、利用indexOf去重


```js
法一：
function unique(arr){
    if(!Array.isArray(arr)){
        throw Error('wrong type,Array required')
    }
    let res = [];
    for (let i = 0; i < arr.length; i++) {
        if(res.indexOf(arr[i])===-1){
            res.push(arr[i])
        }
    }
    return res;
}
```

新建一个空的结果数组，for 循环原数组，判断结果数组是否存在当前元素，如果有相同的值则跳过，不相同则push进数组。




## 四、利用sort()

```js
function unique(arr){
    if(!Array.isArray(arr)){
        throw Error('wrong type,Array required')
    }
    arr = arr.sort();
    let res = [arr[0]];
    for (let i = 1; i < arr.length ; i++) {
        if(arr[i]!==arr[i-1]){
            res.push(arr[i])
        }
    }
    return res;
}
```

利用sort()排序方法，然后根据排序后的结果进行遍历及相邻元素比对。


## 五、Object 键值对


这种方法是利用一个空的 Object 对象，我们把数组的值存成 Object 的 key 值，比如 Object[value1] = true，在判断另一个值的时候，如果 Object[value2]存在的话，就说明该值是重复的。示例代码如下：


```js
var array = [1, 2, 1, 1, '1'];

function unique(array) {
    var obj = {};
    return array.filter(function(item, index, array){
        return obj.hasOwnProperty(item) ? false : (obj[item] = true)
    })
}

console.log(unique(array)); // [1, 2]
```

我们可以发现，是有问题的，因为 1 和 '1' 是不同的，但是这种方法会判断为同一个值，这是因为对象的键值只能是字符串，所以我们可以使用 `typeof item + item` 拼成字符串作为 key 值来避免这个问题：

```js
var array = [1, 2, 1, 1, '1'];

function unique(array) {
    var obj = {};
    return array.filter(function(item, index, array){
        return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true)
    })
}

console.log(unique(array)); // [1, 2, "1"]
```

然而，即便如此，我们依然无法正确区分出两个对象，比如 {value: 1} 和 {value: 2}，因为 `typeof item + item` 的结果都会是 `object[object Object]`，不过我们可以使用 JSON.stringify 将对象序列化：

```js
var array = [{value: 1}, {value: 1}, {value: 2}];

function unique(array) {
    var obj = {};
    return array.filter(function(item, index, array){
        console.log(typeof item + JSON.stringify(item))
        return obj.hasOwnProperty(typeof item + JSON.stringify(item)) ? false : (obj[typeof item + JSON.stringify(item)] = true)
    })
}

console.log(unique(array)); // [{value: 1}, {value: 2}]
```



## 六、filter

ES5 提供了 filter 方法，我们可以用来简化外层循环：

比如使用 indexOf 的方法：

```js
function unique(arr) {
  return arr.filter(function(item, index, arr) {
    //当前元素，在原始数组中的第一个索引==当前索引值，否则返回当前元素
    return arr.indexOf(item, 0) === index;
  });
}
```
