---
title: 'React Hooks之在比对中执行effects'
date: 2020-06-11 12:50:34
tags: [react]
published: true
hideInList: false
feature: 
isTop: false
---
React 针对 React Elements 前后值进行对比，只去更新 DOM 真正发生改变的部分。对于 Effects，能否有类似这样的理念呢？


某个 Effects 函数一旦执行，函数内的副作用已经发生，React 无法猜测到函数相比于上一次做了哪些变化。但我们可以给 useEffect 传入第二个参数，作为依赖数组 (deps)，避免 Effects 不必要的重复调用。
这个 deps 的含义是：当前 Effect 依赖了哪些变量。

实际应用中，我们不需要在每次组件更新时，都去执行某些 effects，这个时候我们可以给 useEffect 设置依赖，告诉 React 什么时候去执行 useEffect

但有时问题不一定能解决。比如官网就有[ 这样的例子:](https://zh-hans.reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)

```jsx
const [count, setCount] = useState(0);

useEffect(() => {
    const id = setInterval(() => {
        setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
}, [count]);


```

如果我们频繁修改 count，每次执行 Effect，上一次的计时器被清除，需要调用 setInterval 重新进入时间队列，实际的定期时间被延后，甚至有可能根本没有机会被执行。

但是下面这样的实践方式也不宜采用：

在 Effect 函数中寻找一些变量添加到 deps 中，需要满足条件：其变化时，需要重新触发 effect。

按照这种实践方式，count 变化时，我们并不希望重新 setInterval，故 deps 为空数组。这意味着该 hook 只在组件挂载时运行一次。Effect 中明明依赖了 count，但我们撒谎说它没有依赖，那么当 setInterval 回调函数执行时，获取到的 count 值永远为 0。

遇到这种问题，直接从 deps 移除是不可行的。静下来分析一下，此处为什么要用到 count？能否避免对其直接使用？

可以看到，在 setCount 中用到了 count，为的是把 count 转换为 count + 1 ，然后返回给 React。React 其实已经知道当前的 count，我们需要告知 React 的仅仅是去递增状态，不管它现在具体是什么值。

所以有一个最佳实践：状态变更时，应该通过 setState 的函数形式来代替直接获取当前状态。

```
setCount(c => c + 1);
```

另外一种场景是：

```jsx
const [count, setCount] = useState(0);

useEffect(() => {
    const id = setInterval(() => {
        console.log(count);
    }, 1000);
    return () => clearInterval(id);
}, []);
```

复制代码在这里，同样的，当count 变化时，我们并不希望重新 setInterval。但我们可以把 count 通过 ref 保存起来。

```jsx
const [count, setCount] = useState(0);
const countRef = useRef();
countRef.current = count;

useEffect(() => {
    const id = setInterval(() => {
        console.log(countRef.current);
    }, 1000);
    return () => clearInterval(id);
}, []);
```

复制代码这样，count 的确不再被使用，而是用 ref 存储了一个在所有帧中共享的变量。

另外的情况是，Effects 依赖了函数或者其他引用类型。与原始数据类型不同的是，在未优化的情况下，每次 render 函数调用时，因为对这些内容的重新创建，其值总是发生了变化，导致 Effects 在使用 deps 的情况下依然会频繁被调用。

对于这个问题，[官网的 FAQ ](https://zh-hans.reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies)已经给出了答案：对于函数，使用 useCallback 避免重复创建；对于对象或者数组，则可以使用 useMemo。从而减少 deps 的变化。


>[原文链接](https://juejin.im/post/5ec7372cf265da76de5cd0c9)
