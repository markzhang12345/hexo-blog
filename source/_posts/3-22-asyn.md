---
title: "[JS]关于异步JavaScript"
date: 2024-03-22 19:27:43
tags: web
categories: 笔记
toc: true
cover: https://radicalhub.com/wp-content/uploads/2018/07/javascript.jpg
---
# 异步JavaScript简介
异步编程是我们在等待一个长期任务完成得同时可以执行其他命令，不必等待长期任务的完成。**同时，你的程序将在任务完成后显示成果。**

浏览器有许多功能需要很长时间来完成，因此，异步编程是必要的，例如：
* 使用`fetch()`发起HTTP请求
* 使用`getUserMedia`访问用户的摄像头和麦克风
* 使用`showOpenFilePicker`请求用户选择文件以供访问

## 事件处理程序
事件处理程序实际上就是异步编程的一种形式：你提供的函数将在事件发生时被调用，而不是立刻被调用。若“事件”是**异步操作已经完成**，那么异步操作的调用结果就会被事件传递给调用者。

一些早期的异步API，如`XMLHttpRequest`API就是以这种方式来使用事件的。`XMLHttpRequest`可以让你用`JavaScript`向远程服务器发起HTTP请求。这样的操作通常需要很长时间，所以它被设计成异步API。我们可以给`XMLHttpRequest`对象附加时间监听器来让程序在请求进展和最终完成时获得通知。

## 回调
时间处理程序时一种特殊的回调函数。回调函数，即**一个被传递到另一个函数中的会在适当的时候被调用的函数**。回调函数曾是JavaScript中实现异步函数的主要方式。

然而，当回调函数本身需要调用其他同样接受回调函数的函数时，基于回调的代码会非常难以理解。
```
function doStep1(init, callback) {
  const result = init + 1;
  callback(result);
}
function doStep2(init, callback) {
  const result = init + 2;
  callback(result);
}
function doStep3(init, callback) {
  const result = init + 3;
  callback(result);
}
function doOperation() {
  doStep1(0, (result1) => {
    doStep2(result1, (result2) => {
      doStep3(result2, (result3) => {
        console.log(`结果：${result3}`);
      });
    });
  });
}
doOperation();
```
该函数深度嵌套，非常难以阅读和调试。事实上，JavaScript中异步编程的基础是`Promise`。
# Promise
`Promise`是现代JavaScript中异步编程的基础，是一个由异步函数返回的可以向我们只是当前操作所处状态的**对象**。在`Promise`返回给调用者时，操作往往没有完成，但`Promise`**对象**可以让我们在操作最终完成时对其进行处理。下面，我们以`fetch`API为例：
## fetch() API
`fetch()`是一个现代的，基于`Promise`的，用于替代`XMLHttpRequest`的**方法**。

由如下代码：
```
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

console.log(fetchPromise);

fetchPromise.then((response) => {
  console.log(`已收到响应：${response.status}`);
});

console.log("已发送请求……");
```

这里：
1. 调用`fetch()`API,并将返回值赋值给`fetchPromise`变量。
2. 输出`fetchPromise`变量，输出结果为`Promise {<state>: "pending"}`。这里输出的是`Promise`**对象**的信息：它有一个`state`属性，值为`"pending"`。`"pending"`状态意味着操作仍在进行。
3. 将一个处理函数传递给`Promise`的`then`方法，若操作成功，`Promise`将调用我们的处理函数，传去一个包含服务器响应的`Response`**对象**。
4. 输出一条成功信息。

完整的输出如下：
```
Promise { <state>: "pending" }
已发送请求……
已收到响应：200
```

## 链式使用Promise
在我们通过`fetch()`API得到一个`Respose`**对象**时，我们需要调用另外一个函数来获取响应数据。若我们向获取`json`格式，我们可以调用`Response`**对象**的`json()`方法。事实上，`json()`也是异步的，所以我们要调用两个异步函数：
```
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise.then((response) => {
    const jsonPromise = response.json();
    jsonPromise.then((response) => {
        console.log(json[0].name);
    });
});
```

在上面的代码中，我们给`fetch()`返回的`Promise`**对象**中添加了一个`then()`处理程序。在这个处理程序中，我们调用了`json()`**方法**(该方法返回一个`Promise`对象)，然后将新的`then()`处理程序传递到`response.json()`返回的`Promise`中。

`Promise`的优雅之处在于`then()`本身也可以返回一个`Promise`，这个`Promise`将指示`then()`中调用的异步函数的完成状态。(即将`then()`内调用的异步函数返回的`Promise`显式的作为`then()`的返回值，看作then()为一个异步函数).

改写上方代码：
```
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise
  .then((response) => {
    return response.json();
  })
  .then((json) => {
    console.log(json[0].name);
  });
```

当然，`Promise`的状态不总是程序成功执行。若其状态码不是**OK**，我们就抛出一个错误：
```
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise
  .then((response) => {
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    return response.json();
  })
  .then((json) => {
    console.log(json[0].name);
  });
```

# 错误捕获
`Promise`提供了一个`catch()`方法来支持错误处理。异步操作成功时，参数被传入`then()`，若失败，则将错误信息传入`catch()`。

如果将 catch() 添加到 Promise 链的末尾，它就可以在任何异步函数失败时被调用。于是，我们就可以将一个操作实现为几个连续的异步函数调用，并在一个地方处理所有错误。、

```
const fetchPromise = fetch(
  "bad-scheme://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise
  .then((response) => {
    if (!response.ok) {
      throw new Error(`HTTP 请求错误：${response.status}`);
    }
    return response.json();
  })
  .then((json) => {
    console.log(json[0].name);
  })
  .catch((error) => {
    console.error(`无法获取产品列表：${error}`);
  });
```
# Promise术语
Promise 有三种状态：

- 待定(pending)：初始状态，既没有被兑现，也没有被拒绝。这是调用 fetch() 返回 Promise 时的状态，此时请求还在进行中。
- 已兑现(fulfilled)：意味着操作成功完成。当 Promise 完成时，它的 then() 处理函数被调用。
- 已拒绝(rejected)：意味着操作失败。当一个 Promise 失败时，它的 catch() 处理函数被调用。

有时我们用**已敲定（settled）**这个词来同时表示**已兑现（fulfilled）**和**已拒绝（rejected）**两种情况。

如果一个`Promise`处于**resolved**状态，或者它被“锁定”以跟随另一个`Promise`的状态，那么它就是**fulfilled**。

# 学习链接
[MDN](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Asynchronous)