---
layout: post
category : JavaScript
title: doT 模板中直接写 Javascript 代码导致 Uncaught SyntaxError Unexpected identifier
tagline: "一个分号的故事"
tags : [template, doT, 分号]
---

在使用 doT 的过程中，由于项目需要，我们将电话号码拼成了以逗号分开的字符串

```javascript
/** 初始json对象 **/
 var data = {
    tel:'4008107107,4008107106'
 }
```

<!-- more -->


而页面中需要显示独立的电话号码，这就需要在 doT 模板渲染中使用 Javascript 的 `split` 方法，将字符串解析成数组。

```javascript
/** 目标json对象 **/
 var data = {
    tel:[4008107107,4008107106]
 }
```

我们知道，doT 的原理就是将整个的模板 html 代码放在`<script></script>`标签中，然后生成一段可执行的 Javascript 代码，然后将 JSON 格式的数据传入该函数，return 生成的`html`代码，而 doT 在生成可执行 Javascript 代码片段的过程中其实就是字符串的解析与拼接。

```
/**doT中直接写Javascript代码**/
{ { it.tel = it.tel.split(',') } }
```

写成上面这样子，页面渲染会报错*`Uncaught SyntaxError: Unexpected identifier`*因为在生成Javascript代码片段时该段JS代码被解析字符串


```
it.tel = it.tel.split(',')
```

与后一句可执行的JS代码` out += '';`拼接之后变成

```
it.tel = it.tel.split(',') out += '';
```

doT无法像Javascript解析器一样自动补全分号，这就导致了上面的错误发生。

### 解决方法

加上分号就好了

```
{ { it.tel = it.tel.split(','); } }
```



**由于doT中2个{}套在一起无法被解析，所以在两个花括号之间加入引号**





作者 [@zhouweicsu][1]

[1]: https://github.com/zhouweicsu