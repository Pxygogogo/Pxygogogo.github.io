---
title: 'React Hooks之每一次渲染都有它自己的 Props and State和事件处理函数'
date: 2020-06-10 10:40:23
tags: [react]
published: true
hideInList: false
feature: 
isTop: false
---
React Hooks之每一次渲染都有它自己的 Props and State和事件处理函数,就我个人角度，我将它简单的理解成为一个快照


### 通过一个例子来说明：
```js
export default function Example(props) {
  const [times, setTimes] = useState(0);
  const handleClick = () => {
    setTimeout(() => {
      alert(`example--${props.count}`);
      setTimes(times + 1);
    }, 3000);
  };
  return (
    <>
      <h1>Example</h1>
      <p>props.count---{props.count}</p>
      <p>state.times---{times}</p>
      <button onClick={handleClick}>alert count</button>
    </>
  );
}
```



重点关注 <Example> 函数组件的代码，其中的 count 属性由父组件传入，初始值为 0，每隔一秒增加 1。点击 "alert count" 按钮，将延迟 3 秒钟弹出 count 的值。==操作后发现，弹窗中出现的值，与页面中文本展示的值不同，而是等于点击 "alert Count" 按钮时 count 的值==。

其中的times属性是组件自身的state，初始值为0，点击 "alert count" 按钮，将延迟 3 秒钟 + 1。如果我在3s内，多次点击按钮，最终它的值并不会是上一次的times值+点击次数*1而是上一次的times+1，这说明只要当点击事件触发时那一刻的times的值没有发生改变，那么无论你在3s内点击多少次，它都是一样的结果。



其点击的快照类似如此：

```js
// 第一次点击后3s内的所有点击快照
const handleClick = () => {
    setTimeout(() => {
      alert(`example--点击时的props.count`);
      setTimes(0 + 1);
    }, 3000);
  };
```

如果更换为 class 组件，它的实现是 <Example2> 这样的：



```js

class Example2 extends Component {
  constructor(props) {
    super(props);
    this.state = {
      times: 0
    };
  }
  handleClick = () => {
    setTimeout(() => {
      alert(`example2--${this.props.count}`);
      this.setState({
        times: this.state.times + 1
      });
    }, 3000);
  };
  render() {
    return (
      <div>
        <h1>Example2</h1>
        <p>{this.props.count}</p>
        <p>{this.state.times}</p>
        <button onClick={this.handleClick}>alert count</button>
      </div>
    );
  }
}

```
此时，点击 "alert count" 按钮，延迟 3 秒钟弹出 count 的值，与页面中文本展示的值是一样的。
在某些情况下，<Example> 函数组件中的行为才符合预期。如果将 setTimeout 类比到一次 Fetch 请求，在请求成功时，我要获取的是发起 Fetch 请求前相关的数据，并对其进行修改。

其中的times属性是组件自身的state，初始值为0，点击 "alert count" 按钮，将延迟 3 秒钟 + 1。如果我在3s内，多次点击按钮，最终它的值始终会是上一次的times+1，与hooks组件的表现不一。


### 如何理解其中的差异？

在 <Example2> class 组件中，我们是从 this 中获取到的 props.count。this 是固定指向同一个组件实例的。在 3 秒的延时器生效后，组件重新进行了渲染，this.props 也发生了改变。当延时的回调函数执行时，读取到的 this.props 是当前组件最新的属性值。
而在 <Example> 函数组件中，每一次执行 render 函数时，props 作为该函数的参数传入，它是函数作用域下的变量。

由于 props 是 Example 函数作用域下的变量，可以说对于这个函数的每一次调用中，都产生了新的 props 变量，它在声明时被赋予了当前的属性，他们相互间互不影响。
换一种说法，对于其中任一个 props ，其值在声明时便已经决定，不会随着时间产生变化。handleClick 函数亦是如此。例如定时器的回调函数是在未来发生的，但 props.count 的值是在声明 handleClick 函数时就已经决定好的。


### 结论：

在任意一次渲染中，props和state是始终保持不变的。如果props和state在不同的渲染中是相互独立的，那么使用到它们的任何值也是独立的（包括事件处理函数）。它们都“属于”一次特定的渲染。即便是事件处理中的异步函数调用“看到”的也是这次渲染中的count值。

[edit on codesandbox](https://codesandbox.io/s/react-hookszhimeiyicixuanranduyoutazijide-props-and-stateheshijianchulihanshu-syhjb?file=/src/index.js)

>参考文章：
>
>[useEffect 完整指南](https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/)
>
>[React Hooks 最佳实践](https://juejin.im/post/5ec7372cf265da76de5cd0c9)
