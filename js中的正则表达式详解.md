# js中的正则表达式详解

1. 创建正则表达式的方法
   
   可以使用以下两种方法之一构建一个正则表达式：
   
   使用一个正则表达式字面量，其由包含在斜杠之间的模式组成，如下所示：
   
   ```js
   var re = /ab+c/;
   ```
   
   使用正则表达式字面量为正则表达式提供了脚本加载后**\(loaded\)** 的编译。当正则表达式保持不变时，使用此方法可获得更好的性能。
   
   或者调用**RegExp**对象的构造函数，如下所示：
   
   ```js
   var re = new RegExp("ab+c");
   ```
   
   使用构造函数为正则表达式提供了运行时的编译。使用构造函数的方式，当你知道正则表达式的模式将会改变，或者你不知道模式，并且从其他来源获取它，如用户输入。
   
   > Using the constructor function provides runtime compilation of the regular expression. Use the constructor function when you know the regular expression pattern will be changing, or you don't know the pattern and are getting it from another source, such as user input

2. 使用正则表达式的几种方法
   
   - exec
     
     `RegExpObject.exec(string)`
     
     **返回值**
     
     返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 null。
   
   - test
     
     `RegExpObject.test(string)`
     
     **返回值**
     
     如果字符串 string 中含有与 RegExpObject 匹配的文本，则返回 true，否则返回 false。
   
   - match
     
     **定义和用法**
     
     match() 方法可在字符串内检索指定的值，或找到一个或多个正则表达式的匹配。
     
     该方法类似 indexOf() 和 lastIndexOf()，但是它返回指定的值，而不是字符串的位置。
     
     **语法**
     
     `stringObject.match(searchvalue)`
     `stringObject.match(regexp)`
     
     | 参数          | 描述                                                                               |
     | ----------- | -------------------------------------------------------------------------------- |
     | searchvalue | 必需。规定要检索的字符串值。                                                                   |
     | regexp      | 必需。规定要匹配的模式的 RegExp 对象。如果该参数不是 RegExp 对象，则需要首先把它传递给 RegExp 构造函数，将其转换为 RegExp 对象。 |

| 方法                                                                                                                                                                                                                            | 描述                                                       |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| [`exec`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec "exec() 方法在一个指定字符串中执行一个搜索匹配。返回一个结果数组或 null。")                                                                           | 一个在字符串中执行查找匹配的RegExp方法，它返回一个数组（未匹配到则返回 null）。            |
| [`test`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test "test() 方法执行一个检索，用来查看正则表达式与指定的字符串是否匹配。返回 true 或 false。")                                                                | 一个在字符串中测试是否匹配的RegExp方法，它返回 true 或 false。                 |
| [`match`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match "match() 方法检索返回一个字符串匹配正则表达式的的结果。")                                                                                    | 一个在字符串中执行查找匹配的String方法，它返回一个数组，在未匹配到时会返回 null。           |
| [`matchAll`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/matchAll "matchAll() 方法返回一个包含所有匹配正则表达式及分组捕获结果的迭代器。")                                                                     | 一个在字符串中执行查找所有匹配的String方法，它返回一个迭代器（iterator）。             |
| [`search`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/search "search() 方法执行正则表达式和 String 对象之间的一个搜索匹配。")                                                                          | 一个在字符串中测试匹配的String方法，它返回匹配到的位置索引，或者在失败时返回-1。             |
| [`replace`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace "replace() 方法返回一个由替换值（replacement）替换一些或所有匹配的模式（pattern）后的新字符串。模式可以是一个字符串或者一个正则表达式，替换值可以是一个字符串或者一个每次匹配都要调用的回调函数。") | 一个在字符串中执行查找匹配的String方法，并且使用替换字符串替换掉匹配到的子字符串。             |
| [`split`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/split "split() 方法使用指定的分隔符字符串将一个String对象分割成字符串数组，以将字符串分隔为子字符串，以确定每个拆分的位置。")                                                  | 一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中的 `String` 方法。 |

3. 关于正则表达式 \1 \2之类的问题
   
   我们创建一个正则表达式
   `var RegExp = /^(123)(456)\2\1$/;`
   
   这个正则表达式匹配到的字符串是
   `123456123`
   创建另外第三正则表达式
   `var RegExp1 = /^(123)(456)\2$/;`
   这个正则表达式匹配到的字符串是
   `123456456`
   
   这个`\1  \2......`  都要和正则表达式集合()一起使用
   简单的说就是
   `\1`表示重复正则第一个圆括号内匹配到的内容
   `\2`表示重复正则第二个圆括号内匹配到的内容
