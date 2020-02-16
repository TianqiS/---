# js执行顺序Event Loop(转自[掘金](https://juejin.im/post/5aba1284f265da238e0dc436))

javascript是一门单线程语言，为了实现主线程的不阻塞，但可以用Event Loop模拟多线程操作 Event Loop中同步异步任务执行顺序：

- 所有异步任务都是在Event Table中注册函数，当指定的时间完成时，Event Table会将函数放入Event Queue，主线程的同步任务执行完会去Event Queue读取对应函数，进入主线程执行。

![image](https://user-gold-cdn.xitu.io/2018/3/27/16266d7f081601b3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

js引擎monitoring process进程，当发现主进程执行栈为空，会去执行Event Queue中的函数

```
let data = [];$.ajax({    url:url    data:data,    success:() => {        console.log('发送成功!');    }})
console.log('代码执行结束');

复制代码
```

上面是ajax执行顺序： ajax进入Event Table，注册回调函数success。 执行console.log('代码执行结束')。 ajax事件完成，回调函数success进入Event Queue。 主线程从Event Queue读取回调函数success并执行。

除了广义的同步任务和异步任务，我们对任务有更精细的定义：

macro-task(宏任务)：：setTimeout、setInterval、setImmediate、I/O、UI交互事件 micro-task(微任务)：Promise、process.nextTick、MutaionObserver

```js
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})


```

整段代码，共进行了三次事件循环，完整的输出为1，7，6，8，2，4，3，5，9，11，10，12。 (请注意，node环境下的事件监听依赖libuv与前端环境不完全相同，输出顺序可能会有误差)
