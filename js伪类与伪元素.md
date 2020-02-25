### js伪类与伪元素（转自[总结伪类与伪元素](http://www.alloyteam.com/2016/05/summary-of-pseudo-classes-and-pseudo-elements/#prettyPhoto)）

**1. 伪类与伪元素**

先说一说为什么 css 要引入伪元素和伪类，以下是 [css2.1 Selectors 章节中对伪类与伪元素的描述](https://www.w3.org/TR/CSS2/selector.html#pseudo-elements)：

*CSS introduces the concepts of pseudo-elements and pseudo-classes  to permit formatting based on information that lies outside the document tree.*

直译过来就是：css 引入伪类和伪元素概念是为了格式化文档树以外的信息。也就是说，伪类和伪元素是用来修饰不在文档树中的部分，比如，一句话中的第一个字母，或者是列表中的第一个元素。下面分别对伪类和伪元素进行解释：

伪类用于当已有元素处于的某个状态时，为其添加对应的样式，这个状态是根据用户行为而动态变化的。比如说，当用户悬停在指定的元素时，我们可以通过:hover 来描述这个元素的状态。虽然它和普通的 css 类相似，可以为已有的元素添加样式，但是它只有处于 dom 树无法描述的状态下才能为元素添加样式，所以将其称为伪类。

伪元素用于创建一些不在文档树中的元素，并为其添加样式。比如说，我们可以通过:before 来在一个元素前增加一些文本，并为这些文本添加样式。虽然用户可以看到这些文本，但是这些文本实际上不在文档树中。

**2. 伪类与伪元素的区别**

这里通过两个例子来说明两者的区别。

下面是一个简单的 html 列表片段：

```html
<ul>
    <li>我是第一个</li>
    <li>我是第二个</li>
</ul>
```

如果想要给第一项添加样式，可以在为第一个<li> 添加一个类，并在该类中定义对应样式：

```html


<ul>
    <li class="first-item">我是第一个</li>
    <li>我是第二个</li>
</ul>
```

```css
li.first-item {
 color: orange
 }
```

如果不用添加类的方法，我们可以通过给设置第一个<li> 的:first-child 伪类来为其添加样式。这个时候，被修饰的<li> 元素依然处于文档树中。

```html
<ul>
    <li>我是第一个</li>
    <li>我是第二个</li>
</ul>
```

```css
li:first-child {
    color: orange
}
```

下面是另一个简单的 html 段落片段：

```html
<p>Hello World, and wish you have a good day!</p>
```

如果想要给该段落的第一个字母添加样式，可以在第一个字母中包裹一个<span> 元素，并设置该 span 元素的样式：

**HTML:**

```html
<p><span class="first">H</span>ello World, and wish you have a good day!</p>
```

**CSS:**

```css
.first {
    font-size: 5em;
}
```

如果不创建一个<span> 元素，我们可以通过设置<p> 的:first-letter 伪元素来为其添加样式。这个时候，看起来好像是创建了一个虚拟的<span> 元素并添加了样式，但实际上文档树中并不存在这个<span> 元素。

**HTML:**

```html
<p>Hello World, and wish you have a good day!</p>
```

**CSS:**

```css
p:first-letter {
    font-size: 5em;
}
```

从上述例子中可以看出，伪类的操作对象是文档树中已有的元素，而伪元素则创建了一个文档数外的元素。因此，伪类与伪元素的区别在于：有没有创建一个文档树之外的元素。

### 使用js控制伪元素的方法(转自[简书](https://www.jianshu.com/p/37f639f108dd))

## 获取伪元素的属性值：

获取伪元素的属性值可以使用**window.getComputedStyle()**方法，获取伪元素的CSS样式声明对象。然后利用getPropertyValue方法或直接使用键值访问都可以获取对应的属性值。

**语法：window.getComputedStyle(element[, pseudoElement])**

> - 参数如下：
> - element（Object）：伪元素的所在的DOM元素；
> - pseudoElement（String）：伪元素类型。可选值有：”:after”、”:before”、”:first-line”、”:first-letter”、”:selection”、”:backdrop”；

举个栗子：

```cpp
// CSS代码
#myId:before {
    content: "hello world!";
    display: block;
    width: 100px;
    height: 100px;
    background: red;
}

// HTML代码
<div id="myId"></div>

// JS代码
var myIdElement = document.getElementById("myId");
var beforeStyle = window.getComputedStyle(myIdElement, ":before");
console.log(beforeStyle); // [CSSStyleDeclaration Object]
console.log(beforeStyle.width); // 100px
console.log(beforeStyle.getPropertyValue("width")); // 100px
console.log(beforeStyle.content); // "hello world!"
```

**备注：**

**1.**

getPropertyValue()和直接使用键值访问，都可以访问CSSStyleDeclaration Object。它们两者的区别有：

> - 对于float属性，如果使用键值访问，则不能直接使用getComputedStyle(element, null).float，而应该是cssFloat与styleFloat；
> - 直接使用键值访问，则属性的键需要使用驼峰写法，如：style.backgroundColor；
> - 使用getPropertyValue()方法不必可以驼峰书写形式（不支持驼峰写法），例如：style.getPropertyValue(“border-top-color”)；
> - getPropertyValue()方法在IE9+和其他现代浏览器中都支持；在IE6~8中，可以使用getAttribute()方法来代替；

**2.**

伪元素默认是”**display: inline**”。如果没有定义display属性，即使在CSS中显式设置了width的属性值为固定的大小如”100px”，但是最后获取的width值仍是”auto”。这是因为行内元素不能自定义设置宽高。解决办法是给伪元素修改display属性为”block”、”inline-block”或其他。

## 四. 更改伪元素的样式：

### 方法1. 更换class来实现伪元素属性值的更改：

举个栗子：

```kotlin
// CSS代码
.red::before {     content: "red";     color: red; 
}
.green::before {     content: "green";     color: green;
}

// HTML代码
<div class="red">内容内容内容内容</div>

// jQuery代码
$(".red").removeClass('red').addClass('green');
```

### 方法2. 使用CSSStyleSheet的insertRule来为伪元素修改样式：

举个栗子：

```ruby
document.styleSheets[0].addRule('.red::before','color: green'); // 支持IE document.styleSheets[0].insertRule('.red::before { color: green }', 0); // 支持非IE的现代浏览器
```

### 方法3. 在`<head>`标签中插入`<style>`的内部样式：

```dart
var style = document.createElement("style"); document.head.appendChild(style); sheet = style.sheet; sheet.addRule('.red::before','color: green');  // 兼容IE浏览器
sheet.insertRule('.red::before { color: green }', 0); // 支持非IE的现代浏览器
```

或者用jQuery：

```xml
$('<style>.red::before{color:green}</style>').appendTo('head');
```

## 五. 修改伪元素的content的属性值：

### 方法1. 使用CSSStyleSheet的insertRule来为伪元素修改样式：

```jsx
var latestContent = "修改过的内容";
var formerContent = window.getComputedStyle($('.red'), '::before').getPropertyValue('content'); document.styleSheets[0].addRule('.red::before','content: "' + latestContent + '"'); document.styleSheets[0].insertRule('.red::before { content: "' + latestContent + '" }', 0);
```

### 方法2. 使用DOM元素的data-*属性来更改content的值：

```kotlin
// CSS代码
.red::before {
    content: attr(data-attr);
    color: red;
}

// HTML代码
<div class="red" data-attr="red">内容内容内容内容</div>

// JacaScript代码
$('.red').attr('data-attr', 'green');
```

## 六. 一点小小建议：

**1.**

伪元素的content属性很强大，可以写入各种字符串和部分多媒体文件。但是伪元素的内容只存在于CSS渲染树中，并不存在于真实的DOM中。所以为了SEO优化，最好不要在伪元素中包含与文档相关的内容。

**2.**

修改伪元素的样式，建议使用通过更换class来修改样式的方法。因为其他两种通过插入行内CSSStyleSheet的方式是在JavaScript中插入字符代码，不利于样式与控制分离；而且字符串拼接易出错。

**3.**

修改伪元素的content属性的值，建议使用利用DOM的data-*属性来更改。
