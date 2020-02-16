# 总结：JavaScript异步、事件循环与消息队列、微任务与宏任务(转自[掘金](https://juejin.im/post/5be5a0b96fb9a049d518febc))

## 前言

[原文](https://github.com/ZavierTang/zavier-notes/blob/master/JavaScript/%E5%90%8C%E6%AD%A5%E4%B8%8E%E5%BC%82%E6%AD%A5%E3%80%81%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF%E4%B8%8E%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E3%80%81%E5%BE%AE%E4%BB%BB%E5%8A%A1%E4%B8%8E%E5%AE%8F%E4%BB%BB%E5%8A%A1.md)

*Philip Roberts* 在演讲 [great talk at JSConf on the event loop](https://www.youtube.com/watch?v=8aGhZQkoFbQ) 中说：要是用一句话来形容 JavaScript，我可能会这样：

> “JavaScript 是单线程、异步、非阻塞、解释型脚本语言。”

- 单线程 ?
- 异步 ? ?
- 非阻塞 ? ? ?

然后，这又牵扯到了事件循环、消息队列，还有微任务、宏任务这些。

作为一个初学者，对这些了解甚少。

这几天翻阅了不少资料，似乎了解到了一二，是时候总结一下了，它们困扰了我好一段时间，就像学高数那会儿自己去理解一个概念一样。

## 单线程与多线程

单线程语言：JavaScript 的设计就是为了处理浏览器网页的交互（DOM操作的处理、UI动画等），决定了它是一门单线程语言。

> 如果有多个线程，它们同时在操作 DOM，那网页将会一团糟。

JavaScript 是单线程的，那么处理任务是一件接着一件处理，从上往下顺序执行：

```js
console.log('script start')
console.log('do something...')
console.log('script end')

// script start
// do something...
// script end
复制代码
```

上面的代码会依次打印: "script start" >> "do something..." >> "script end"

那如果一个任务的处理耗时（或者是等待）很久的话，如：网络请求、定时器、等待鼠标点击等，后面的任务也就会被阻塞，也就是说会阻塞所有的用户交互（按钮、滚动条等），会带来极不友好的体验。

但是：

```js
console.log('script start')

console.log('do something...')setTimeout(() => {  console.log('timer over')}, 1000)

// 点击页面
console.log('click page')

console.log('script end')

// script start
// do something...
// click page
// script end
// timer over
复制代码
```

`"timer over"` 在 `"script end"` 后再打印，也就是说计时器并没有阻塞后面的代码。那，发生了什么？

其实，JavaScript 单线程指的是浏览器中负责解释和执行 JavaScript 代码的只有一个线程，即为**JS引擎线程**，但是浏览器的渲染进程是提供多个线程的，如下：

- JS引擎线程
- 事件触发线程
- 定时触发器线程
- 异步http请求线程
- GUI渲染线程

> 浏览器渲染进程参考[这里](https://juejin.im/post/5a6547d0f265da3e283a1df7#heading-6)

当遇到计时器、DOM事件监听或者是网络请求的任务时，JS引擎会将它们直接交给 webapi，也就是浏览器提供的相应线程（如定时器线程为setTimeout计时、异步http请求线程处理网络请求）去处理，而JS引擎线程继续后面的其他任务，这样便实现了 **异步非阻塞**。

定时器触发线程也只是为 `setTimeout(..., 1000)` 定时而已，时间一到，还会把它对应的回调函数(callback)交给 **消息队列** 去维护，JS引擎线程会在适当的时候去消息队列取出消息并执行。

JS引擎线程什么时候去处理呢？消息队列又是什么？

这里，JavaScript 通过 **事件循环** **event loop** 的机制来解决这个问题。

这个放在后面再讨论吧！

## 同步与异步

上面说到了异步，JavaScript 中有同步代码与异步代码。

下面便是同步：

```js
console.log('hello 0')

console.log('hello 1')

console.log('hello 2')

// hello 0
// hello 1
// hello 2
复制代码
```

它们会依次执行，执行完了后便会返回结果（打印结果）。

```js
setTimeout(() => {  console.log('hello 0')}, 1000)

console.log('hello 1')

// hello 1
// hello 0
复制代码
```

上面的 `setTimeout` 函数便不会立刻返回结果，而是发起了一个异步，setTimeout 便是异步的发起函数或者是注册函数，() => {...} 便是异步的回调函数。

这里，JS引擎线程只会关心异步的发起函数是谁、回调函数是什么？并将异步交给 webapi 去处理，然后继续执行其他任务。

异步一般是以下：

- 网络请求
- 计时器
- DOM时间监听
- ...

## 事件循环与消息队列

回到事件循环 event loop

其实 **事件循环** 机制和 **消息队列** 的维护是由事件触发线程控制的。

**事件触发线程** 同样是浏览器渲染引擎提供的，它会维护一个 **消息队列**。

JS引擎线程遇到异步（DOM事件监听、网络请求、setTimeout计时器等...），会交给相应的线程单独去维护异步任务，等待某个时机（计时器结束、网络请求成功、用户点击DOM），然后由 **事件触发线程** 将异步对应的 **回调函数** 加入到消息队列中，消息队列中的回调函数等待被执行。

同时，JS引擎线程会维护一个 **执行栈**，同步代码会依次加入执行栈然后执行，结束会退出执行栈。

![Stack&Queue](https://user-gold-cdn.xitu.io/2018/11/9/166f8fdabca476ef?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

如果执行栈里的任务执行完成，即执行栈为空的时候（即JS引擎线程空闲），事件触发线程才会从消息队列取出一个任务（即异步的回调函数）放入执行栈中执行。

> 消息队列是类似队列的数据结构，遵循先入先出(FIFO)的规则。

执行完了后，执行栈再次为空，事件触发线程会重复上一步操作，再取出一个消息队列中的任务，这种机制就被称为事件循环（event loop）机制。

还是上面的代码：

```
console.log('script start')setTimeout(() => {  console.log('timer over')}, 1000)

// 点击页面
console.log('click page')

console.log('script end')

// script start
// click page
// script end
// timer over
复制代码
```

执行过程：

1. 主代码块（script）依次加入执行栈，依次执行，主代码块为：
   
   - console.log('script start')
   - setTimeout()
   - console.log('click page')
   - console.log('script end')

2. console.log() 为同步代码，JS引擎线程处理，打印 "script start"，出栈；

3. 遇到异步函数 `setTimeout`，交给定时器触发线程（异步触发函数为：`setTimeout`，回调函数为：`() => { ... }`），JS引擎线程继续，出栈；

4. console.log() 为同步代码，JS引擎线程处理，打印 "click page"，出栈；

5. console.log() 为同步代码，JS引擎线程处理，打印 "script end"，出栈；

6. 执行栈为空，也就是JS引擎线程空闲，这时从消息队列中取出（如果有的话）一条任务（callback）加入执行栈，并执行；

7. 重复第6步。

8. （此步的位置不确定）某个时刻（1000ms后），定时器触发线程通知事件触发线程，事件触发线程将回调函数 `() => { ... }` 加入消息队列队尾，等待JS引擎线程执行。

可以看出，setTimeout异步函数对应的回调函数( `() => {}` )会在执行栈为空，主代码块执行完了后才会执行。

零延时：

```js
console.log('script start')
setTimeout(() => {
  console.log('timer 1 over')
  }, 1000)

setTimeout(() => {
    console.log('timer 2 over')
    }, 0)

console.log('script end')

// script start
// script end
// timer 2 over
// timer 1 over
复制代码
```

这里会先打印 "timer 2 over"，然后打印 "timer 1 over"，尽管 timer 1 先被定时器触发线程处理，但是 timer 2 的callback会先加入消息队列。

上面，timer 2 的延时为 0ms，HTML5标准规定 setTimeout 第二个参数不得小于4（不同浏览器最小值会不一样），不足会自动增加，所以 "timer 2 over" 还是会在 "script end" 之后。

就算延时为 0ms，只是 timer 2 的回调函数会立即加入消息队列而已，回调的执行还是得等执行栈为空（JS引擎线程空闲）时执行。

> 其实 setTimeout 的第二个参数并不能代表回调执行的准确的延时事件，它只能表示回调执行的最小延时时间，因为回调函数进入消息队列后需要等待执行栈中的同步任务执行完成，执行栈为空时才会被执行。

## 宏任务与微任务

以上机制在ES5的情况下够用了，但是ES6会有一些问题。

Promise同样是用来处理异步的：

```js
console.log('script start')
setTimeout(function() {    console.log('timer over')}, 0)

Promise.resolve().then(function() {    console.log('promise1')}).then(function() {    console.log('promise2')})

console.log('script end')

// script start
// script end
// promise1
// promise2
// timer over
复制代码
```

WTF?? "promise 1" "promise 2" 在 "timer over" 之前打印了？

这里有一个新概念：`macrotask`（宏任务） 和 `microtask`（微任务）。

所有任务分为 `macrotask` 和 `microtask`:

- macrotask：主代码块、setTimeout、setInterval等（可以看到，事件队列中的每一个事件都是一个 macrotask，现在称之为宏任务队列）

- microtask：Promise、process.nextTick等

JS引擎线程首先执行主代码块。

每次执行栈执行的代码就是一个宏任务，包括任务队列(宏任务队列)中的，因为执行栈中的宏任务执行完会去取任务队列（宏任务队列）中的任务加入执行栈中，即同样是事件循环的机制。

在执行宏任务时遇到Promise等，会创建微任务（.then()里面的回调），并加入到微任务队列队尾。

microtask必然是在某个宏任务执行的时候创建的，而在下一个宏任务开始之前，浏览器会对页面重新渲染(`task` >> `渲染` >> `下一个task`(从任务队列中取一个))。同时，在上一个宏任务执行完成后，渲染页面之前，会执行当前微任务队列中的所有微任务。

也就是说，在某一个macrotask执行完后，在重新渲染与开始下一个宏任务之前，就会将在它执行期间产生的所有microtask都执行完毕（在渲染前）。

这样就可以解释 "promise 1" "promise 2" 在 "timer over" 之前打印了。"promise 1" "promise 2" 做为微任务加入到微任务队列中，而 "timer over" 做为宏任务加入到宏任务队列中，它们同时在等待被执行，但是微任务队列中的所有微任务都会在开始下一个宏任务之前都被执行完。

> 在node环境下，process.nextTick的优先级高于Promise，也就是说：在宏任务结束后会先执行微任务队列中的nextTickQueue，然后才会执行微任务中的Promise。

执行机制：

1. 执行一个宏任务（栈中没有就从事件队列中获取）

2. 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中

3. 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）

4. 当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染

5. 渲染完毕后，JS引擎线程继续，开始下一个宏任务（从宏任务队列中获取）

## 总结

- JavaScript 是单线程语言，决定于它的设计最初是用来处理浏览器网页的交互。浏览器负责解释和执行 JavaScript 的线程只有一个（所有说是单线程），即JS引擎线程，但是浏览器同样提供其他线程，如：事件触发线程、定时器触发线程等。

- 异步一般是指：
  
  - 网络请求
  - 计时器
  - DOM事件监听

- 事件循环机制：
  
  - JS引擎线程会维护一个执行栈，同步代码会依次加入到执行栈中依次执行并出栈。
  - JS引擎线程遇到异步函数，会将异步函数交给相应的Webapi，而继续执行后面的任务。
  - Webapi会在条件满足的时候，将异步对应的回调加入到消息队列中，等待执行。
  - 执行栈为空时，JS引擎线程会去取消息队列中的回调函数（如果有的话），并加入到执行栈中执行。
  - 完成后出栈，执行栈再次为空，重复上面的操作，这就是事件循环(event loop)机制。

- 微任务和宏任务的区别：
  
  macrotask中的事件都是放在一个事件队列中的，而这个队列由**事件触发线程**维护

- microtask中的所有微任务都是添加到微任务队列（Job Queues）中，等待当前macrotask执行完毕后执行，而这个队列由**JS引擎线程维护**
