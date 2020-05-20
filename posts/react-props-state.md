---
title: 'react中通过props给子组件传值，父组件setState后props不能同步更新的问题'
date: 2020-05-20 22:48:59
tags: [react]
published: true
hideInList: false
feature: 
isTop: false
---
一、业务场景

父组件给子组件一个数据列表后，增删改都在子组件中进行

实现思路：父组件将数据列表以props的形式传递给子组件，子组件将这个props赋值给自身的state，之后的增删改就更新自身的state值，以实现局部刷新。

二、一个demo实现

==强烈推荐使用codesandbox.io进行demo操作==

```js
import { render } from 'react-dom'
import React from 'react'

class Child extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      list: props.list
    }
  }

  handleCilck = () => {
    this.setState({
      list: this.state.list.concat({ name: 'sarah' })
    })
  }

  render() {
    return (
      <div>
        <button onClick={this.handleCilck}>添加</button>
        {this.state.list.map((item, index) => {
          return <h1 key={index}>Hello, {item.name}</h1>
        })}
      </div>
    )
  }
}

class Parent extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      list: [{ name: 'lily' }, { name: 'bob' }]
    }
  }

  render() {
    return (
      <div>
        <Child list={this.state.list} />;
      </div>
    )
  }
}

render(<Parent />, document.getElementById('root'))
```

**这个demo会产生一个问题，如果父组件传递给子组件的props改变了，子组件渲染的列表数据却不会对应发生改变。**


原因：父组件更新导致子组件更新时，子组件的生命周期执行顺序如下：

```js
componentWillReceiveProps(UNSAFE_componentWillReceiveProps) 
// componentWillReceiveProps is deprecated since React 16.9.0, use UNSAFE_componentWillReceiveProps instead
shouldComponentUpdate
componentWillUpdate
render
componentDidUpdate
```

==**也就是说子组件刷新的时候，父组件传递给子组件的props发生改变，引发子组件的render，并没有执行子组件的constructor函数，子组件没有被卸载自然不会重新加载，只会重新render，而如果父组件的props传递给子组件的state，那么子组件的state只会在第一次加载的时候被赋值，后续的父组件props变化并不会被赋值到子组件的state上，还是要理解组件的声明周期函数，以及各个函数在什么时候会被调用。**==

解决方案：在UNSAFE_componentWillReceiveProps中重新对state赋值。

```js
UNSAFE_componentWillReceiveProps(nextProps) {
    this.setState({
      list: nextProps.list
    })
}

```

接着给子组件添加修改功能

js部分:
```js
handleModify = () => {
    let newArray = this.state.list
    newArray[0].name = 'natasha'
    this.setState({
      list: newArray
    })
  }
```
jsx部分:
```html
<button onClick={this.handleAdd}>添加</button>
```

如果直接这样做又会引发一个新的问题

因为list是引用类型，这样更改的话，不仅更改了子组件自身的state，还更改了父组件传递进来的props，也就是父组件的state.list的值。

因此在更改引用类型的数据时，应该采用浅拷贝或者深拷贝的方式


三、总结

总结以上demo实践，父组件用props赋值给子组件的state时，需要注意两点：

1.在生命周期UNSAFE_componentWillReceiveProps中重新赋值

2.用深拷贝创建不可变类型引用数据


> react官方文档讲的挺好
>
> 参考资料：
>
> [文章一](https://www.azimiao.com/6316.html)
>
> [文章二](https://www.jianshu.com/p/19c4dc3394f0)
>
> [文章三](https://segmentfault.com/q/1010000008387645)
>
> [文章四](https://blog.csdn.net/RuiKe1400360107/article/details/89554600)