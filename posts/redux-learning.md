---
title: 'redux理解'
date: 2020-05-10 11:57:17
tags: [redux]
published: true
hideInList: false
feature: 
isTop: false
---
![redux工作流程](https://s1.ax1x.com/2020/05/09/YMT4Hg.jpg)

1. 一个比喻：
- component --- 读者
- store --- 图书馆
- reducer --- 图书管理员
- action --- 读者的动作

2. 流程理解
- 所有component的数据状态均来自统一的store中
- 如果需要更新store中的数据，需要在对应的事件中定义一个action，action就是一个对象，这个对象一般有两个属性，第一个是对action的描述，第二个是要改变的值
- action会通过dispatch方法将type和value通过store的自动推送策略直接传递给reducer（虚线的作用）
- reducer的两个参数 
> state：指的是原始仓库里的状态
>
> action: 指的是action新传递的状态
- reducer中不能直接修改state，需要深拷贝一个state对象，然后在新的state对象上操作，并最终返回新的state


3. 注意点

- store必须是唯一的，多个store是坚决不允许，只能有一个store空间
- 只有store能改变自己的内容，Reducer不能改变
- Reducer必须是纯函数

**纯函数定义**
>如果函数的调用参数相同，则永远返回相同的结果。它不依赖于程序执行期间函数外部任何状态或数据的变化，必须只依赖于其输入参数。


比如在Reducer里增加一个异步ajax函数，获取一些后端接口数据，然后再返回，这就是不允许的（包括你使用日期函数也是不允许的），因为违反了调用参数相同，返回相同的纯函数规则