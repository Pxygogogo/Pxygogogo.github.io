---
title: 'Redux和Flux'
date: 2020-05-11 22:35:38
tags: [redux]
published: true
hideInList: false
feature: 
isTop: false
---
## Flux
### Flux 优点

- **最重要的就是“==单向数据流==”的管理方式**。在Flux的理念里，如果要改变界面，必须改变Store中的状态，如果要改变Store中的状态，必须派发一个action对象，这就是规矩。在这个规矩之下，想要追溯一个应用的逻辑就变得非常容易。

解决的MVC的问题:
>MVC最大的问题就是无法禁绝View和Model之间的直接对话，对应于MVC中View就是Flux中的View，对应于MVC中的Model的就是Flux中的Store，在Flux中，Store只有get方法，没有set方法，根本可能直接去修改其内部状态，View只能通过get方法获取Store的状态，无法直接去修改状态，如果View想要修改Store状态的话，只有派发一个action对象给Dispatcher

### Flux 不足
1. Store之间依赖关系
2. 难以进行服务器端渲染
3. Store混杂了逻辑和状态


## Redux
2013年问世的Flux饱受争议，而2015年Dan Abramov提出了在Flux基础上的改进框架Redux，则是一鸣惊人，在所有Flux的变体中算是最受关注的框架，没有之一。

Flux的基本原则是“单向数据流”,

Redux在此基础上强调三个基本原则：
- 唯一数据源（Single Source of Truth）；

唯一数据源指的是应用的状态数据应该只存储在唯一的一个Store上。这个唯一Store上的状态，是一个树形的对象，每个组件往往只是用树形对象上一部分的数据，而如何设计Store上状态的结构，就是Redux应用的核心问题
- 保持状态只读（State is read-only）；

保持状态只读，就是说不能去直接修改状态，要修改Store的状态，必须要通过派发一个action对象完成，这一点，和Flux的要求并没有什么区别。
- 数据改变只能通过纯函数完成（Changes are made with pure functions）。

这里所说的纯函数就是Reducer, Redux这个名字的前三个字母Red代表的就是Reducer。按照创作者Dan Abramov的说法，Redux名字的含义是Reducer+Flux。Reducer不是一个Redux特定的术语，而是一个计算机科学中的通用概念，很多语言和框架都有对Reducer函数的支持。就以JavaScript为例，数组类型就有reduce函数，接受的参数就是一个reducer, reduce做的事情就是把数组所有元素依次做“规约”，对每个元素都调用一次参数reducer，通过reducer函数完成规约所有元素的功能.


>Redux是Flux框架的一个巨大改进，Redux强调单一数据源、保持状态只读和数据改变只能通过纯函数完成的基本原则，和React的UI=render(state)思想完全契合