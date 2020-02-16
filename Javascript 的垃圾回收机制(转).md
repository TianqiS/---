## Javascript 的垃圾回收机制(转自[掘金@李赫feixuan](https://juejin.im/post/5b684f30f265da0f9f4e87c))

Javascript 会找出不再使用的变量，不再使用意味着这个变量生命周期的结束。Javascript 中存在两种变量——全局变量和局部变量，全部变量的声明周期会一直持续，直到页面卸载

而局部变量声明在函数中，它的声明周期从执行函数开始，直到函数执行结束。在这个过程中，局部变量会在堆或栈上被分配相应的空间以存储它们的值，函数执行结束，这些局部变量也不再被使用，它们所占用的空间也就被释放

但是有一种情况的局部变量不会随着函数的结束而被回收，那就是局部变量被函数外部的变量所使用，其中一种情况就是**闭包**，因为在函数执行结束后，函数外部的变量依然指向函数内的局部变量，此时的局部变量依然在被使用，所以也就不能够被回收

```js
function func1 () {
      const obj = {}
}

function func2 () {
      const obj = {}
      return obj
}

const a = func1()
const b = func2()

```

上面这个例子中，`func1` 执行时为 `obj` 分配了一块内存，但是随着函数执行结束，`obj`占用的空间也就被释放了；而 `func2` 执行时，也为 `obj` 分配了内存，但是由于 `obj` 最终被返回赋值给了 `b` 导致其依然被使用，所以 `func2` 中的 `obj` 占用的内存不会被释放

## 垃圾回收的两种实现方式

垃圾回收有两种实现方式，分别是**标记清除**和**引用计数**

## 标记清楚

当变量进入执行环境时标记为“进入环境”，当变量离开执行环境时则标记为“离开环境”，被标记为“进入环境”的变量是不能被回收的，因为它们正在被使用，而标记为“离开环境”的变量则可以被回收

```js
function func3 () {
      const a = 1
      const b = 2
      // 函数执行时，a b 分别被标记 进入环境
}

func3() // 函数执行结束，a b 被标记 离开环境，被回收

```

## 引用计数

统计引用类型变量声明后被引用的次数，当次数为 0 时，该变量将被回收

```js
function func4 () {
      const c = {} // 引用类型变量 c的引用计数为 0
      let d = c // c 被 d 引用 c的引用计数为 1
      let e = c // c 被 e 引用 c的引用计数为 2
      d = {} // d 不再引用c c的引用计数减为 1
      e = null // e 不再引用 c c的引用计数减为 0 将被回收
}

```

但是引用计数的方式，有一个相对明显的缺点——循环引用

```js
function func5 () {
      let f = {}
      let g = {}
      f.prop = g
      g.prop = f
      // 由于 f 和 g 互相引用，计数永远不可能为 0
}

```

像上面这种情况就需要手动将变量的内存释放

```js
f.prop = null
g.prop = null
```

在现代浏览器中，Javascript 使用的方式是**标记清楚**，所以我们无需担心循环引用的问题

### 什么是内存泄露?

本质上讲, 内存泄露就是不再被需要的内存, 由于某种原因, 无法被释放.  

### 常见的内存泄露案例

**1）全局变量照成内存泄露**

```js
             function fn() {
   		name = "你我贷"
             }
   	     console.log(name)
```

![](https://user-gold-cdn.xitu.io/2018/8/6/1650f7bbc378f70c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  

在 JS 中处理未被声明的变量, 上述范例中的会把 name , 定义到全局对象中, 在浏览器中就是 window 上. 在页面中的全局变量, 只有当页面被关闭后才会被销毁. 所以这种写法就会造成内存泄露, 当然在这个例子中泄露的只是一个简单的字符串, 但是在实际的代码中, 往往情况会更加糟糕.  

另外一种意外创建全局变量的情况.  

```js
                function fn() {
   			this.name = "你我贷"
   		}
   		console.log(name)
```

![](https://user-gold-cdn.xitu.io/2018/8/6/1650f7d9d8f40c31?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  

在这种情况下this被指向了全局变量 window, 意外的创建了全局变量.
我们谈到了一些意外情况下定义的全局变量, 代码中也有一些我们明确定义的全局变量. 如果使用这些全局变量用来暂存大量的数据, 记得在使用后, 对其重新赋值为 null.  

**2）未销毁的定时器和回调函数照成内存泄露**

```js
        function fn() {
    		return 2
    	}
    	var oTxt = fn();
   	setInterval(function() {
	    var oHtml = document.getElementById("test")
	    if(oHtml) {
	        oHtml.innerHTML = oTxt;
	    }
	}, 1000); // 每 1 秒调用一次
```

如果后续 **oHtml **元素被移除, 整个定时器实际上没有任何作用. 但如果你没有回收定时器, 整个定时器依然有效, 不但定时器无法被内存回收, 定时器函数中的依赖也无法回收. 在这个案例中的 **fn**也无法被回收.  

**3 ) 闭包照成内存泄露**

在 JS 开发中, 我们会经常用到闭包, 一个内部函数, 有权访问包含其的外部函数中的变量. 下面这种情况下, 闭包也会造成内存泄露.  

**3）DOM 引用照成内存泄露**

很多时候, 我们对 Dom 的操作, 会把 Dom 的引用保存在一个数组或者 Map 中.

```js
        var elements = {
    		txt: document.getElementById("test")
    	}
    	function fn() {
    		elements.txt.innerHTML = "1111"
    	}
    	function removeTxt() {
    		document.body.removeChild(document.getElementById('test'));
    	}
    	fn();
    	removeTxt()
    	console.log(elements.txt)
```

![](https://user-gold-cdn.xitu.io/2018/8/6/1650f8c9d66d0ca3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  

上述案例中, 即使我们对于 test 元素进行了移除, 但是仍然有对 test 元素的引用, 依然无法对齐进行内存回收.
另外需要注意的一个点是, 对于一个 Dom 树的叶子节点的引用. 举个例子: 如果我们引用了一个表格中的 td 元素, 一旦在 Dom 中删除了整个表格, 我们直观的觉得内存回收应该回收除了被引用的 td 外的其他元素. 但是事实上, 这个 td 元素是整个表格的一个子元素, 并保留对于其父元素的引用. 这就会导致对于整个表格, 都无法进行内存回收. 所以我们要小心处理对于 Dom 元素的引用.
