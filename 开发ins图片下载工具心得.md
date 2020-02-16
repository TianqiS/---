# 使用nodejs开发ins图片下载工具心得

## 开发思路

首先通过ins的app来复制链接地址，之后通过链接地址的解析获取到网页的html内容，再找到img标签对应的src即可

复制到的链接地址格式如下所示

> [https://www.instagram.com/p/B7I1a89gta5/?igshid=unh7bi7a964d](https://www.instagram.com/p/B7I1a89gta5/?igshid=unh7bi7a964d)

![ ](C:\Users\宋大帅\Pictures\qq截图\QQ截图20200119165848.png)

在浏览器上访问的结果是这个样子的，看起来问题不大。

然而，如果将这个链接地址通过postman来直接访问获取的内容确实这样的：

![ ](C:\Users\宋大帅\Pictures\qq截图\QQ截图20200119170118.png)

获取到的内容全部都是js代码和一些外链的css和js文件，没有办法直接获取原本网页的html代码内容。

## 解决方法

查过排查之后，发现ins的网站采用了一种叫做**JavaScript动态加载页面**的方式来渲染网页，采用这样的技术的网页具有一定的反爬虫技术，因此如果我们直接访问网页链接的话get到的html代码并不是我们想要的代码。

这个时候，可以使用过一个nodejs的一个叫做**Puppeteer**的包。

下面是puppeteer的介绍:

> Puppeteer本质上是一个chrome浏览器，只不过可以通过代码进行各种操控。比如模拟鼠标点击、键盘输入等操作，有点像按键精灵，网页很难分清这是人类用户还是爬虫，所以限制也就无处谈起。

puppeteer是通过代码来模拟出的一个浏览器，好处和坏处都很明显，好处是比较方便快捷，坏处就是比较耗用资源，因为每次都要启动一个浏览器，所以并不适合太大的项目，但是在这个项目中使用应该是没有问题的。

以下是获取图片的接口:

```javascript
router.get('/downloadPic', async (ctx, next) => {
    const url = ctx.query.url
    const browers = await puppeteer.launch()
    const page = await browers.newPage()

    await page.goto(url)
    let imgElement = await page.$eval('.FFVAD', (imgEle) => {
        return imgEle.src
    })
    ctx.redirect(imgElement)
})
```

## 在使用Puppeteer中踩得坑

在使用puppeteer中注意到一个很有意思的事情

```javascript
router.get('/downloadPic', async (ctx, next) => {
 const url = ctx.query.url
 const browers = await puppeteer.launch()
 const page = await browers.newPage()
 await page.goto(url)
 let imgElement = await page.$eval('.FFVAD', (imgEle) => {
 console.log("test")
 return imgEle.src
 })
 console.log(imgElement)
 ctx.body = 123
 })
```

上述代码理论上的输出应该是这样的：

```javascript
//test
//imgElement
```

然而实际上只输出了`imgElement` ，一开始我以为是webstorm自身的bug，但是经历过很多的测试发现怎么样都没有办法打印出`"test"` ，后来查阅官方文档才发现`page.eval`这个方法传递的function实际上是要浏览器这个对象来执行的function，而非是服务端所执行的function，这就是`console.log`无法正确执行的原因，以下是文档摘录：

> #### page.$$eval(selector, pageFunction[, ...args])v0.12.0
> 
> - `selector` <[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#String_type "String")> A [selector](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors "selector") to query page for
> - `pageFunction` <[function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function "Function")([Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array "Array")<[Element](https://developer.mozilla.org/en-US/docs/Web/API/element "Element")>)><span style="color:red"> Function to be evaluated in browser context</span>
> - `...args` <...[Serializable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#Description "Serializable")|[JSHandle](https://pptr.dev/#?product=Puppeteer&version=v2.0.0&show=api-class-jshandle "JSHandle")> Arguments to pass to `pageFunction`
> - returns: <[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise "Promise")<[Serializable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#Description "Serializable")>> Promise which resolves to the return value of `pageFunction`

## 关于eval function的理解

笔者认为eval这个function实际上是在已经创建的浏览器的对象中来执行响应的`pageFunction` 来达到获取document相关信息的目的。

这个function实际上就等同于在浏览器中执行`document.querySelector()`这个function

![ ](C:\Users\宋大帅\Pictures\qq截图\QQ截图20200120003808.png)

上图中代码实现的功能相当于下面代码所实现的功能

```javascript
let imgElement = await page.$eval('.FFVAD', (imgEle) => {
 return imgEle.src
 })
```

----

- centos安装nginx [https://segmentfault.com/a/1190000021522530](https://segmentfault.com/a/1190000021522530)

- 我的appSecret d6cf1fbe32f3e1b7168d0d9aa21d5b5b


