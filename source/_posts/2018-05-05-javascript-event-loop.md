---
layout: post 
category : JavaScript
title: 从 Vue.nextTick 源码看 JavaScript 事件循环
date: 2018-05-05 19:55:59
tags: [JavaScript, Vue, Event Loop] 
---

## Vue.nextTick ##

虽然 Vue 建议避免直接操作 DOM，但在业务开发中，有时候不得不在数据变化之后操作 DOM，然而我们知道 Vue 默认是[异步执行 DOM 更新](https://cn.vuejs.org/v2/guide/reactivity.html#%E5%BC%82%E6%AD%A5%E6%9B%B4%E6%96%B0%E9%98%9F%E5%88%97)，数据变化之后所有需要更新的 Watcher 会被推入一个队列，然后，在下一个的事件循环 “tick” 中，Vue 刷新队列并执行更新。那如果我想在数据变化之后操作 DOM 该如何做呢？Vue 提供了 nextTick 方法。

<!-- more -->

在 Vue 源码 2.1.10 版本中 nextTick 的实现采用优雅降级的 3 种方式：Promise、MutationObserver 和 setTimeout。到最新的一个版本的 2.5.16 改为了 setImmediate、MessageChannel、Promise 和 setTimeout。

作者在源码中给出了如下的[注释](https://github.com/vuejs/vue/blob/v2.5.16/src/core/util/next-tick.js#L20~L27)：

> Here we have async deferring wrappers using both microtasks and (macro) tasks. In < 2.4 we used microtasks everywhere, but there are some scenarios where microtasks have too high a priority and fire in between supposedly sequential events (e.g. #4521, #6690) or even between bubbling of the same event (#6566). However, using (macro) tasks everywhere also has subtle problems when state is changed right before repaint (e.g. #6813, out-in transitions). Here we use microtask by default, but expose a way to force (macro) task when needed (e.g. in event handlers attached by v-on).

> 大概意思就是 nextTick 方法使用了 microtask 和 (macro) task。低于 2.4 的版本中到处都用 microtask，但有些场景下因为 microtask 优先级太高执行的太早了，然而用 (macro) task 也有一些小问题。所以，默认还是使用 microtask，但提供一种方式在必要的情况下可以使用 (macro) task。

## microtask 与 (macro) task ##

注释中提到了两个名词：`microtask` 和 `(macro) task`，这两个是属于 JavaScript Event Loop（事件循环）中的概念。上面提到的 Promise（浏览器原生实现）、MutationObserver、MessageChannel 属于 microtask，setImmediate 和 setTimeout 属于 (macro) task。

分类：
> (macro) task：script（整体代码）、setTimeout、setInterval、 setImmediate、requestAnimationFrame、I/O、UI Rendering。

> microtask：process.nextTick、Promise、Object.observe、MutationObserver。

我们知道 JavaScript 是单线程语言，所有任务都在主线程上排队等待执行（在执行栈中执行）。但那些耗时的 I/O 操作或者 Ajax 异步请求，线程不可能一直等待，所以，JavaScript 将任务分为`同步任务`和`异步任务`，同步任务（我理解就是整体代码）在主线程上排队顺序执行，异步任务分为 (macro) task 和 microtask，基于事件循环机制执行任务。主线程上的同步任务执行完了（即主线程空闲时），才会执行异步任务。

事件循环由三部分组成：执行 (macro) task，执行 microtask，UI 渲染。具体步骤：执行一个 (macro) task（整体代码也是一个 macro task），然后清空 microtask 队列，最后 UI 渲染（并不是每次都会执行渲染，浏览器只需保证 60Hz 的刷新率即可）。

看一下浏览器执行一次 UI 渲染的基本流程：

* 解析 HTML 构建 DOM tree；
* 解析 CSS 构建 CSSOM tree；
* 结合 DOM tree 与 CSSOM tree 构建 render tree；
* 根据 render tree 计算每个节点的位置信息；
* 在屏幕上绘制节点。

可以看到，一次 UI 渲染浏览器的计算量是非常大的，所以 Vue 在 nextTick 函数的实现中优先使用 microtask，因为一次渲染之前只会执行一个 (macro) task，但会执行完所有的 microtask。

## 实例解析 ##

看一个常见的面试题：
```
console.log('start')

setTimeout( function () {
  console.log('setTimeout')
}, 0 )

new Promise(function() {
  console.log('promise0')
}).then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});

console.log('end')

// start
// promise0
// end
// promise1
// promise2
// setTimeout
```
其中整块代码是属于同步代码，setTimeout 是 (macro) task，Promise 是 microtask。所以，先顺序执行代码，输出 start，然后将 setTimeout 放入 异步的 (macro) task 队列，初始化 Promise 会立即执行输出 promise0，then 的回调会放入异步的 microtask 队列，再顺序执行输出 end。这时主进程空闲，会优先处理 microtask，输出 promise1 和 promise2，microtask 队列被清空，再取一个 (macro) task 队列中的任务执行，输出 setTimeout。

> 事件循环中，microtask 队列只有一个，但 (macro) task 队列不止一个，每个 task 都有一个 task source，每个 source 对应一个队列，相应的优先级也不一样，这里就不展开写。

> 注意：浏览器中的 Event Loop 和 Node.js 中的 Event Loop 不一样。

## 扩展阅读 ##

* [Vue源码详解之nextTick：MutationObserver只是浮云，microtask才是核心！](https://github.com/Ma63d/vue-analysis/issues/6)
* [Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)
* [Promise的队列与setTimeout的队列有何关联？ - 何幻的回答 - 知乎](https://www.zhihu.com/question/36972010/answer/71338002)
* [从event loop规范探究javaScript异步及浏览器更新渲染时机](https://github.com/aooy/blog/issues/5)



