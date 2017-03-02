---
layout: post
category : JavaScript
date: 2015-09-01
title: 「译」深入理解ES6中的解构
tagline: "ES6 JavaScript Destructuring in Depth"
tags: [JavaScript, ES6, Destructuring] 
---


我曾在React系列文章中简短的提及过一些ES6特性（以及[Babel入门](http://ponyfoo.com/articles/universal-react-babel#setting-up-babel)），现在我想深入研究这些新的**语言特性**。在阅读海量ES6与ES7的相关文章之后，我想是时候在[Pony Foo](http://ponyfoo.com/)的博客中开始讨论ES6与ES7的特性了。

本文开头会给那些ES6新特性的过度使用者一些忠告。接着介绍ES6中的解构，以及解构的使用场景，还有一些陷阱和注意事项，本文是ES6系列的第一篇。


<!-- more -->

## 一句忠告

当你不确定时，只要能选择，都推荐选择ES5和较旧的语法，而不是ES6。我这样说并不意味着使用ES6语法是一个坏主意，恰恰相反，你看我正在写的关于ES6的文章呢！需要明确的一点是，我们使用ES6特性是因为**它会提高我们的代码质量**，而不仅仅因为它很酷。

目前为止我写代码都是使用*简单的* ES5语法，然后在真的能提高代码质量的地方使用ES6语法糖。相信随着时间的推移，我可以更快速地识别什么情况下使用ES6而不是ES5，但在起步阶段最好*不要* 过于追求使用ES6的新特性。因此，你首先应该仔细分析最适合你的代码是什么，与此同时也**考虑使用ES6**。

"通过这种方式，你就能更好的**学习使用**这些新特性，而不是仅仅学点语法。"

接下来，咱们就开讲这个炫酷特性！

##解构

解构是ES6中最常用，也是最简单的特性之一。它允许你将数组和对象的属性赋给任何变量。

```javascript
var foo = { bar: 'pony', baz: 3 }
var {bar, baz} = foo
console.log(bar)
// <- 'pony'
console.log(baz)
// <- 3
```

这样获取对象中某个特定属性将会非常简洁，当然，你也可以将这个属性赋值给某个变量。

```javascript
var foo = { bar: 'pony', baz: 3 }
var {bar: a, baz: b} = foo
console.log(a)
// <- 'pony'
console.log(b)
// <- 3
```

你可以获取任意深度的属性，并且也可以将这些属性赋值给变量。

```javascript
var foo = { bar: { deep: 'pony', dangerouslySetInnerHTML: 'lol' } }
var {bar: { deep, dangerouslySetInnerHTML: sure }} = foo
console.log(deep)
// <- 'pony'
console.log(sure)
// <- 'lol'
```

默认情况下，若解构一个未定义的属性时会得到`undefined`，这与使用点或者括号去访问对象的属性是一致的。

```javascript
var {foo} = {bar: 'baz'}
console.log(foo)
// <- undefined
```

解构一个不存在的父级元素的深层嵌套的属性时会抛出异常。

```javascript
var {foo:{bar}} = {baz: 'ouch'}
// <- Exception
```

假如你认为解构是像以下ES5代码中的语法糖，那么这个异常就很合理了。

```javascript
var _temp = { baz: 'ouch' }
var bar = _temp.foo.bar
// <- Exception
```

解构还有一个超酷特性，当你交换两个变量的值时不再需要使用臭名昭著的`aux`变量了。

```javascript
function es5 () {
  var left = 10
  var right = 20
  var aux
  if (right > left) {
    aux = right
    right = left
    left = aux
  }
}
function es6 () {
  var left = 10
  var right = 20
  if (right > left) {
    [left, right] = [right, left]
  }
}
```

解构另一个特别方便的特性是keys也可以使用[计算后属性名（computed property names）](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)。

```javascript
var key = 'such_dynamic'
var { [key]: foo } = { such_dynamic: 'bar' }
console.log(foo)
// <- 'bar'
```

这在ES5中，你得用一个额外的语句与临时变量来实现。

```javascript
var key = 'such_dynamic'
var baz = { such_dynamic: 'bar' }
var foo = baz[key]
console.log(foo)
```

当你要解构的属性是`undefined`时你可以提供一个默认值。

```javascript
var {foo=3} = { foo: 2 }
console.log(foo)
// <- 2
var {foo=3} = { foo: undefined }
console.log(foo)
// <- 3
var {foo=3} = { bar: 2 }
console.log(foo)
// <- 3
```

正如前面提到的，解构在数组中也可以工作。注意我在声明变量时使用了**方括号**。

```javascript
var [a] = [10]
console.log(a)
// <- 10
```

数组解构与对象解构一样，也可以使用默认值。

```javascript
var [a] = []
console.log(a)
// <- undefined
var [b=10] = [undefined]
console.log(b)
// <- 10
var [c=10] = []
console.log(c)
// <- 10
```

在数组解构中，可以方便地跳过被解构数组中那些你不关心的元素。

```javascript
var [,,a,b] = [1,2,3,4,5]
console.log(a)
// <- 3
console.log(b)
// <- 4
```

在一个function的参数列表中也可以使用解构。

```javascript
function greet ({ age, name:greeting='she' }) {
  console.log(`${greeting} is ${age} years old.`)
}
greet({ name: 'nico', age: 27 })
// <- 'nico is 27 years old'
greet({ age: 24 })
// <- 'she is 24 years old'
```

掌握以上知识点你就知道**如何**使用解构了。那解构有**何用**呢？

## 解构的应用场景

解构在很多情况下都能派上用场。下面我例举一些最常用的场景。首先，我使用最多的一个场景是，在许多模块顶部的`import`声明中，解构可以从模块的公共API中只加载你需要的模块。举个[contra](https://github.com/bevacqua/contra)的例子：

```javascript
import {series, concurrent, map } from 'contra'
series(tasks, done)
concurrent(tasks, done)
map(items, mapper, done)
```

第二个场景，当你的方法返回一个对象时，解构也让赋值变得非常简洁。

```javascript
function getCoords () {
  return {
    x: 10,
    y: 22
  }
}
var {x, y} = getCoords()
console.log(x)
// <- 10
console.log(y)
// <- 22
```

举一个函数的参数使用解构的例子，假设你有一个方法，方法的参数都是可选且需设默认值的，这种情况下你可以使用解构。这与其他语言中（例如Python或者C#）的可选命名参数一样都极其有意思。

```javascript
function random ({ min=1, max=300 }) {
  return Math.floor(Math.random() * (max - min)) + min
}
console.log(random({}))          //必须输入{}，否则报错
// <- 174
console.log(random({max: 24}))
// <- 18
```

如果你想让可选对象变成真正的*全部可选*，你可以将代码修改如下。

```javascript
function random ({ min=1, max=300 } = {}) {
  return Math.floor(Math.random() * (max - min)) + min
}
console.log(random())           //可以不输入{}
// <- 133
```

解构另外一个非常适合使用的场景就是正则表达式，你只要定义变量接收返回值，不再需要依赖数组索引再取一遍。这里有一个[StackOverflow](http://stackoverflow.com/a/27755/389745)上使用正则表达式解析URL的例子。

```javascript
function getUrlParts (url) {
  var magic = /^(https?):\/\/(ponyfoo\.com)(\/articles\/([a-z0-9-]+))$/
  return magic.exec(url)
}
var parts = getUrlParts('http://ponyfoo.com/articles/es6-destructuring-in-depth')
var [,protocol,host,pathname,slug] = parts
console.log(protocol)
// <- 'http'
console.log(host)
// <- 'ponyfoo.com'
console.log(pathname)
// <- '/articles/es6-destructuring-in-depth'
console.log(slug)
// <- 'es6-destructuring-in-depth'
```


原文链接：[ES6 JavaScript Destructuring in Depth](http://ponyfoo.com/articles/es6-destructuring-in-depth)


译者 [@zhouweicsu](http://zhouweicsu.github.io/)   