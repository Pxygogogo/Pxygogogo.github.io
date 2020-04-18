---
title: '属性设置和屏蔽'
date: 2020-03-16 09:46:39
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
## 属性设置和屏蔽
- 给一个对象设置属性并不仅仅是添加一个新属性或者修改已有的属性值。
- 现在我们完整地讲解一下这个过程：
```
myObject.foo = "bar";
```

- 如果myObject对象中包含名为foo的普通数据访问属性，这条赋值语句只会修改已有的属性值。
- 如果foo不是直接存在于myObject中，[[Prototype]]链就会被遍历，类似[[Get]]操作。如果原型链上找不到foo, foo就会被直接添加到myObject上。
- 然而，如果foo存在于原型链上层，赋值语句myObject.foo = "bar"的行为就会有些不同（而且可能很出人意料）。稍后我们会进行介绍。
- 如果属性名foo既出现在myObject中也出现在myObject的[[Prototype]]链上层，那么就会发生屏蔽。myObject中包含的foo属性会屏蔽原型链上层的所有foo属性，因为myObject.foo总是会选择原型链中最底层的foo属性。
- 屏蔽比我们想象中更加复杂。下面我们分析一下如果foo不直接存在于myObject中而是存在于原型链上层时myObject.foo = "bar"会出现的三种情况。
1. 如果在[[Prototype]]链上层存在名为foo的普通数据访问属性（参见第3章）并且没有被标记为只读（writable:false），那就会直接在myObject中添加一个名为foo的新属性，它是屏蔽属性。
2. 如果在[[Prototype]]链上层存在foo，但是它被标记为只读（writable:false），那么无法修改已有属性或者在myObject上创建屏蔽属性。如果运行在严格模式下，代码会抛出一个错误。否则，这条赋值语句会被忽略。总之，不会发生屏蔽。
3. 如果在[[Prototype]]链上层存在foo并且它是一个setter（参见第3章），那就一定会调用这个setter。foo不会被添加到（或者说屏蔽于）myObject，也不会重新定义foo这个setter。

- 大多数开发者都认为如果向[[Prototype]]链上层已经存在的属性（[[Put]]）赋值，就一定会触发屏蔽，但是如你所见，三种情况中只有一种（第一种）是这样的。如果你希望在第二种和第三种情况下也屏蔽foo，那就不能使用=操作符来赋值，而是使用Object.defineProperty(..)（参见第3章）来向myObject添加foo。
> 第二种情况可能是最令人意外的，只读属性会阻止[[Prototype]]链下层隐式创建（屏蔽）同名属性。这样做主要是为了模拟类属性的继承。你可以把原型链上层的foo看作是父类中的属性，它会被myObject继承（复制），这样一来myObject中的foo属性也是只读，所以无法创建。但是一定要注意，实际上并不会发生类似的继承复制（参见第4章和第5章）。这看起来有点奇怪，myObject对象竟然会因为其他对象中有一个只读foo就不能包含foo属性。更奇怪的是，这个限制只存在于=赋值中，使用Object. defineProperty(..)并不会受到影响。


> 来自你不知道的JavaScript上卷