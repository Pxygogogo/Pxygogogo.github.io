---
title: 'react复制内容到剪切板'
date: 2020-05-18 22:31:43
tags: [react,js,浏览器相关]
published: true
hideInList: false
feature: 
isTop: false
---

### react-copy-to-clipboard插件
一个react插件，npm安装即可。
优点：查看官方示例，上手简单，兼容性扩展性会比较强
缺点：截止发文，最新版5.0.2版包大小达到了37.2kb，对于一个在项目中使用不多的需求有些浪费。

### 原生js实现



#### document.execCommand()方法


[MDN上定义：](hhttps://developer.mozilla.org/zh-CN/docs/Web/API/Document/execCommand)

当一个HTML文档切换到设计模式时，document暴露 execCommand 方法，该方法允许运行命令来操纵==可编辑内容区域==的元素。

再看可编辑内容区域（contenteditable）的定义

全局属性 contenteditable  是一个枚举属性，表示元素是否可被用户编辑。 如果可以，浏览器会修改元素的部件以允许编辑。

也就是意味着除了 ==<input>、<textarea>== 这样的输入域以外，是无法使用这个方法的

如果是需要获取input和textarea的值然后复制的，可以直接通过获取元素节点后通过select方法选中后直接调用document.execCommand('copy')即可

样例：


```js
    const btn = document.querySelector('#btn');
    btn.addEventListener('click',() => {
        const input = document.createElement('input');
        input.setAttribute('readonly', 'readonly');
        input.setAttribute('value', 'hello world!');
        document.body.appendChild(input);
        input.select();
        if (document.execCommand('copy')) {
            document.execCommand('copy');
            console.log('复制成功');
        }
        document.body.removeChild(input);
    })
```

> 如果需要隐藏新创建的元素时需要注意不能通过设置display值为none来实现，因为设置为none后将无法获取元素的属性值包括value，上一篇文章仔细探讨了display：none和visibility：hidden区别

#### createTextRange()



[MDN上对于range定义：](https://developer.mozilla.org/zh-CN/docs/Web/API/Range)

Range 接口表示一个包含节点与文本节点的一部分的文档片段。

可以用 Document 对象的 Document.createRange 方法创建 Range，也可以用 Selection 对象的 getRangeAt 方法获取 Range。另外，还可以通过 Document 对象的构造函数 Range() 来得到 Range

其原理与上种方法大同小异

样例：


```js
        var div = document.getElementById('div1');
        if (document.body.createTextRange) {
            var range = document.body.createTextRange();
            range.moveToElementText(div);
            range.select();
        } else if (window.getSelection) {
            var selection = window.getSelection();
            var range = document.createRange();
            range.selectNodeContents(div);
            selection.removeAllRanges();
            selection.addRange(range);
        } else {
            console.warn("none");
        }
        document.execCommand("copy")
```




[参考一](https://www.cnblogs.com/dch0/p/12674129.html)

[参考二](https://www.jb51.net/article/135605.htm)