### 理解html中的Dom

#### 一、 Html Dom是什么？

The HTML DOM is a standard **object** model and **programming interface** for HTML. It defines:

- The HTML elements as **objects**
- The **properties** of all HTML elements
- The **methods** to access all HTML elements
- The **events** for all HTML elements

In other words: **The HTML DOM is a standard for how to get, change, add, or delete HTML elements.**

#### 二、Html Dom Methods

HTML DOM methods are **actions** you can perform (on HTML Elements).

HTML DOM properties are **values** (of HTML Elements) that you can set or change.

---

**The DOM Programming Interface**

The HTML DOM can be accessed with JavaScript (and with other programming languages).

In the DOM, all HTML elements are defined as **objects**.

The programming interface is the properties and methods of each object.

A **property** is a value that you can get or set (like changing the content of an HTML element).

A **method** is an action you can do (like add or deleting an HTML element).

例如：

```javascript
<script>
document.getElementById("demo").innerHTML = "Hello World!";
</script>
```

在上述例子中`getElementById` is a **method**, while `innerHTML` is a **property**.

#### 三、 The HTML DOM Document Object

The document object represents your web page.

If you want to access any element in an HTML page, you always start with accessing the document object.

Below are some examples of how you can use the document object to access and manipulate HTML.

---

**Finding HTML Elements**

| Method                                  | Description                   |
| --------------------------------------- | ----------------------------- |
| document.getElementById(*id*)           | Find an element by element id |
| document.getElementsByTagName(*name*)   | Find elements by tag name     |
| document.getElementsByClassName(*name*) | Find elements by class name   |

#### 四、Html Dom Elements

## Finding HTML Elements

Often, with JavaScript, you want to manipulate HTML elements.

To do so, you have to find the elements first. There are several ways to do this:

- Finding HTML elements by id
- Finding HTML elements by tag name
- Finding HTML elements by class name
- Finding HTML elements by CSS selectors
- Finding HTML elements by HTML object collections

---

DOM树中总共分为如下几种节点格式：Element类型（元素节点）、Text类型（文本节点）、Comment类型（注释节点）、Document类型（document节点）。
