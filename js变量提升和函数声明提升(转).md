## js变量提升和函数声明提升[转自@segmentfault](https://segmentfault.com/a/1190000013562979)

JS在编译阶段，函数声明和变量声明都会被先处理置于执行环境的顶部，且赋值会被留在原地，这个过程称之为提升。 
举个简单例子：

```js
console.log(i);
var i = 1;
function fn () {
  console.log(2)
  }
```

实际上代码顺序是这样的：

```js
function fn () {
    console.log(2)
}
var i;
console.log(i);
i = 1;
```

## 一、变量提升

变量声明在编译阶段被处理，而变量赋值则留在原地等待执行。

```js
console.log(i);   // undefined
var i = 1;
console.log(i);   // 1
```

相当于：

```js
var i;
console.log(i);   // 由于i只声明未赋值，输出undefined
i = 1;
console.log(i)    // i已赋值，输出1
```

一道测试题

```js
var age = 10;  
function person () {      
  age = 100;     
  console.log(age);  // 100
  var age;      
  console.log(age)  // 100
}  
person();  
console.log(age);   // 10
```

## 二、函数提升

解析器在解析时对函数声明与函数表达式有着不同的优先级，**实际上编译阶段函数声明会先于变量被提升，并使其在执行任何代码之前可访问，函数表达式实际上是变量声明的一种，因此函数声明提升优于函数表达式**

```js
console.log(fn(1));    // 1
function fn (a) {
    return a;
}
```

如上面的代码，由于函数声明被置于执行环境顶部，即使调用函数的代码在声明函数之前也可以正确访问。再看函数表达式的例子：

```js
console.log(fn(1));
var fn = function (a) {
    return a;
}
// 相当于

var fn;
console.log(fn(1));
fn = function (a) {  
return a;
}
// fn is not a function
```

上面的例子之所以报错，是因为变量fn声明后还未对函数引用。 
**另外函数声明提升不会被变量声明覆盖，但会被变量赋值覆盖。** 
变量未赋值的例子：

```js
  function fn() {    
  console.log(1);  
}  
  var fn;  
  console.log(fn);    // 由于后一个fn只声明未负值，因此输出的是函数fn
```

变量赋值的例子：

```js
function fn() {    
    console.log(1);  
}  
var fn = function () {      
    console.log(2)
};  
fn();    // 2
// 相当于
function fn(){    
    console.log(1);  
}  
var fn;  
fn = function () {      
    console.log(2)
};  
fn();    // 2（因为声明的函数fn被后一个已引用函数的变量fn所覆盖，因此输出2）
```

再来点：

```js
fn();  
var fn = function () {
    console.log(1);
}
fn();  
function fn () {
    console.log(2);  
}  
var fn;  
fn();  // 依次输出2,1,1
```
