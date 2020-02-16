## CSS 选择器

在 CSS 中，选择器是一种模式，用于选择需要添加样式的元素。

"CSS" 列指示该属性是在哪个 CSS 版本中定义的。（CSS1、CSS2 还是 CSS3。）

| 选择器                                                                                                                            | 例子                    | 例子描述                               | CSS |
| ------------------------------------------------------------------------------------------------------------------------------ | --------------------- | ---------------------------------- | --- |
| [.*class*](https://www.w3school.com.cn/cssref/selector_class.asp "CSS .class 选择器")                                             | .intro                | 选择 class="intro" 的所有元素。            | 1   |
| [#*id*](https://www.w3school.com.cn/cssref/selector_id.asp "CSS #id 选择器")                                                      | #firstname            | 选择 id="firstname" 的所有元素。           | 1   |
| [*](https://www.w3school.com.cn/cssref/selector_all.asp "CSS * 选择器")                                                           | *                     | 选择所有元素。                            | 2   |
| [*element*](https://www.w3school.com.cn/cssref/selector_element.asp "CSS element 选择器")                                         | p                     | 选择所有 <p> 元素。                       | 1   |
| [*element*,*element*](https://www.w3school.com.cn/cssref/selector_element_comma.asp "CSS element,element 选择器")                 | div,p                 | 选择所有 <div> 元素和所有 <p> 元素。           | 1   |
| [*element* *element*](https://www.w3school.com.cn/cssref/selector_element_element.asp "CSS element element 选择器")               | div p                 | 选择 <div> 元素内部的所有 <p> 元素。           | 1   |
| [*element*>*element*](https://www.w3school.com.cn/cssref/selector_element_gt.asp "CSS element>element 选择器")                    | div>p                 | 选择父元素为 <div> 元素的所有 <p> 元素。         | 2   |
| [*element*+*element*](https://www.w3school.com.cn/cssref/selector_element_plus.asp "CSS element+element 选择器")                  | div+p                 | 选择紧接在 <div> 元素之后的所有 <p> 元素。        | 2   |
| [[*attribute*]](https://www.w3school.com.cn/cssref/selector_attribute.asp "CSS [attribute] 选择器")                               | [target]              | 选择带有 target 属性所有元素。                | 2   |
| [[*attribute*=*value*]](https://www.w3school.com.cn/cssref/selector_attribute_value.asp "CSS [attribute=value] 选择器")           | [target=_blank]       | 选择 target="_blank" 的所有元素。          | 2   |
| [[*attribute*~=*value*]](https://www.w3school.com.cn/cssref/selector_attribute_value_contain.asp "CSS [attribute~=value] 选择器") | [title~=flower]       | 选择 title 属性包含单词 "flower" 的所有元素。    | 2   |
| [attribute\|=value]                                                                                                            | [lang\|=en]           | 选择 lang 属性值以 "en" 开头的所有元素。         | 2   |
| [:link](https://www.w3school.com.cn/cssref/selector_link.asp "CSS :link 选择器")                                                  | a:link                | 选择所有未被访问的链接。                       | 1   |
| [:visited](https://www.w3school.com.cn/cssref/selector_visited.asp "CSS :visited 选择器")                                         | a:visited             | 选择所有已被访问的链接。                       | 1   |
| [:active](https://www.w3school.com.cn/cssref/selector_active.asp "CSS :active 选择器")                                            | a:active              | 选择活动链接。                            | 1   |
| [:hover](https://www.w3school.com.cn/cssref/selector_hover.asp "CSS :hover 选择器")                                               | a:hover               | 选择鼠标指针位于其上的链接。                     | 1   |
| [:focus](https://www.w3school.com.cn/cssref/selector_focus.asp "CSS :focus 选择器")                                               | input:focus           | 选择获得焦点的 input 元素。                  | 2   |
| [:first-letter](https://www.w3school.com.cn/cssref/selector_first-letter.asp "CSS :first-letter 选择器")                          | p:first-letter        | 选择每个 <p> 元素的首字母。                   | 1   |
| [:first-line](https://www.w3school.com.cn/cssref/selector_first-line.asp "CSS :first-line 选择器")                                | p:first-line          | 选择每个 <p> 元素的首行。                    | 1   |
| [:first-child](https://www.w3school.com.cn/cssref/selector_first-child.asp "CSS :first-child 选择器")                             | p:first-child         | 选择属于父元素的第一个子元素的每个 <p> 元素。          | 2   |
| [:before](https://www.w3school.com.cn/cssref/selector_before.asp "CSS :before 选择器")                                            | p:before              | 在每个 <p> 元素的内容之前插入内容。               | 2   |
| [:after](https://www.w3school.com.cn/cssref/selector_after.asp "CSS :after 选择器")                                               | p:after               | 在每个 <p> 元素的内容之后插入内容。               | 2   |
| [:lang(*language*)](https://www.w3school.com.cn/cssref/selector_lang.asp "CSS :lang(language) 选择器")                            | p:lang(it)            | 选择带有以 "it" 开头的 lang 属性值的每个 <p> 元素。 | 2   |
| [*element1*~*element2*](https://www.w3school.com.cn/cssref/selector_gen_sibling.asp "CSS element1~element2 选择器")               | p~ul                  | 选择前面有 <p> 元素的每个 <ul> 元素。           | 3   |
| [[*attribute*^=*value*]](https://www.w3school.com.cn/cssref/selector_attr_begin.asp "CSS [attribute^=value] 选择器")              | a[src^="https"]       | 选择其 src 属性值以 "https" 开头的每个 <a> 元素。 | 3   |
| [[*attribute*$=*value*]](https://www.w3school.com.cn/cssref/selector_attr_end.asp "CSS [attribute$=value] 选择器")                | a[src$=".pdf"]        | 选择其 src 属性以 ".pdf" 结尾的所有 <a> 元素。   | 3   |
| [[*attribute**=*value*]](https://www.w3school.com.cn/cssref/selector_attr_contain.asp "CSS [attribute*=value] 选择器")            | a[src*="abc"]         | 选择其 src 属性中包含 "abc" 子串的每个 <a> 元素。  | 3   |
| [:first-of-type](https://www.w3school.com.cn/cssref/selector_first-of-type.asp "CSS :first-of-type 选择器")                       | p:first-of-type       | 选择属于其父元素的首个 <p> 元素的每个 <p> 元素。      | 3   |
| [:last-of-type](https://www.w3school.com.cn/cssref/selector_last-of-type.asp "CSS :last-of-type 选择器")                          | p:last-of-type        | 选择属于其父元素的最后 <p> 元素的每个 <p> 元素。      | 3   |
| [:only-of-type](https://www.w3school.com.cn/cssref/selector_only-of-type.asp "CSS :only-of-type 选择器")                          | p:only-of-type        | 选择属于其父元素唯一的 <p> 元素的每个 <p> 元素。      | 3   |
| [:only-child](https://www.w3school.com.cn/cssref/selector_only-child.asp "CSS :only-child 选择器")                                | p:only-child          | 选择属于其父元素的唯一子元素的每个 <p> 元素。          | 3   |
| [:nth-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-child.asp "CSS :nth-child(n) 选择器")                           | p:nth-child(2)        | 选择属于其父元素的第二个子元素的每个 <p> 元素。         | 3   |
| [:nth-last-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-child.asp "CSS :nth-last-child(n) 选择器")            | p:nth-last-child(2)   | 同上，从最后一个子元素开始计数。                   | 3   |
| [:nth-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-of-type.asp "CSS :nth-of-type(n) 选择器")                     | p:nth-of-type(2)      | 选择属于其父元素第二个 <p> 元素的每个 <p> 元素。      | 3   |
| [:nth-last-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-of-type.asp "CSS :nth-last-of-type(n) 选择器")      | p:nth-last-of-type(2) | 同上，但是从最后一个子元素开始计数。                 | 3   |
| [:last-child](https://www.w3school.com.cn/cssref/selector_last-child.asp "CSS :last-child 选择器")                                | p:last-child          | 选择属于其父元素最后一个子元素每个 <p> 元素。          | 3   |
| [:root](https://www.w3school.com.cn/cssref/selector_root.asp "CSS :root 选择器")                                                  | :root                 | 选择文档的根元素。                          | 3   |
| [:empty](https://www.w3school.com.cn/cssref/selector_empty.asp "CSS :empty 选择器")                                               | p:empty               | 选择没有子元素的每个 <p> 元素（包括文本节点）。         | 3   |
| [:target](https://www.w3school.com.cn/cssref/selector_target.asp "CSS :target 选择器")                                            | #news:target          | 选择当前活动的 #news 元素。                  | 3   |
| [:enabled](https://www.w3school.com.cn/cssref/selector_enabled.asp "CSS :enabled 选择器")                                         | input:enabled         | 选择每个启用的 <input> 元素。                | 3   |
| [:disabled](https://www.w3school.com.cn/cssref/selector_disabled.asp "CSS :disabled 选择器")                                      | input:disabled        | 选择每个禁用的 <input> 元素                 | 3   |
| [:checked](https://www.w3school.com.cn/cssref/selector_checked.asp "CSS :checked 选择器")                                         | input:checked         | 选择每个被选中的 <input> 元素。               | 3   |
| [:not(*selector*)](https://www.w3school.com.cn/cssref/selector_not.asp "CSS :not(selector) 选择器")                               | :not(p)               | 选择非 <p> 元素的每个元素。                   | 3   |
| [::selection](https://www.w3school.com.cn/cssref/selector_selection.asp "CSS ::selection 选择器")                                 | ::selection           | 选择被用户选取的元素部分。                      | 3   |
