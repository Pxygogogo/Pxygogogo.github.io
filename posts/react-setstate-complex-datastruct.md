---
title: 'react setState修改嵌套对象'
date: 2020-05-19 22:39:52
tags: [react]
published: true
hideInList: false
feature: 
isTop: false
---
在开发过程中,时常会在state中遇到一些比较复杂的数据结构,类似下面这样的:


```js
state = {
     list: {
        objA: {
          name: 'A',
          age: 20
        },
        objB: {
          name: 'B',
          age: 25
        },
        objC: {
          show: false
        },
        objD: 'D'
      }
}
```

这时需要我们修改list中objA中的name属性和objD的属性,遇到这样的情况我们一般会使用什么办法解决呢?其实有三种解决方案:

### 方案一(作用于对象中的深层级和第一层级):


```js
this.setState({
     list: {
         ...this.state.list,
          objA: {
            ...this.state.list.objA,
            name: 'A1'
            }
        }
    })
```



### 方案二(作用对象中的第一层级):
```js
    let data = Object.assign({}, this.state.list, {objD: 'D1'})
    this.setState({
        list: data
    })
```

### 方案三(作用于对象中的深层级和第一层级):


```js
    let data = this.state.list;
    data.objA.name = 'A1';
    data.objD = 'D1';
    this.setState({
        list: data
    })
```

总结:
| 方案 | 适用范围 | 缺点
| :-: | :-: | :-: |
| 方案一 |多层级和单一层级都存在 | 写法麻烦,单次对象赋值,对象层级多的时候,容易遗漏
| 方案二 |只适用与第一层级 | 只适用第一层级
| 方案三 |多层级和单一层级都存在 | 若存在深层及和单一层级的,需要多次赋值

这三种写法都可以,使用哪一种方案,还需要根据业务来定

[原文链接](https://blog.csdn.net/weixin_34191845/article/details/89596546?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase)
