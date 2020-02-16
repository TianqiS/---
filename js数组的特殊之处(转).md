# 面试官：JavaScript的数组有什么特殊之处？（转自掘金[@寻找海蓝96](https://juejin.im/post/5d2832a5f265da1bc07e669e)）

数组是前端开发者最常用的数据结构了，我们在项目中无时不刻在操作着数组，例如将列表组件的数据储存在数组里、将需要渲染成条形图的数据同样储存在一个数组里，虽然我们经常使用数组，但是很多人并不了解JavaScript数组的本质。

本节我们将从JavaScript数组的使用、内存模型两大部分进行讲解，希望通过这个小节，让大家对JavaScript的数组有更深的认识。

> 在正是开始这节之前，请大家思考一个问题，JavaScript的数组有什么特殊之处？

## 数组的使用

数组是我们最常用的数据结构，很多基于数组的操作大家也足够熟悉了，我们不会在这里罗列数组的API，因为[MDN数组](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)这一部分足够权威也足够全面，我们会简单介绍下重点的数组方法，为接下来的内容做铺垫。

### 数组的创建与初始化

如果你之前学过其它语言类似于c++/java等，你可能会用一下方法创建并初始化一个数组:

```js
const appleMac = new Array('Mac Book Air', 'iMac', 'Mac Book Pro', 'Mac pro')
```

当然这在JavaScript中是可以的，但并不主流方法，通常人们创建并初始化数组用的是字面量的方式:

```js
const appleMac = ['Mac Book Air', 'iMac', 'Mac Book Pro', 'Mac pro']
```

在es6中引入了两个新方法，同样可以创建数组：

- Array.of() 返回由所有参数组成的数组，不考虑参数的数量或类型，如果没有参数就返回一个空数组
- Array.from()从一个类数组或可迭代对象中创建一个新的数组

这两个方法分别解决了两个问题，`Array.of()`解决了构造函数方法创建数组时单个数字引起了怪异行为。

```js
const a = new Array(3);   // (3) [empty × 3] 构造函数方法单个数组会被用于数组长度
const b = Array.of(3);    // [3]
```

`Array.from()`解决了『类数组』的转化问题，之前我们将类数组转化为数组的方法普遍用的是`Array.prototype.slice.call(arguments)`这种偏Hack的方法，`Array.from()`的出现将其规范化，在以后的转化中我们最好按照标准的`Array.from()`方法进行转化。

### 数组的操作

数组的操作有数十种之多，我们不可能一一讲到，具体使用也可以看MDN，我们只讲两个对本节比较重要的api。

#### 向头部插入元素

unshift操作是最常见的向数组头部添加元素的操作

```js
const arr = [1, 2, 3]

arr.unshift(0) // arr = [0, 1, 2, 3,]
```

#### 向尾部插入元素

push操作是最常见的向数组尾部添加元素的操作

```js
const arr = [1, 2, 3]

arr.push(4) // arr = [1, 2, 3, 4]
```

## 内存模型

编程语言的内存通常要经历三个阶段

1. 分配内存
2. 对内存进行读、写
3. 释放内存（垃圾回收）

数组的创建对应着第一阶段，数组的操作对应着第二阶段。

因此，现在有一个问题，我们分别用push和unshift往数组的尾部和头部添加元素，谁的速度更快？

### 连续内存

如果你比较了解相关数据结构内存的话应该会知道，数组是会被分配一段连续的内存，如图:

![2019-06-18-10-21-29](https://user-gold-cdn.xitu.io/2019/7/12/16be505d064f66c6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

那么当我们向这个数组最后`push`元素6的时候，只需要将后面的一块内存分配给6即可。

而unshift则不同，因为是向数组头部添加元素，数组为了保证连续性，头部之后的元素需要依次向后移动。

unshift的本质类似于下面的代码:

```js
for (var i=numbers.length; i>=0; i--){
      numbers[i] = numbers[i-1];
    }
    numbers[0] = -1;
```

![2019-06-18-10-30-59](https://user-gold-cdn.xitu.io/2019/7/12/16be505d06628139?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

由于unshift出发了所有元素内存后移，导致性能远比push要差。

我在node10.x版本下作了一个实验:

```js
function unshiftFn() {
    const a = []

    console.time('unshift')
    for (var i=0;i<100000;i++) {
        a.unshift(1);
    }

    console.timeEnd('unshift')
}

function pushFn() {
    const a = []

    console.time('push')
    for (var i=0;i<100000;i++) {
        a.push(1);
    }

    console.timeEnd('push')
}

unshiftFn() // unshift: 2297.383ms
pushFn() // push: 3.760ms


作者：寻找海蓝96
链接：https://juejin.im/post/5d2832a5f265da1bc07e669e
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

我们看见两者的速度差了非常多，而且如果你不断调整for循环的次数，会发现当次数越多的时候，unshift操作就越慢，因为需要往后移的元素也就越多。

而造成这个差异的正是因为数组是被储存为一块连续内存导致的，这就造成了数组的『插入』『删除』的性能都很差，因为我们一旦删除或者插入元素，其他元素为了保持一块连续的内存都不得不产生大量元素位移，这是性能的杀手。

### 非连续内存

我们开头就有一个问题：JavaScript的数组有什么特殊之处？

当然我们会说很多JavaScript的特殊之处，什么支持字面量声明创建，支持储存不同类型数据、动态性等等。

而本质上JavaScript数组的特殊之处在于JavaScript的数组不一定是连续内存。

而维基百科关于数组的定义：

> 在计算机科学中，数组数据结构（英语：array data structure），简称数组（英语：Array），是由相同类型的元素（element）的集合所组成的数据结构，分配一块连续的内存来存储。

如果是这样的话，JavaScript的数组似乎并不是严格意义上的数组，那么为什么上一小节说数组是分配了连续内存呢？这不是自相矛盾了吗？

JavaScript的数组是否分配连续内存取决于数组成员的类型，如果统一是单一类型的数组那么会分配连续内存，如果数组内包括了各种各样的不同类型，那么则是非连续内存。

非连续内存的数组用的是类似哈希映射的方式存在，比如声明了一个数组，他被分配给了1001、2011、1088、1077四个非连续的内存地址，通过指针连接起来形成一个线性结构，那么当我们查询某元素的时候其实是需要遍历这个线性链表结构的，这十分消耗性能。

![2019-06-18-11-08-47](https://user-gold-cdn.xitu.io/2019/7/12/16be505d073f3703?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

而线性储存的数组只需要遵循这个寻址公式,进行数学上的计算就可以找到对应元素的内存地址。

> a[k]_address = base_address + k * type_size

我们做一个简单的实验,我们不断向数组插入元素，但对比的双方是非线性储存的数组和线性储存的同构数组：

```
const total = 1000000

function unshiftContinuity() {
    const arr = new Array(total)
    arr.push({name: 'xiaomuzhu'});
    console.time('unshiftContinuity')
    for(let i=0;i<total; i++){
        arr[i]=i
    }
    console.timeEnd('unshiftContinuity')
}

function unshiftUncontinuity() {
    const arr = new Array(total)
    console.time('unshiftUncontinuity')
    for (let i=0;i<total;i++) {
        arr[i]=i
    }

    console.timeEnd('unshiftUncontinuity')
}

unshiftContinuity() // unshiftContinuity: 71.050ms
unshiftUncontinuity() // unshiftUncontinuity: 1.691ms

作者：寻找海蓝96
链接：https://juejin.im/post/5d2832a5f265da1bc07e669e
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

我们看到，非线性储存的数组其速度比线性储存的数组要慢得多。
