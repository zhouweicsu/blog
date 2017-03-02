---
layout: post 
category : 翻译
date: 2017-02-22
title: 「译」面试中要注意的 3 个 JavaScript 问题
tagline: "3 JavaScript questions to watch out for during coding interviews" 
tags: [翻译, JavaScript, 面试] 
---


JavaScript 是[所有现代浏览器](https://developer.mozilla.org/en-US/docs/Web/JavaScript)的官方语言。因此，各种语言的开发者面试中都会遇到 JavaScript 问题。

本文不讲最新的 JavaScript 库，通用开发实践，或任何新的 [ES6 函数](https://hackernoon.com/es6-features-you-need-to-know-now-b525e2b0755e#.seo0weyr4)。而是讲讲面试中经常出现的 3 个 JavaScript 问题。我问过这些问题，我的朋友说他们也问。

<!--more -->

当然不是说你在准备 JavaScript 面试时只要学习这 3 个问题 —— 你还[有](http://www.thatjsdude.com/interview/js2.html)[很多](http://jstherightway.org/#getting-started)[途径](https://medium.com/javascript-scene/10-interview-questions-every-javascript-developer-should-know-6fa6bdf5ad95#.7fty5p61c)去更好的准备即将到来的面试 —— 但面试官很有可能通过下面 3 个问题来判断你了解和掌握 JavaScript 和 DOM 的情况。

让我们开始吧！注意下面的例子中我们使用原生 JavaScript， 因为面试官通常想考查你在不借助库（例如 jQuery）的帮助时掌握 JavaScript 和 DOM 的情况。

## 问题 #1： 事件代理

创建应用时，有时需要给页面中的按钮，文字，或图片添加事件监听器，当用户与这些元素交互时触发某些操作。

我们以一个简单的代办事项列表为例，面试官会告诉你，他们希望在用户点击列表中某一项时触发一个动作。并让你用 JavaScript 根据下面的 HTML 代码实现这个功能：


```javascript
<ul id="todo-app">
  <li class="item">Walk the dog</li>
  <li class="item">Pay bills</li>
  <li class="item">Make dinner</li>
  <li class="item">Code for one hour</li>
</ul>
```

你可能会像下面的代码一样给元素添加事件监听器：

```
document.addEventListener('DOMContentLoaded', function() {
  
  let app = document.getElementById('todo-app');
  let items = app.getElementsByClassName('item');
  
  // 给每个列表项添加事件监听器
  for (let item of items) {
    item.addEventListener('click', function() {
      alert('you clicked on item: ' + item.innerHTML);
    });
  }
  
});
```

当然上面的代码能完成面试官的需求，问题是每个列表项都会加上一个事件监听器。当列表只有 4 项时没有问题，但如果有人给代办事项列表新增了 10,000 个事项呢（他们也许有一大堆事情要做）？那时函数会创建 10,000 个事件监听器，然后把它们都添加到 DOM 上。这样[效率](https://www.kirupa.com/html5/handling_events_for_many_elements.htm)非常低。

面试中最好首先问一下面试官用户最多可以添加多少个代办事项。如果永远不会超过 10 个，那上面的代码运行起来就没有问题。但如果用户输入待办事项的数量没有上限，那你就得换一个更高效的解决方案。

如果应用有上百个事件监听器，更高效的解决方案是给最外层的容器添加**一个**事件监听器，当用户真正点击的时候再去获取实际被点击的代办事项。这被称为[事件代理](https://davidwalsh.name/event-delegate)，这比给每个代办事项都单独添加事件监听器更高效。

下面是事件代理的代码：

```
document.addEventListener('DOMContentLoaded', function() {
  
  let app = document.getElementById('todo-app');
  
  // 给容器添加事件监听器
  app.addEventListener('click', function(e) {
    if (e.target && e.target.nodeName === 'LI') {
      let item = e.target;
      alert('you clicked on item: ' + item.innerHTML);
    }
  });
  
});
```

## 问题 #2: 在循环中使用闭包

面试中经常会问到闭包，因为面试官能通过这个问题的回答判断你对语言的熟悉程度，以及考察你是否知道什么时候使用闭包。

闭包就是能访问作用域外部变量的[内部函数](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36#.44xk49tyt) 。闭包能用来实现[私有化](https://medium.com/written-in-code/practical-uses-for-closures-c65640ae7304#.70gp35hbn)和创建[工厂函数](https://medium.com/javascript-scene/javascript-factory-functions-vs-constructor-functions-vs-classes-2f22ceddf33e#.1817w0lmb)等作用。关于闭包的常见面试题是这样的：

_写一个函数，循环一个整数数组，延迟 3 秒打印这个数组中每个元素的索引。_

这个问题常见（不正确）的实现是这样：

```JavaScript
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  setTimeout(function() {
    console.log('The index of this number is: ' + i);
  }, 3000);
}
```

如果你运行这段函数，你会发现 3 秒之后每次都打印的是 **4**，而不是预期的 **0, 1, 2, 3**。

为了正确的找到出现这种情况的原因，你需要理解 JavaScript 是如何运行这段代码的，这也是面试官想要考察你的地方。

原因是 `setTimeout` 函数创建了一个访问外部作用域的函数（闭包），就是包含索引 `i` 的那个循环。3 秒之后，函数开始执行打印 `i` 的值，而此时循环也结束了，`i` 的值已经是 4。因为循环遍历 0, 1, 2, 3, 4 后最终停在了 4。

实际上有[好几种方法](http://stackoverflow.com/questions/3572480/please-explain-the-use-of-javascript-closures-in-loops)能[正确](https://coderbyte.com/algorithm/3-common-javascript-closure-questions)解决这个问题。这里有两个：

```
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  // 给每个函数传入变量 i 让其能访问正确的索引
  setTimeout(function(i_local) {
    return function() {
      console.log('The index of this number is: ' + i_local);
    }
  }(i), 3000);
}
```
```
const arr = [10, 12, 15, 21];
for (let i = 0; i < arr.length; i++) {
  // 使用 ES6 中的 let 关键字，它会在函数调用时创建一个新的绑定
  // 了解更多：http://exploringjs.com/es6/ch_variables.html#sec_let-const-loop-heads
  setTimeout(function() {
    console.log('The index of this number is: ' + i);
  }, 3000);
}
```

## 问题 #3： Debouncing（防抖动）

有些浏览器事件能在很短的时间内被触发多次，例如调整窗口大小或滚动页面。如果你给窗口滚动事件添加一个事件监听器，然后用户不停地快速向下滚动页面，那你的事件可能在 3 秒之内被触发数千次。这会导致非常严重的性能问题。

如果在面试中讨论到构建应用程序，以及滚动事件，窗口调整事件，或者键盘事件等，请务必提及 debouncing 或者 throttling，作为提高页面速度与性能的方法。来一个 [css-tricks](https://css-tricks.com/debouncing-throttling-explained-examples/) 的实例：

> 2011 年，Twitter 出了一个问题：当滚动 Twitter 摘要时，页面变的很卡甚至无响应。John Resig 写了[一篇关于这个问题的博客](http://ejohn.org/blog/learning-from-twitter)，解释了直接将耗时的函数绑定在 `scroll` 事件上是一个多么糟糕的想法。

Debouncing 是解决这个问题的一种方法，它的做法是限制下次函数调用之前必须等待的时间间隔。正确实现 debouncing 的方法是将若干个函数调用 _合成_ 一次，并在给定时间过去之后仅被调用一次。下面是一个原生 JavaScript 的实现，用到了[作用域](https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/), 闭包, [this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this), 和 [计时事件](http://www.w3schools.com/jsref/met_win_settimeout.asp)：

```
// 将会包装事件的 debounce 函数
function debounce(fn, delay) {
  // 维护一个 timer
  let timer = null;
  // 能访问 timer 的闭包
  return function() {
    // 通过 ‘this’ 和 ‘arguments’ 获取函数的作用域和变量
    let context = this;
    let args = arguments;
    // 如果事件被调用，清除 timer 然后重新设置 timer
    clearTimeout(timer);
    timer = setTimeout(function() {
      fn.apply(context, args);
    }, delay);
  }
}
```

这个函数 — 当传入一个事件（fn）时 — 会在经过给定的时间（delay）后执行。

函数这样用：

```
// 当用户滚动时被调用的函数
function foo() {
  console.log('You are scrolling!');
}

// 在 debounce 中包装我们的函数，过 2 秒触发一次
let elem = document.getElementById('container');
elem.addEventListener('scroll', debounce(foo, 2000));
```

Throttling 是与 debouncing 类似的一种技术，但它不是在调用函数之前等待一段时间，throttling 是在较长的时间间隔内调用函数。所以如果一个事件每 100 毫秒被触发 10 次，throttling 会在每隔 2 秒时执行一次这个函数，而不是在 100 毫秒内执行 10 次事件。

更多关于 debouncing 和 throttling 的信息请参考以下文章和教程：

*   [JavaScript 中的 Throttling 和 Debouncing](https://medium.com/@_jh3y/throttling-and-debouncing-in-javascript-b01cad5c8edf#.ly8uqz8v4)

*   [Throttling 与 Debouncing 的区别](https://css-tricks.com/the-difference-between-throttling-and-debouncing/)

*   [实例解析 Throttling 和 Debouncing](https://css-tricks.com/debouncing-throttling-explained-examples/)

*   [Throttling 函数调用 —— Remy Sharp](https://remysharp.com/2010/07/21/throttling-function-calls)

* * *

如果你喜欢阅读这篇文章，那你或许也喜欢阅读 JavaScript 教程，参与我在 [Coderbyte](https://coderbyte.com/) 上主持的 JavaScript 编码挑战。期待你的想法！
                