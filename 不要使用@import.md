#### LINK vs. @import

大家都知道，有两种方法可以在你的页面中导入样式文件。你可以使用LINK标签:

```html
<link href="a.css" rel="stylesheet">
```

或者使用@import 方法：

```css
<style>
@import url('a.css');
</style>
```

我更喜欢使用LINK，因为它比较简单——而如果使用`@import`的话，你必须时刻记得要将`@import`放到样式代码的最前面，否则它将会不起作用。而且事实证明，避免使用`@import` 同样对网站性能有益。

#### @import @import

我将探究LINK和`@import`两种方式的不同。在这些例子中，有两个样式表: `a.css`和`b.css`。每个样式表都配置为需要花费两秒钟来下载，这样就比较容易的看出来它们对网站性能的影响。第一个例子使用`@import` 导入两个样式文件。这个例子，我们称之为[@import @import](http://stevesouders.com/tests/atimport/import-import.php)，HTML代码可以写成这个样子：

```css
<style>
@import url('a.css');
@import url('b.css');
</style>
```

如果你一直这种方式使用`@import`，那么就没有什么性能问题，尽管这可能会因为竞态条件而可能引起JavaScript错误。两个样式文件将同时并行下载，就像在图一中显示的那样(第一个小的请求是HTML该文件) 。问题出现在当`@import`嵌套入其它样式中或者和LINK联合使用的时候。

![](C:\Users\宋大帅\AppData\Roaming\marktext\images\2020-02-06-17-12-21-image.png)

*图一：一直使用@import 是可以的*

#### LINK @import

这个[LINK @import](http://stevesouders.com/tests/atimport/link-import.php)的例子使用LINK加载`a.css`，使用`@import`导入`b.css`:

```html
<link href="a.css" rel="stylesheet" type="text/css">
<style>
@import url('b.css');
</style>
```

在IE中(在6, 7, 和8中测试过)，这会导致样式表文件逐个加载，正如图二所示。并行下载资源是加速页面的一个关键。就像图示的那样，这种方法在IE中会导致页面需要更多的时间才能加载完成。

![](C:\Users\宋大帅\AppData\Roaming\marktext\images\2020-02-06-17-13-02-image.png)

图二. 在IE中link混合@import 会破坏并行下载

#### LINK嵌套@import

在这个[LINK 嵌套@import](http://stevesouders.com/tests/atimport/link-with-import.php) 例子中，`a.css` 通过LINK插入到页面中，然后`a.css` 通过`@import`规则来引入`b.css`:

HTML代码:

```html
<link href="a.css" rel="stylesheet" type="text/css">
```

在a.css中:

```css
@import url('b.css');
```

这种方式同样阻止并行加载代码，但是这次是对于所有的浏览器。其实这个应该不会让我们感到奇怪吧，简单的想一下就能理解了。浏览器必须下载`a.css`先，并分析它，这个时候，浏览器发现了`@import` 规则，然后才会开始加载`b.css`.

 ![](C:\Users\宋大帅\AppData\Roaming\marktext\images\2020-02-06-17-13-28-image.png)
图三. 在在一个通过LINK加载的的样式文件中使用`@import`将会在所有的浏览器里面打破并行下载。

#### LINK 阻断 @import

上面的例子做一个细微的变化，IE中会引起惊人的结果：使用LINK导入`a.css` 和一个新的样式文件`proxy.css`。`proxy.css`没有添加额外的样式，它只是用来通过`@import` 规则导入`b.css`.

HTML代码如下：

```html
<link href="a.css" rel="stylesheet" type="text/css">
    
<link href="proxy.css" rel="stylesheet" type="text/css">
```

`proxy.css`的代码:

```css
@import url('b.css');
```

这个例子在IE中运行的结果，[LINK 阻断@import](http://stevesouders.com/tests/atimport/link-blocks-import.php)，在图四中显示。第一个请求是HTML文档。第二个请求是`a.css` (花了两秒钟)，第三个(很小) 的请求是`proxy.css`。第四个请求是`b.css` (也花费了两秒钟)。令人震惊的是，在下载`a.css`完成之前，IE不会开始下载`b.css`。但是在其它所有的浏览器中，这种情况不会发生，结果页面显示的也比较快。如下图五所示。

 ![](C:\Users\宋大帅\AppData\Roaming\marktext\images\2020-02-06-17-14-04-image.png)
图四. IE中，LINK 阻断使用`@import`嵌入的其它样式文件。

![](C:\Users\宋大帅\AppData\Roaming\marktext\images\2020-02-06-17-14-12-image.png) 
图五. 在非IE浏览器中，LINK不会阻断`@import` 嵌入样式表。

#### 多个@imports

这个[使用多个@imports](http://stevesouders.com/tests/atimport/many-imports.php)的例子展示在IE中使用`@import`会引起资源被按照一个不同于预期的顺序下载。这个例子有6个样式表(每个将花两秒钟的下载时间)以及后面跟着一个js脚本文件(需要四苗种下载)。

```css
<style>
@import url('a.css');@import url('b.css');@import url('c.css');@import url('d.css');@import url('e.css');@import url('f.css');
</style>
<script src="one.js" type="text/javascript"></script>
```

看一下图六，最长的条条是耗时四秒钟的脚本。尽管它在代码里面被列在最后，但是在IE中，它被首先下载。如果脚本中包含的代码以来从样式表文件中应用的样式(比如`getElementsByClassName`)， 那么就将可能会发生意外的结果，因为脚本先于样式被加载，尽管开发人员将其置于代码的最后面。

![](C:\Users\宋大帅\AppData\Roaming\marktext\images\2020-02-06-17-14-33-image.png) 
图六 `@import`在IE中引发资源文件的下载顺序被打乱

#### LINK LINK

使用LINK来引入样式更简单和安全：

```html
<link href="a.css" rel="stylesheet" type="text/css">
    
<link href="b.css" rel="stylesheet" type="text/css">
```

使用LINK 可确保样式在所有浏览器里面都能被并行下载。这个[LINK LINK](http://stevesouders.com/tests/atimport/link-link.php)的例子演示了这一点，就像在图七中显示的那样。使用LINK 同样能保证资源按照开发人员制定的顺序下载。

![](C:\Users\宋大帅\AppData\Roaming\marktext\images\2020-02-06-17-14-49-image.png) 
图七：使用LINK确保在所有的浏览器里面都能并行下载

这些问题都需要考虑到IE。它非常不好的地方是，资源文件可能会在个别地方结束下载，所有浏览器在下载样式文件的时候应该执行一些前瞻以导入所有的`@import`规则并立即下载它们（通过`@import`导入的样式）。知道所有的浏览器都变成这种方式，我都会推荐避免使用`@import`并一直使用LINK 来插入样式。
