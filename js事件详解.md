**1.事件流**

一般情况下，每个事件的触发，都会产生3个阶段，分别是事件捕获阶段，处于目标阶段，事件冒泡阶段。比较特别的是，IE浏览器没有事件捕获阶段。

![](https://pic1.zhimg.com/80/v2-318f37c7c7c43a6a7bc7d87dd8f7430c_hd.jpg)

事件捕获阶段就是当事件触发时，先从DOM树顶层开始进行事件捕获，直到事件触发到达了事件源元素。事件冒泡阶段是，该事件会随着DOM树的层级路径，由子节点向父节点进行层层传递，直至到达document。

那么，我们平时绑定的事件可以绑定在事件捕获阶段，也可以绑定在事件冒泡阶段

按类型划分，事件绑定类型有3种：

（1）DOM0级：

document.onclick=function(){} //绑定

document.onlick = null; //移除

注意：内部的this对象总是指向触发事件的那个节点。

（2）DOM2级：

document.addEventListener(function(){},false) ;//绑定

document.removeEventListner(function(){},false);//移除

注意：内部的this对象总是指向触发事件的那个节点。

（3）特别的IE事件处理：

dom.attachEvent('onclick',function(){ });//绑定

detachEvent('onclick',function(){});//移除

注意：IE在使用attachEvent方法的情况下，事件处理程序的作用域为全局作用域，因此this等于window。

按方式划分，事件绑定的方式有3种:

（1）作为属性，写在html元素上

（2） document.onclick=function(){}

（3）document.addEventListener(function(){},false) //推荐的标准事件模式

前面两种方式是绑定在事件冒泡阶段的，这个是事件内部决定的，我们外部改变不了。第三种方式是可以改变事件绑定的阶段的，注意到第二个参数，当它为false时，绑定在事件冒泡阶段（默认下是绑定在冒泡阶段）。当它为true时，绑定在捕获阶段，那么绑定在不同阶段会有什么不同的表现呢，如果绑定在捕获阶段，监听函数就只在捕获阶段触发，如果绑定在冒泡阶段，监听函数只在冒泡阶段触发。

**2.绑定元素和目标元素**

绑定元素和目标元素是两个不一样的概念.绑定元素是把事件处理函数绑定在这上面，目标元素是用户操作界面，用户触发的元素。

**3.事件的执行顺序**

（1）无论是哪种绑定方式，对于同一个绑定元素，都是遵循先绑定的先执行原则。

（2）如果是以onclick的方式绑定的，如果对同一个元素重复绑定的话，后面的会覆盖前面的。但是如果是以addEventLisener方式绑定的话，同一个元素绑定多少次，就会执行多少次。

（3）如果在DOM中直接使用onclick ，则onclick的绑定是早于 addEventListener 的。

**4.事件委托**

事件委托是为了减少绑定元素个数和事件处理函数而产生的，绑定在父级元素，利用事件冒泡去触发父级事件处理函数的一种技巧。Jquery的on事件，就是用的事件委托。如果我们这样写

```text
$(document).on("click",".box",function(){})
```

这个on事件是绑定在document上面的，box是目标元素，on事件内部是通过e.target来判断点击元素是不是box的，所以说，如果你在这个处理函数里面阻止事件冒泡，阻止的是哪个元素？当然是document啊，所以是没什么用的，因为document已经是最顶级的元素了.所以，要解决我上面的那个坑，有两个方法，一种是直接绑定在目标元素上，一种是还是绑定在document上面，但是可以通过e.target去判断目标元素是不是你想要的。

**5.事件对象event**

event.target：指的是触发事件的那个节点，即事件最初发生的节点。在IE6—IE8之中，该属性的名字不是target，而是srcElement。所以兼容IE的时候，要注意。

event.currentTarget：指的是正在执行的监听函数所绑定的那个节点。

event.path：指的是事件冒泡的顺序。

event.timeStamp：指的是从事件绑定完成到此次事件触发的时间，单位是毫秒。

event.type：事件的类型，比如click，input

event.isTrusted：表示事件是否是真实用户触发，true表示是真实用户触发，false表示脚本触发。

event.preventDefault()：取消事件的默认行为，比如 a标签 默认跳转到一个新网址，如果阻止默认行为，就不会跳转。

event.stopPropagation()：当有event对象时，阻止事件冒泡。

window.event.cancelBubble = true：当没有event对象时，阻止事件冒泡（一般用在IE浏览器）。

event.stopImmediatePropagation()：阻止同一个事件的其他监听函数被调用。比如对同一个元素绑定了两个click事件，如果在第一个click事件写了event.stopImmediatePropagation()，那么它的其他点击事件就都不会被触发。
