# JS中一切都是对象吗？看这一篇就知道了（转自[掘金](https://juejin.im/post/5cd3e5daf265da03612f0271)）

当你刚开始学习JavaScript时，你是否有遇到许多书籍，教程以及那些说“JavaScript中的所有内容都是对象”的人？这算是一个JavaScript中老生常谈的话题了，虽然它并不是100％正确（JavaScript中不是所有都是对象），但其实这种说法又有那么一点说得过去，这可能有点自相矛盾。

![](https://user-gold-cdn.xitu.io/2019/5/9/16a9bba151961d98?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

想必有点疑惑吧，那么造成这种现象的原因是什么呢？接下来就由我一一道来。

### 历史原因

说起一切都是对象这种说法的由来，就要好好提一下历史因素了。

1995年，JavaScript诞生之年，Netscape公司（JavaScript的设计者）与Sun公司（Java语言的发明者和所有者）合作开发一种可以嵌入网页的脚本语言，将JavaScript的数据结构借鉴Java而设计，包括将值分成原始值和对象两大类。

**Java中一切皆对象，但基本类型却不是对象，为了解决这个问题，Java让每个基本类型都对应了一个包装器类型**。包装器类型将基本类型包装起来，添加了属性和方法，包装器类型即为对象，所以可以这么说Java中的一切都可以充当对象，不会说的那么绝对。

因此借鉴了Java数据结构的JavaScript也同样在基本类型中各对应了一个包装器类型，JavaScript中的一切都可以充当对象，接下来将详细进行介绍。

### 原始类型与对象

JavaScript中值可以分为两大类：原始类型和对象。

#### 定义

在JavaScript中，有六种原始数据类型：

- Boolean - `true` 或 `false`
- null - 用 `type of` 检验 `null` 数据类型时为 `Object` ，但它不是对象，这是JS的一个bug
- undefined
- number - JavaScript中的所有数字都是浮点数，没有整数
- string
- symbol (ES6)

除去上面的原始数据类型，所有其他值都是对象。对象可以进一步分为：

- 原始值的包装类型：`Boolean`,`Number`,`String`. - 很少直接使用。

- 以下类型生成的对象也可以通过构造函数创建：
1. [] 类同于 new Array()

2. {} 类同于 new Object()

3. function() {} 类同于 new Function()

4. /\s*/ 类同于 new RegExp("\s*")
- Dates: new Date("2011-12-24")

#### 不同点

第一个不同点：

原始类型没有附加方法; 所以你永远不会看到`undefined.toString（）`。 也正因为如此，原始类型是不可变的，因为它们没有附加的方法可以改变它：

```js
var s = "boy";
s.bar = "girl";
console.log(s.bar); // undefined

```

而默认情况下，对象是可变的，可以添加方法：

```js
var obj = {};
obj.foo = 123;  
console.log(obj.foo); // 123  

```

第二个不同点：

此外，与作为引用存储的对象不同，原始类型作为值本身存储。 这在执行相等性检查时会产生影响：

```js
"dog" === "dog"; // true
14 === 14; // true

{} === {}; // false
[] === []; // false
(function () {}) === (function () {}); // false

```

原始类型按值存储，对象通过引用存储，存储地址也不同，原始类型直接存放在栈中，而对象是存放在堆里的，具体可以看之前写的关于[内存空间](https://luozongmin.com/2019/04/22/JavaScript%E7%B3%BB%E5%88%97%E4%B9%8B%E5%86%85%E5%AD%98%E7%A9%BA%E9%97%B4/)这篇文章。

### 原始值及其包装器

三个基本类型`string`，`number`和`boolean`，它们有时被当做包装器类型，并且在原始值和包装类型之间进行转换很简单：

- 原始类型 to 包装类型: `new String("abc")`
- 包装类型 to 原始类型: `new String("abc").valueOf()`

比如字符串`“abc”`之类的原始值与`new String（“abc”）`之类的包装器实例有根本上的不同。 例如（用`typeof`和`instanceof`判断时）：

```js
typeof "pet";  //"string"
typeof new String("pet");  //"object"

"pet" instanceof String;  // false
new String("pet") instanceof String;  // true

"pet" === new String("pet");  // false
复制代码
```

其实包装器实例就是一个对象，没办法在JavaScript中比较对象，甚至不能通过非严格相等 ==：

```js
var a = new String("pet");
var b = new String("pet");a == b;  // false
a == a;  // true
复制代码
```

因此JavaScript中的一切都可以充当对象，而JavaScript中的一切都是对象这种说法是欠妥的。

### 临时包装（Auto-Boxing）

有趣的是，原始字符串和对象的构造函数都是`String`函数。 更有趣的是你可以在原始字符串上调用`.constructor`这个方法，可是之前说过原始类型不能有方法，咋回事呢？先看下面的代码：

```js
var pet = new String("dog")
pet.constructor === String; // true
String("dog").constructor === String; // true
```

上面代码所发生的事情是一个叫做Auto-Boxing的过程，我觉得翻译成中文是“临时包装”比较适宜。 当您尝试在某些基本类型上调用属性或方法时，JavaScript首先将其转换为临时包装器对象，并访问其上的属性/方法，而不会影响原始属性。

```js
var pet = "dog";
console.log(pet.length); // 3
pet === "dog"; // true
```

在上面的示例中，要访问属性`length`，JavaScript发生临时包装过程将`pet`转换为包装器对象，访问完包装器对象的`length`属性，然后将其丢弃。 这样做不会影响`pet`（`pet`仍然是一个原始字符串）。

这也解释了为什么JavaScript在尝试将属性分配给基本类型时不会出问题，因为赋值是在该临时包装器对象上完成的，而不是基本类型本身，比如：

```js
var foo = 42;
foo.bar = "lzm"; // 是在临时包装器对象上完成的赋值
foo.bar; // undefined
```

但原始类型`undefined`和`null`，都是没有包装器对象的，当你尝试赋予属性时，它会报错。

```js
var foo = null;
foo.bar = "lzm"; // Uncaught TypeError: Cannot set property 'bar' of null
```

#### 小结

1.并非JavaScript中的所有内容都是对象，应该说所有内容都可以充当对象。

2.JavaScript中有6种原始类型。

3.所有不是原始类型的值都是一个对象。

4.字符串，布尔值和数字可以表示为基本类型，但作为包装器类型时也可以表示为对象。

5.由于名为autoboxing的JavaScript特性，某些原始类型（字符串，数字，布尔值）似乎表现得有点像对象。


