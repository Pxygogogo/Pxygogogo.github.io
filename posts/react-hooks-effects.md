---
title: 'React Hooks之每一次渲染都有它自己的effects'
date: 2020-06-10 11:20:47
tags: [react]
published: true
hideInList: false
feature: 
isTop: false
---
### React Hooks之每一次渲染都有它自己的effects

同样通过一个例子来说明：

```js
function Counter() {
  const [count, setCount] = useState(0)

  useEffect(() => {
    setTimeout(() => {
      console.log(`You clicked ${count} times`)
    }, 3000)
  })

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  )
}
```

点击 3 次按钮：

分析：
整个过程组件进行了四次渲染：

>初始化，render0：打印 You clicked 0 times；
>
>修改 count 值为1，render1：打印 You clicked 1 times；
>
>修改 count 值为2，render2：打印 You clicked 2 times；
>
>修改 count 值为3，render3：打印 You clicked 3 times；
>
>通过整个例子我们可以知道，在每次渲染中，useEffect 也是独立的。

并不是count的值在“不变”的effect中发生了改变，而是effect 函数本身在每一次渲染中都不相同。

每一个effect版本“看到”的count值都来自于它属于的那次渲染：

```js
// During first render
function Counter() {
  // ...
  useEffect(
    // Effect function from first render
    setTimeout(() => {
      console.log(`You clicked ${0} times`)
    }, 3000)
  );
  // ...
}

// After a click, our function is called again
function Counter() {
  // ...
  useEffect(
    // Effect function from second render
    setTimeout(() => {
      console.log(`You clicked ${1} times`)
    }, 3000)
  );
  // ...
}

// After another click, our function is called again
function Counter() {
  // ...
  useEffect(
    // Effect function from third render
    setTimeout(() => {
      console.log(`You clicked ${2} times`)
    }, 3000)
  );
  // ..
}

// After another click, our function is called again
function Counter() {
  // ...
  useEffect(
    // Effect function from forth render
    setTimeout(() => {
      console.log(`You clicked ${3} times`)
    }, 3000)
  );
  // ..
}

```


### 清除 effect

在 React class 中，你通常会在 componentDidMount 中设置订阅，并在 componentWillUnmount 中清除它。当我们在 useEffect 中使用了定时器或者添加了某些订阅，可以通过 useEffect 返回一个函数，进行清除定时器或者取消订阅等操作。

看一下例子，在 useEffect 中打印点击的次数：
```js
function Example() {
  const [count, setCount] = useState(0)

  useEffect(() => {
    console.log(`You clicked ${count} times`)
    return() => {
      console.log('销毁')
    }
  })

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  )

```

点击按钮 3 次，控制台中打印的结果如下：

You clicked 0 times
销毁
You clicked 1 times
销毁
You clicked 2 times
销毁
You clicked 3 times

从打印结果我们可以很容易看出，上一次的 effect 是在重新渲染时被清除的。


补充：那么组件的整个重新渲染的过程是怎么样的呢？
假设现在有 render0 和 render1 两次渲染：

React 渲染 render1 的UI;
浏览器绘制，并呈现 render1 的UI；
React 清除 render0 的 effect；
React 运行 render1 的 effect；


>React 只会在浏览器绘制后运行effects。这使得你的应用更流畅因为大多数effects并不会阻塞屏幕的更新。


