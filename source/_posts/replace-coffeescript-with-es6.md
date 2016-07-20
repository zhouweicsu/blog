---
layout: post
date: 2015-03-12
category : JavaScript
title: 用ES6替代CoffeeScript
tagline: "Replace CoffeeScript with ES6"
tags : [JavaScript, ES6, CoffeeScript] 
---

原文链接：[Replace CoffeeScript with ES6][1]

一直以来我都在关注和研究JavaScript的下一个版本——[ES6][2]，并终于在一个项目中用了它。在短暂的使用过程中，我发现ES6解决了许多[CoffeeScript][3]尝试不去大量修改语法想解决的问题。

###如何使用ES6###
我们可以通过6to5这个项目开始使用ES6,[6to5][4]可以将ES6代码转译成符合ES5标准的代码。6to5支持数量众多的构建工具，包括Broccoli，Grunt，Gulp，和Sprockets。我在使用[sprockets-es6][5]上有许多成功的经验，并且Sprockets 4.x版本将会非常好的支持6to5。

如果你使用Vim编辑器的话，那么你可以通过将如下代码加入你的`.vimrc`文件，从而将带`.es6`扩展名的文件与JavaScript关联起来。

```
autocmd BufRead,BufNewFile *.es6 setfiletype javascript
```

你也可以在浏览器中使用[6to5 REPL][6]来编写ES6代码。

###类###
CoffeeScript与ES6都支持类。让我们来看下CoffeeScript类与相同功能的ES6类的对比。

CoffeeScript有可以利用的三个优势：参数列表使用实例变量，字符串插值，函数调用不需要括号。

```coffeescript
class Person
  constructor: (@firstName, @lastName) ->

  name: ->
    "#{@first_name} #{@last_name}"

  setName: (name) ->
    names = name.split " "

    @firstName = names[0]
    @lastName = names[1]

blake = new Person "Blake", "Williams"
blake.setName("Blake Anderson")
console.log blake.name()
```

ES6我们可以使用classes、getters和setters：

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  get name() {
    return this.firstName + " " + this.lastName;
  }

  set name(name) {
    var names = name.split(" ");

    this.firstName = names[0];
    this.lastName = names[1];
  }
}

var blake = new Person("Blake", "Williams");
blake.name = "Blake Anderson"
console.log(blake.name);
```

如果你使用过任何提供类机制的javascript库或框架，你会发现ES6的语法有一点点不同之处：

> * 函数名之后没有分号
> * 关键字function被省略了
> * 每个定义之后都没有逗号了

我们也可以利用getters和setters方法让使用`name`方法像属性那样方便。


###字符串插值###
我一直希望JavaScript能够拥有更加有方便的字符串语法。幸运地是ES6新加了[template strings][7]。下面我们来比较一下CoffeeScript strings，JavaScript strings，和template strings，看看他们的能力。

CoffeeScript:

```coffeescript
"CoffeeScript allows multi-line strings
with
interpolation like 1 + 1 = #{1 + 1}
"
```

JavaScript strings:

```javascript
"JavaScript strings can only span a single line " +
  "and interpolation isn't possible"
```

ES6 template strings:

```
`Template strings allow strings to span
multiple lines and allow interpolation like 1 + 1 = ${1 + 1}
`
```

我们可以在前文给出的例子中使用template strings，将`name`的getter方法改为如下形式：

```
get name() {
  return `${this.firstName} ${this.lastName}`;
}
```

这比之前的字符串拼接的方式看起来清爽多了，并且看起来更加像CoffeeScript。

###胖箭头###
另一个CoffeeScript中很吸引人的特性在ES6中也有新增，那就是胖箭头。胖箭头可以将函数与当前上下文的`this`值绑定起来。首先，让我看看不使用胖箭头函数是如何处理this的。

在ES5中，定义一个函数前必须用一个变量来保存当前的`this`值：

```
var self = this;

$("button").on("click", function() {
  // do something with self
});
```

CoffeeScript中使用胖箭头，并且可以省略参数和花括号：

```
$("button").on"click", => 
    # do something with this
```

在ES6中，花括号是必须的，但是参数可以省略：

```
$("button").on("click", () => {
  // do something with this
});
```
###其他特性###
ES6有一些其他的特性值得顺便提一下。

##默认参数##

CoffeeScript：

```coffeescript  
hello = (name = "guest") ->
  alert(name)
```

ES6:

```
var hello = function(name = "guest"){
  alert(name);
}
```

##参数列表（splats）##
[变长参数函数][8]，CoffeeScript称之为参数列表（splats），允许收集附加参数传递给函数作为一个数组。ES6中称之为其他参数。

CoffeeScript:

```coffeescript
awards = (first, second, others...) ->
  gold = first
  silver = second
  honorable_mention = others
```

ES6:

```
var awards = function(first, second, ...others) {
  var gold = first;
  var silver = second;
  var honorableMention = others;
}
```
##解构赋值##
解构赋值允许按照一定模式，从数组和对象中提取值，对变量进行赋值。

CoffeeScript:

```coffeescript
[first, _, last] = [1, 2, 3]
```

ES6:

```
var [first, , last] = [1, 2, 3]
```

我们可以使用解构赋值将之前定义的`name`的setter函数代码更加简洁：

```
set name(name) {
  [this.firstName, this.lastName] = name.split(" ");
}
```

###结束语###
许多ES6转译工具已经在积极的开发中，功能上已经可以追上CoffeeScript了。本文仅仅介绍了极少数JavaScript的新特性，但是点击[这里][9]找到更多新特性。

下个项目把CoffeeScript放一边，试一试ES6吧！

译者 [@zhouweicsu][10]     
2015 年 3月 15日    

[1]: https://robots.thoughtbot.com/replace-coffeescript-with-es6
[2]: http://en.wikipedia.org/wiki/ECMAScript#ECMAScript_Harmony_.286th_Edition.29
[3]: http://coffeescript.org/
[4]: https://babeljs.io/
[5]: https://github.com/josh/sprockets-es6
[6]: https://babeljs.io/repl/
[7]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings
[8]: http://en.wikipedia.org/wiki/Variadic_function
[9]: https://6to5.org/docs/learn-es6/
[10]: http://zhouweicsu.github.io/