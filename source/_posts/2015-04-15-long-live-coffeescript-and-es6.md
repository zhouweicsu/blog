---
layout: post
category : JavaScript
date: 2015-04-15
title: 「译」CoffeeScript万岁！ES6万岁！
tagline: "Long Live CoffeeScript and Long Live ES6"
tags : [JavaScript, ES6, CoffeeScript]
---

显然ES6对ES5来说是一个巨大的改进，并且[6to5](https://6to5.org/)这类工具使我们可以开始使用这些酷毙了的特性。我之前阅读了[Blake Williams](https://twitter.com/blakewilliams__)的一篇文章[Replace CoffeeScript with ES6](https://robots.thoughtbot.com/replace-coffeescript-with-es6)[ （【译】用ES6替代CoffeeScript） ](http://zhouweicsu.github.io/javascript/2015/03/12/replace-coffeescript-with-es6/)，文章对ES6如何解决许多CoffeeScript解决了的问题做了一个非常好的总结；然而，我想就Blake的几个观点做点评论，并谈谈为什么我将会继续使用CoffeeScript。


<!-- more -->

## 类
ES6中的类（还有其他ES6中的语法变化）与CoffeeScript的非常相似。为了支持ES5中不兼容的浏览器（例如IE8-），因此，我们仍然不能真正的使用getters/setters，所以忽略这些，比较如下：

```CoffeeScript
class Person
  constructor: (@firstName, @lastName) ->

  name: ->
    "#{@firstName} #{@lastName}"

  setName: (name) ->
    [@firstName, @lastName] = name.split " "

  @defaultName: ->
    "Unidentified Person"

blake = new Person "Blake", "Williams"
blake.setName "Blake Anderson"
console.log blake.name()
```

vs

```javascript
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  name() {
    return `${this.firstName} ${this.lastName}`;
  }

  setName(name) {
    [this.firstName, this.lastName] = name.split(" ");
  }

  static defaultName() {
    return "Unidentified Person"
  }
}

var blake = new Person("Blake", "Williams");
blake.setName("Blake Anderson");
console.log(blake.name());
```


我绝对喜欢ES6的风格——无需逗号，不需要到处写`function`；但是我是“write less code”派，所以我还是选择前者。然而，这真的是个人品味问题，如果你喜欢`this.`而不是`@`，喜欢添加所有额外的大括号，那么这毫无疑问你是支持Blake的观点的，认为ES6是一个更好的选择。

虽然我能理解为什么ES6允许你调用父对象的一种不同的方法，但是我还是得说我更喜欢CoffeeScript中`super`的实现。

---
## 字符串插值

很显然ES6与CoffeeScript中的字符串插值非常相似；CoffeeScript的字符串插值使用常规的“strings”，ES6使用<code>\`</code>包裹字符串（吐槽：这导致在写Markdown的时候相当烦人）。CoffeeScript使用`#{var}`，ES6使用`${var}`。这些点都大同小异。

真正突出的差别是对空白的处理——ES6（或者至少是6to5工具）会保留所有位于<code>\`</code>    之间的空格（包括换行和缩进），CoffeeScript的处理时位于简单`"`中的字符串会将所有空白合并成一个空格，若是位于`"""`之间的块级字符串则保留所有空格用来缩进，在我看来这两者的行为都是值得保留的，然而ES6的处理却没有分别处理，举个栗子：

```javascript
(function() {
  function foo(bar, val) {
    if (bar) {
      var str = `This is quite a long first line
        so I wrap it to a second line and then
        append the value ${val}`;
    }
  }
})();
```

通过[6to5 REPL](https://6to5.org/repl/#?experimental=true&playground=true&evaluate=true&loose=false)的转化输出是：

```javascript
var str = "This is quite a long first line\n        so I wrap it to a second line and then\n        append the value " + val;
```

等价的CoffeeScript代码：

```
do ->
  foo = (bar, val) ->
    if bar
      str = "This is quite a long first line
        so I wrap it to a second line and then
        append the value #{val}";
      str2 =
        """
        This is quite a long first line
        so I wrap it to a second line and then
        append the value #{val}
        """
```

编译之后：

```javascript
var str = "This is quite a long first line so I wrap it to a second line and then append the value " + val;
var str2 = "This is quite a long first line\nso I wrap it to a second line and then\nappend the value " + val;
```

我实在想不出一个能让我选择6to5的理由。

---
## 胖箭头，默认参数

这两项是对JS语法的杰出扩展，ES6的表现与CoffeeScript大致相同，只有少量语法不同。

---
## 参数列表（splats）

另一个杰出扩展，我发现参数列表(splats)这个技能在参数列的中间非常 有用，尤其是在Node.js风格的回调函数的情况下，回调函数可以自动弹出然后退出。举个栗子：

```CoffeeScript
# Splat dereferencing
names = ["Alice", "Belinda", "Catherine", "Davidson"]
[firstName, middleNames..., lastName] = names

# Splat function args preserving callback
foo = (fn, args..., callback) ->
  results = (fn arg for arg in args)
  process.nextTick ->
    callback null, results

double = (a) -> a * 2

foo double, 80, 60, 40, (err, results) ->
  console.log results
```

遗憾的是，ES6中只允许splats位于参数列的最后，如果需要你只能使用`pop()`函数来取回调（或姓氏），让你的代码变得很长，让你选择更多变量名（我们都很讨厌选变量名，不是吗？）：

```javascript
var names = ["Alice", "Belinda", "Catherine", "Davidson"];
var firstName, middleNames, lastName, rest;
[firstName, ...rest] = names;
lastName = rest.pop();
middleNames = rest;
```

---
## 构造/解构函数

我必须承认我很喜欢ES6的一个特性，就是在你写`var [first, , last] = [1, 2, 3]`赋值语句时可以使用真正的空格，但是用下划线或其他类似字符也可以是一种解决方案。

ES6中对象的构造/解构函数与CoffeeScript几乎一样（`var {a, b} = {a: 1, c:3}`， `var {foo: a, bar: b} = {foo: 1, baz: 3}`， 还有`var c = {a, b}`），但当引用当前对象的属性时，CoffeeScript的做法更好，如`c = {@a, @b}`（`var c = {a: this.a, b: this.b}`）。

---
## 一些我希望CoffeeScript拥有的ES6特性

尝试将CoffeeScript描绘成一个完美的语言是不明智的，它显然不是，它也有缺点，下面给出一个CoffeeScript中没有但ES6（甚至ES3）中有的特性列表：

- 三元操作符`a ? b : c`（`if a then b else c`对我来说太长了，我是决不会放弃`？`操作符的！）
- 计算（动态）变量名：`{[getKey()]: getValue()}`（参考[StackOverflow answer](http://stackoverflow.com/questions/13997748/is-it-possible-to-define-a-dynamically-named-property-using-object-literal-in-ja/13998890#13998890)，以及一些[有趣的](https://github.com/jashkenas/coffeescript/issues/786)[历史](https://github.com/jashkenas/coffeescript/issues/1731)）


---
## 结束语

总的来说ES6对JavaScript来说是一个伟大的飞跃，非常感谢所有让这些成为可能的开发者。以上未提及的许多ES6新增的特性都没有涉及语法变化，因此CoffeeScript也可以直接使用，例如Proxies，WeakMaps等。（我们现在甚至还有`yield`）。

我还是会坚持选择使用CoffeeScript的语法，因为它简明易读，大大提高了我的工作效率。我也很难放弃各种CoffeeScript的语法糖，例如：`object?.property`，当object是null或者undefined的时候不会报错；`a ?= b`，`a ||= b`等；隐式返回；`unless`；升级版`switch`；范围缩写`[a..b]`;array/string利用范围`arr[2..6]`来切割；`for own k, v of obj`；链式比较 `a < b < c`；块级正则表达式；还有更多！

## CoffeeScript万岁！ES6万岁！



原文链接：[Long Live CoffeeScript and Long Live ES6](http://www.benjiegillam.com/2015/02/long-live-coffeescript-and-es6/)


译者 [@zhouweicsu](http://zhouweicsu.github.io/)    

2015 年 4月 15日  