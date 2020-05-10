---
title: 'redux小技巧'
date: 2020-05-10 11:56:27
tags: [redux]
published: true
hideInList: false
feature: 
isTop: false
---
1. 把Action Types单独写入一个文件
单独出来的原因：

- 这些Types如果不统一管理，不利于大型项目的服用，设置会长生冗余代码。
- 因为Action里的Type，一定要和Reducer里的type一一对应在，所以这部分代码或字母写错后，浏览器里并没有明确的报错，这给调试带来了极大的困难。

例：


```js
// 导出 目录：src/store/actionTypes.js
export const  CHANGE_INPUT = 'changeInput'
export const  ADD_ITEM = 'addItem'
export const  DELETE_ITEM = 'deleteItem'

// 引入
import { CHANGE_INPUT , ADD_ITEM , DELETE_ITEM } from './store/actionTypes'
```

2. 把所有的Redux Action放到一个文件里进行管理

例：


```
// 目录：src/store/actionCreators.js
import {CHANGE_INPUT}  from './actionTypes'

export const changeInputAction = (value)=>({
    type:CHANGE_INPUT,
    value
})
// 引入
import {changeInputAction} from './store/actionCreatores'
changeInputValue(e){
    const action = changeInputAction(e.target.value)
    store.dispatch(action)
}
```


3. redux三个小坑

- store必须是唯一的，多个store是坚决不允许，只能有一个store空间
- 只有store能改变自己的内容，Reducer不能改变
- Reducer必须是纯函数

**纯函数定义**
>如果函数的调用参数相同，则永远返回相同的结果。它不依赖于程序执行期间函数外部任何状态或数据的变化，必须只依赖于其输入参数。


比如在Reducer里增加一个异步ajax函数，获取一些后端接口数据，然后再返回，这就是不允许的（包括你使用日期函数也是不允许的），因为违反了调用参数相同，返回相同的纯函数规则


4. 可选的好方式
- UI和业务逻辑分离
- 无状s态组件