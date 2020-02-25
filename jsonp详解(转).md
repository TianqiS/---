## jsonp跨域详解（转自[简书@公子七](https://www.jianshu.com/p/e1e2920dac95)）

### 0. 前言

说到AJAX就会不可避免的面临两个问题。

- AJAX以何种格式来交换数据？
- 第二个是跨域的需求如何解决？

这两个问题目前都有不同的解决方案，比如数据可以用自定义字符串或者用XML来描述，跨域可以通过服务器端代理来解决。  
但到目前为止最被推崇或者说首选的方案还是用**JSON来传数据，靠JSONP来跨域**。而这就是本文将要讲述的内容。  
JSON和JSONP虽然只有一个字母的差别，但其实他们根本不是一回事儿：**JSON是一种数据交换格式，而JSONP是一种依靠开发人员的聪明才智创造出的一种非官方跨域数据交互协议。**我们拿最近比较火的谍战片来打个比方，JSON是地下党们用来书写和交换情报的“暗号”，而JSONP则是把用暗号书写的情报传递给自己同志时使用的接头方式。看到没？**一个是描述信息的格式，一个是信息传递双方约定的方法。**

### 1. 什么是JSON？

JSON是JavaScript Object Notation的缩写，它是一种数据交换格式。在JSON中，一共就这么几种数据类型：

- number
- boolean
- string
- null
- array
- object

以及上面的任意组合。  
并且，JSON还定死了字符集必须是**UTF-8**，表示多语言就没有问题了。  
由于JSON非常简单，很快就风靡Web世界，并且成为ECMA标准。几乎所有编程语言都有解析JSON的库，而在JavaScript中，我们可以直接使用JSON，因为JavaScript内置了JSON的解析。

### 2. 什么是JSONP？

#### 2.1 先说说JSONP是怎么产生的

1. 一个众所周知的问题，AJAX直接请求普通文件存在跨域无权限访问的问题，甭管你是静态页面、动态网页、web服务、WCF，只要是跨域请求，一律不准；

2. 不过我们又发现，**Web页面上调用js文件时则不受是否跨域的影响**（不仅如此，我们还发现凡是拥有`src`这个属性的标签都拥有跨域的能力，比如`<script>`、`<img>`、`<iframe>`)；

3. 于是可以判断，当前阶段如果想通过纯web端（ActiveX控件、服务端代理、Web socket等方式不算）跨域访问数据就只有一种可能，那就是在远程服务器上设法把数据装进js格式的文件里，供客户端调用和进一步处理；

4. 恰巧我们已经知道有一种叫做JSON的纯字符数据格式可以简洁的描述复杂数据，更妙的是JSON还被JS原生支持，所以在客户端几乎可以随心所欲的处理这种格式的数据；

5. 这样子解决方案就呼之欲出了，web客户端通过与调用脚本一模一样的方式，来调用跨域服务器上动态生成的js格式文件（一般以JSON为后缀），显而易见，服务器之所以要动态生成JSON文件，目的就在于把客户端需要的数据装入进去。

6. 客户端在对JSON文件调用成功之后，也就获得了自己所需的数据，剩下的就是按照自己需求进行处理和展现了，这种获取远程数据的方式看起来非常像AJAX，但其实并不一样。

7. 为了便于客户端使用数据，逐渐形成了一种非正式传输协议，人们把它称作JSONP，该协议的一个要点就是**允许用户传递一个`callback`参数给服务端，然后服务端返回数据时会将这个`callback`参数作为函数名来包裹住JSON数据，这样客户端就可以随意定制自己的函数来自动处理返回数据了**。

如果对于`callback`参数如何使用还有些模糊的话，我们后面会有具体的实例来讲解。

#### 2.2 JSONP的客户端具体实现

不管jQuery也好，extjs也罢，又或者是其他支持JSONP的框架，他们幕后所做的工作都是一样的，下面我来循序渐进的说明一下JSONP在客户端的实现。  
**1）**我们知道，哪怕跨域js文件中的代码（当然指符合web脚本安全策略的），web页面也是可以无条件执行的。  
远程服务器`remoteserver.com`根目录下有个`remote.js`文件代码如下：

```bash
alert('我是远程文件');
```

本地服务器`localserver.com`下有个`jsonp.html`页面代码如下：

```xml
<!DOCTYPE html>
<html>
  <head>
    <title></title>
    <script type="text/javascript" src="http://remoteserver.com/remote.js"></script>
  </head>
  <body></body>
</html>
```

毫无疑问，页面将会弹出一个提示窗体，显示跨域调用成功。

**2）**现在我们在`jsonp.html`页面定义一个函数，然后在远程`remote.js`中传入数据进行调用。  
`jsonp.html`页面代码如下：

```xml
<!DOCTYPE html>
<html>
  <head>
    <title></title>
    <script type="text/javascript">
      var localHandler = function(data){
          alert('我是本地函数，可以被跨域的remote.js文件调用，远程js带来的数据是：' + data.result);
      };
    </script>
    <script type="text/javascript" src="http://remoteserver.com/remote.js"></script>
  </head>
  <body></body>
</html>
```

`remote.js`文件代码如下：

```bash
localHandler({  "result":"我是远程js带来的数据"});
```

运行之后查看结果，页面成功弹出提示窗口，显示本地函数被跨域的远程js调用成功，并且还接收到了远程js带来的数据。很欣喜，跨域远程获取数据的目的基本实现了，但是又一个问题出现了，**怎么让远程js知道它应该调用的本地函数叫什么名字呢**？毕竟是JSONP的服务者都要面对很多服务对象，而这些服务对象各自的本地函数都不相同啊？我们接着往下看。

**3）**聪明的开发者很容易想到，只要服务端提供的js脚本是动态生成的就行了呗，这样调用者可以传一个参数过去告诉服务端“我想要一段调用XXX函数的js代码，请你返回给我”，于是服务器就可以按照客户端的需求来生成js脚本并响应了。

看`jsonp.html`页面的代码：

```xml
<!DOCTYPE html>
<html>
  <head>
    <title></title>
    <script type="text/javascript">
      // 得到航班信息查询结果后的回调函数
      var flightHandler = function(data){
          alert('你查询的航班结果是：票价 ' + data.price + ' 元，' + '余票 ' + data.tickets + ' 张。');
      };
      // 提供jsonp服务的url地址（不管是什么类型的地址，最终生成的返回值都是一段javascript代码）
      var url = "http://flightQuery.com/jsonp/flightResult.aspx?code=CA1998&callback=flightHandler";
      // 创建script标签，设置其属性
      var script = document.createElement('script');
      script.setAttribute('src', url);
      // 把script标签加入head，此时调用开始
      document.getElementsByTagName('head')[0].appendChild(script); 
    </script>
  </head>
  <body></body>
</html>
```

这次的代码变化比较大，不再直接把远程js文件写死，而是编码实现**动态查询**，而**这也正是JSONP客户端实现的核心部分**，本例中的重点也就在于如何完成JSONP调用的全过程。  
我们看到调用的`url`中传递了一个`code`参数，告诉服务器我要查的是CA1998次航班的信息，而`callback`参数则告诉服务器，我的本地回调函数叫做`flightHandler`，所以请把查询结果传入这个函数中进行调用。  
OK，服务器很聪明，这个叫做`flightResult.aspx`的页面生成了一段这样的代码提供给`jsonp.html`（服务端的实现这里就不演示了，与你选用的语言无关，说到底就是拼接字符串）：

```bash
flightHandler({  "code": "CA1998",  "price": 1780,  "tickets": 5});
```

我们看到，传递给`flightHandler`函数的是一个`json`，它描述了航班的基本信息。运行一下页面，成功弹出提示窗口，JSONP的执行全过程顺利完成！

#### 2.3 总结原生JS实现JSONP的步骤

##### 2.3.1 客户端

1. 定义获取数据后调用的回调函数
2. 动态生成对服务端JS进行引用的代码
   - 设置`url`为提供`jsonp`服务的`url`地址，并在该`url`中设置相关`callback`参数
   - 创建`script`标签，并设置其`src`属性
   - 把`script`标签加入`head`，此时调用开始。

##### 2.3.2 服务端

将客户端发送的`callback`参数作为函数名来包裹住`JSON`数据，返回数据至客户端。

#### 2.4 JSONP在jQuery中的具体实现

在jQuery中实现JSONP主要有两种方式。

- $.getJSON
- $.ajax

##### 2.4.1 $.getJSON实现方式

```xml
<script type="text/javascript" src="jquery.js"></script>  
<script type="text/javascript">  
    $.getJSON("http://crossdomain.com/services.php?callback=?", function(json){
        alert('您查询到航班信息：票价： ' + json.price + ' 元，余票： ' + json.tickets + ' 张。');
    });  
</script>
```

##### 2.4.2 $.ajax实现方式：

```xml
<script type="text/javascript" src=jquery.min.js"></script>
<script type="text/javascript">
     $(document).ready(function(){ 
        $.ajax({
             type: "get",
             url: "http://flightQuery.com/jsonp/flightResult.aspx?code=CA1998",
             dataType: "jsonp",
             jsonp: "callback",//传递给请求处理程序或页面的，用以获得jsonp回调函数名的参数名(一般默认为:callback)
             jsonpCallback:"flightHandler",//自定义的jsonp回调函数名称，默认为jQuery自动生成的随机函数名，也可以写"?"，jQuery会自动为你处理数据
             success: function(json){
                 alert('您查询到航班信息：票价： ' + json.price + ' 元，余票： ' + json.tickets + ' 张。');
             },
             error: function(){
                 alert('fail');
             }
         });
     });
</script>
```

是不是有点奇怪？为什么我这次没有写`flightHandler`这个函数呢？而且竟然也运行成功了！哈哈，这就是jQuery的功劳了，jquery在处理JSONP类型的ajax时（还是忍不住吐槽，虽然jQuery也把jsonp归入了ajax，但其实它们真的不是一回事儿），自动帮你生成回调函数并把数据取出来供`success`属性方法来调用。

### 3. JSONP与GET / POST 请求

我们知道`script`，`link`，`img` 等标签引入外部资源，都是`GET`请求的，那么就决定了 **JSONP 一定是GET**的。即便在`$.ajax()`中使用`POST`请求也能成功。一旦当我们指定`dataType: 'jsonp'`，不管指定`type`的值是什么（`GET`/`POST`/甚至不写），都会进行`GET`请求。  
**这是jQuery在封装JSONP跨域时就已经写死了的。**  
对应的源码如下：

```jsx

```

**`if( s.crossDomain){ s.type = "GET"; ...}`**这里就是真相~ 在ajax的过滤函数中，只要是跨域，jQuery就将其`type`设置成`GET`，真是那句话：在源码面前，一切了无秘密~ jQuery源码我自己很多地方读不懂，但是并不妨碍我们去读，去探索~

### 4. AJAX与JSONP的异同：

1. AJAX和JSONP这两种技术在调用方式上“看起来”很像，目的也一样，都是请求一个`url`，然后把服务器返回的数据进行处理，因此jQuery和extjs等框架都把JSONP作为AJAX的一种形式进行了封装；

2. 但AJAX和JSONP其实本质上是不同的东西。**AJAX的核心是通过`XmlHttpRequest`获取非本页内容，而JSONP的核心则是动态添加`<script>`标签来调用服务器提供的js脚本。**

3. 所以说，其实AJAX与JSONP的**区别不在于是否跨域**，AJAX通过服务端代理一样可以实现跨域，JSONP本身也不排斥同域的数据的获取。

4. 还有就是，JSONP是一种方式或者说非强制性协议，如同AJAX一样，它也不一定非要用JSON格式来传递数据，如果你愿意，字符串都行，只不过这样不利于用JSONP提供公开服务。

5. 总而言之，JSONP不是AJAX的一个特例，哪怕jQuery等巨头把它封装进了AJAX，也不能改变这一点！


