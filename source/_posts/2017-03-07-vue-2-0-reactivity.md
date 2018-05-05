---
layout: post 
category : JavaScript
title: Vue2.0 源码阅读：响应式原理
date: 2017-03-07 17:07:44
tags: [JavaScript, Vue, 源码] 
---


> 本文基于 Vue.js 2.1.10

## 基础知识

在讲源码之前，我们了解一些阅读源码过程中必备的基础知识：Object.defineProperty，观察者模式，Watcher，Dep 以及 Observer 类。

<!-- more -->

### Object.defineProperty

Vue.js 在[官网](https://cn.vuejs.org/v2/guide/reactivity.html#异步更新队列)中提到：

> 把一个普通 Javascript 对象传给 Vue 实例的 data 选项，Vue 将遍历此对象所有的属性，并使用 `Object.defineProperty` 把这些属性全部转为 getter/setter。Object.defineProperty 是仅 ES5 支持，且无法 shim 的特性，这也就是为什么 Vue 不支持 IE8 以及更低版本浏览器的原因。

其中 `Object.defineProperty` 是与 `Angular` 的脏值检查不同的 `MVVM` 实现方式，是实现响应式的关键。该方法的具体语法与使用请参考 [MDN: Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)。

需要重点提及的是 `descriptor` 中 `get` 和 `set` 方法。看一个简单的示例：

```javascript
var obj = {};
var a;
Object.defineProperty(obj, 'a', {
  get: function() {
    console.log('get val');　
    return a;
  },
  set: function(newVal) {
    console.log('set val:' + newVal);
    a = newVal;
  }
});
obj.a;     // get val 
obj.a = '111'; // set val: 111
```

示例代码中 Object.defineProperty 把 obj 的 a 属性转化为 getter 和 setter，可以实现 obj.a 的数据监控。这个转化是 Vue.js 响应式的基石。

### 观察者模式

[维基百科](https://zh.wikipedia.org/wiki/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F)中对观察者模式的定义：
> 观察者模式是软件设计模式的一种。在此种模式中，一个目标对象管理所有相依于它的观察者对象，并且在它本身的状态改变时主动发出通知。这通常透过呼叫各观察者所提供的方法来实现。此种模式通常被用来实时事件处理系统。

订阅者模式涉及三个对象：发布者、主题对象、订阅者，三个对象间的是一对多的关系，每当主题对象状态发生改变时，其相关依赖对象都会得到通知，并被自动更新。看一个简单的示例：

```javascript
function Dep() {//主题对象
  this.subs = []; //订阅者列表
}

Dep.prototype.notify = function() { //主题对象通知订阅者
  this.subs.forEach(function(sub){ //遍历所有的订阅者，执行订阅者提供的更新方法
    sub.update();
  });
}

function Sub(x) { //订阅者
  this.x = x;
}

Sub.prototype.update = function() { //订阅者更新
  this.x = this.x + 1;
  console.log(this.x);
}

var pub = { //发布者
  publish: function() {
    dep.notify();
  }
};

var dep = new Dep(); //主题对象实例
Array.prototype.push.call(dep.subs, new Sub(1), new Sub(2), new Sub(4)); //新增 3 个订阅者

pub.publish(); //发布者发布更新

// 2
// 3
// 5
```

代码示例中发布者（pub）发出通知（notify），主题对象（Dep）收到通知并推送给订阅者（Sub），订阅者执行相应操作（update）。
Vue.js 也是通过这个模式来实现数据驱动视图。上一个小节讲的 setter 方法就是一个发布者，后面我们会详细讲到。


### Observer、Watcher、Dep

Observer、Watcher、Dep 是响应式原理中涉及到的 3 个重要的对象。

> 代码中的英文注释均为源码中自带，中文注释为作者添加。

**Observer**

Vue 中的数据对象都会在初始化过程中转化为 Observer 对象。

我们通过源码看一下 Observer 对象的结构，Observer 对象的 constructor 函数（[src/core/observer/index.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/index.js#L39-L53)）：
```javascript
  constructor (value: any) {
    this.value = value
    this.dep = new Dep()  //一个 Dep对象实例，Watcher 和 Observer 之间的纽带
    this.vmCount = 0
    def(value, '__ob__', this)  //把自身 this 添加到 value 的 __ob__ 属性上
    if (Array.isArray(value)) { //对 value 的类型进行判断
      const augment = hasProto
        ? protoAugment
        : copyAugment
      augment(value, arrayMethods, arrayKeys) // 数组增强方法
      this.observeArray(value) //如果是数组则观察数组
    } else {
      this.walk(value) //否则观察单个元素。
    }
  }
```
Observer 对象的标志就是 \__ob__ 这个属性，这个属性保存了 Observer 对象自己本身。对象在转化为 Observer 对象的过程中是一个递归的过程，对象的子元素如果是对象或数组的话，也会转化为 Observer 对象。

> Note: 由于 JavaScript 的限制， Vue 不能检测数组的变化，于是作者在数组增强方法中对 Array 的  'push',  'pop',  'shift',  'unshift',  'splice',  'sort',  'reverse' 方法做了增强实现，具体实现可以看[源码](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/array.js)，这也是 Vue.js 中数组操作只能使用这 7 个方法的原因。

**Watcher**

Watcher 是将模板和 Observer 对象结合在一起的纽带。Watcher 是订阅者模式中的订阅者。Watcher 的来源有 3 种，代码中的 computed 属性（Tips：若该 computed 属性未再模板中使用，则不会生成 Watcher 实例）、watch 函数以及模板编译过程中的指令和数据绑定。

我们看一下源码中 Watcher 的 constructor 函数（[src/core/observer/watcher.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/watcher.js#L39-L85)）：

```javascript
  constructor (
    vm: Component,
    expOrFn: string | Function,
    cb: Function,
    options?: Object
  ) {
    this.vm = vm
    vm._watchers.push(this)// 将当前 Watcher 类推送到对应的 Vue 实例中
    ......
    // parse expression for getter
    if (typeof expOrFn === 'function') {
      // 如果是函数，相当于指定了当前订阅者获取数据的方法，每次订阅者通过这个方法获取数据然后与之前的值进行对比
      this.getter = expOrFn
    } else {
      this.getter = parsePath(expOrFn)// 否则的话将表达式解析为可执行的函数
      ......
    }
    this.value = this.lazy
      ? undefined
      : this.get()   //如果 lazy 不为 true，则执行 get 函数进行依赖收集
  }
```
Watcher 的两个参数： expOrFn 最终会被转换为 getter 函数， cb 是更新时执行的回调。依赖收集的入口就是 `get` 函数。

源码中 Watcher 的 get 函数（[src/core/observer/watcher.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/watcher.js#L87-L101)）：
```javascript
/**
* Evaluate the getter, and re-collect dependencies.
*/
get () {
    pushTarget(this)  // 设置全局变量 Dep.target，将 Watcher 保存在这个全局变量中，2.0 变成了 pushTarget 没有细看为什么
    const value = this.getter.call(this.vm, this.vm) // 调用 getter 函数，进入 get 方法进行依赖收集操作
    // "touch" every property so they are all tracked as
    // dependencies for deep watching
    if (this.deep) {
      traverse(value)
    }
    popTarget()  // 将全局变量 Dep.target 置为 null
    this.cleanupDeps()
    return value
 }
```
getter 函数是用来连接监控属性与 Watcher 的关键。`const value = this.getter.call(this.vm, this.vm)` 这一行就是去 `touch` Watcher 初始化时传入的参数 expOrFn 中涉及到的每一项数据，然后触发该数据项的 getter 函数；设置 Dep.target 是个依赖收集过程中的重要一步，getter 函数中就是通过判断 Dep.target 的有无来判断是 Watcher 初始化时调用的还是普通数据读取，如果有则进行依赖收集。

**Dep**

Dep 类是 Watcher 和 Observer 之间的纽带。每一个 Observer 都有一个 Dep 实例，用来存储订阅者 Watcher。

看一下源码中 Dep 类的 constructor 函数（[src/core/observer/dep.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/dep.js#L17-L20)）：

```javascript
constructor () {
    this.id = uid++
    this.subs = [] //存储 Watcher 实例的数组
}
```
我们看到源码中这个类非常简单，只有一个 id 和一个数组 subs。

源码中 Dep 类的 notify 函数（[src/core/observer/dep.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/dep.js#L36-L42)）：

```javascript
notify () {
    // stablize the subscriber list first
    const subs = this.subs.slice()
    for (let i = 0, l = subs.length; i < l; i++) {   //遍历 Watcher 列表，调用 update 方法进行更新操作
        subs[i].update()
    }
}
```
这个方法是在响应式的过程中调用的，用户修改数据触发 setter 函数，函数的最后一行就是调用 dep.notify 去通知订阅者更新视图。


## 源码解析

了解上面三个重要的类之后，我们接下来通过源码深入了解数据双向绑定的过程。

### 源码目录结构

阅读源码之前，我们首先介绍一下相关源码的目录。

src
 |--- compile    模板编译的代码，1.0 和 2.0 版本在模板编译这一块改动非常大
 |--- core/instance    生命周期，初始化入口
 |--- core/observer    响应式
 |--- core/vdom     虚拟DOM
 |--- entries     编译入口文件
 
本文中涉及的响应式代码大部分都在 `src/core/observer/` 文件夹下。

### 生命周期

**从现在开始推荐 WebStrom 查看源码（按住 control 键单击方法名可以直接跳转，源码阅读神器），或者在浏览器中打断点跟着本文的顺序往下看源码。**

我们先找到源码入口（即 [new Vue](https://github.com/vuejs/vue/blob/v2.1.10/src/core/instance/index.js#L8-L14)）开始讲，入口文件是 [src/core/instance/index.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/instance/index.js)，这段代码很简单，我就不列出来了，里面就是提示需要用 `new` 来实例化 Vue ，然后调用 `this._init` 方法开始 Vue 的生命周期。生命周期的示意图可以参考官网：

![生命周期图](https://cn.vuejs.org/images/lifecycle.png)

> 这个图与目前的版本（2.1.10）代码稍有差异，主要是一些初始化的顺序。不影响对响应式原理的理解。


我们看一下生命周期源码（[src/core/instance/init.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/instance/init.js#L40-L48)）：
```javascript
initLifecycle(vm)   //vm 的生命周期相关变量初始化
initEvents(vm)    // vm 的事件监控初始化
initRender(vm)  // 模板解析
callHook(vm, 'beforeCreate')
initState(vm) //vm 的状态初始化，prop/data/computed/method/watch 都在这里完成初始化，是响应式的关键步！
callHook(vm, 'created')
if (vm.$options.el) {
  vm.$mount(vm.$options.el)
}
```
initLifecycle 主要是初始化 vm 实例上的一些参数；initEvents 是事件监控的初始化；initRender 是模板解析，值得一提的是在 2.0 的版本中这一块有很大的改动，1.0 的版本中 Vue 使用的是 [DocumentFragment](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment) 来进行模板解析，而 2.0 之后作者采用 John Resig 的 [HTML Parser](https://github.com/vuejs/vue/blob/v2.1.10/src/compiler/parser/html-parser.js) 将模板解析成可直接执行的 render 函数，这是模板预编译和服务端渲染（SSR）的前提；callHook(vm, 'beforeCreate')是执行钩子函数，就是你在 new Vue 实例的时候写的 beforeCreate 方法；initState 是本文的重点，我们下一节会详细讲到；callHook(vm, 'created')也是执行钩子函数；最后是是执行 mount 函数。

### initState

跟随代码走入 `initState` 里面，我们看一下源码（[src/core/instance/state.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/instance/state.js#L24-L36)）：
```javascript
export function initState (vm: Component) {
  vm._watchers = []  //新建一个订阅者列表
  const opts = vm.$options
  if (opts.props) initProps(vm, opts.props)  // 初始化 Props，与 initData 差不多
  if (opts.methods) initMethods(vm, opts.methods)  // 初始化 Methods，Methods 的初始化比较简单，就是作用域的重新绑定。
  if (opts.data) {
    initData(vm) // 初始化 Data，响应式关键步
  } else {
    observe(vm._data = {}, true /* asRootData */) //如果没有 data，则观察一个空对象
  }
  if (opts.computed) initComputed(vm, opts.computed)// 初始化 computed，这部分会涉及 Watcher 类以及依赖收集，computed 其实本身也是一种特殊的 Watcher
  if (opts.watch) initWatch(vm, opts.watch)// 初始化 watch，这部分会涉及 Watcher 类以及依赖收集
}
```
代码中每一步都有注释，这些初始化都涉及到数据转化为 Observer 对象的过程，我们会以 initData 为例来讲响应式。

### initData

进入 initData 方法，我们看源码（[src/core/instance/state.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/instance/state.js#L74-L104)）：

```javascript
function initData (vm: Component) {
  let data = vm.$options.data
  data = vm._data = typeof data === 'function'
    ? data.call(vm)
    : data || {}
  if (!isPlainObject(data)) {// 保证data必须为纯对象
    ......
  }
  // proxy data on instance
  const keys = Object.keys(data)
  const props = vm.$options.props
  let i = keys.length
  while (i--) {
    if (props && hasOwn(props, keys[i])) {// 是props，则不代理
      ...... //如果和 props 里面的变量重了，则抛出 Warning
    } else {// 否则将属性代理的 vm 上，这样就可以通过 vm.xx 访问到 vm._data.xx
      proxy(vm, keys[i]) //proxy方法遍历 data 的 key，把 data 上的属性代理到 vm 实例上
    }
  }
  // observe data
  observe(data, true /* asRootData */)  //关键步！observe(data, this)方法来对 data 做监控
}
```
这个函数做了以下几件事情：

* 保证 data 为纯对象
* 判断与 props 里的属性是否有重复，有就报错
* 进行数据代理，方便数据读取，代理后我们可以直接使用 vm.key，而不需要 vm._data.key
* 调用 observe 方法，这是响应式的关键步！

### observe

observe 方法会为传进来的 value 值创建一个 Observer 对象，创建之前会做一些判断。看一下源码（[src/core/observer/index.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/index.js#L101-L126)）:
```javascript
/**
 * Attempt to create an observer instance for a value,
 * returns the new observer if successfully observed,
 * or the existing observer if the value already has one.
 * 返回一个 Observer 对象
 */
export function observe (value: any, asRootData: ?boolean): Observer | void {
  if (!isObject(value)) {  //如果不是对象和数组则不监控，直接返回
    return
  }
  let ob: Observer | void
  //判断 value 是否已经添加了 __ob__ 属性，并且属性值是 Observer 对象的实例。避免重复引用导致的死循环
  if (hasOwn(value, '__ob__') && value.__ob__ instanceof Observer) {    //如果是就直接用
    ob = value.__ob__
  } else if (
    observerState.shouldConvert && //只有 root instance props 需要创建 Observer 对象
    !isServerRendering() && //不是服务端渲染
    (Array.isArray(value) || isPlainObject(value)) && //数组或者普通对象
    Object.isExtensible(value) && //可扩展对象
    !value._isVue // 非 Vue 组件
  ) {
    ob = new Observer(value)  //关键步！在 value 满足上述条件的情况下创建一个 Observer 对象
  }
  if (asRootData && ob) {
    ob.vmCount++
  }
  return ob // 返回一个 Observer 对象
}
```
observe 方法主要就是判断 value 是否已经是 Observer 对象，如果是直接返回；否则，若干个判断条件成立则将这个对象转化为 Observer 对象。

### Observer 类

上面基础知识我们已经讲过 Observer 类，其构造函数主要做了这么几件事：

* 首先创建了一个 Dep 对象实例；
* 然后把自身 this 添加到 value 的 \__ob__ 属性上；
* 最后对 value 的类型进行判断，如果是数组则观察数组，否则观察单个元素（要理解这一步是个递归过程，即 value 的元素如果符合条件也需要转化为 Observer 对象）。

其实 [observeArray](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/index.js#L67-L75) 方法就是对数组进行遍历，递归调用 [observe](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/index.js#L101-L126) 方法，最终都会走入 [walk](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/index.js#L55-L65) 方法（代码很简单，就不单独列出来，可以戳[链接](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/index.js#L55-L65)进入 github 查看）监控单个元素。而 walk 方法就是遍历对象，结合 [defineReactive](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/index.js#L128-L184) 方法递归将属性转化为 getter 和 setter。

### defineReactive

此方法是最终实现响应式的**重点！基石！核心！**看一下源码（[src/core/observer/index.js](https://github.com/vuejs/vue/blob/v2.1.10/src/core/observer/index.js#L128-L184)）：

```javascript
/**
 * Define a reactive property on an Object.
 */
export function defineReactive (
  obj: Object,
  key: string,
  val: any,
  customSetter?: Function
) {
  const dep = new Dep()  //每个对象都会有一个 Dep 实例，用来保存依赖 (Watcher 对象)
  ......
  let childOb = observe(val)   //结合 observe 函数进行将对象的对象也变成监控对象
  // 最重点、基石、核心的部分：通过调用 Object.defineProperty 给 data 的每个属性添加 getter 和 setter 方法。
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter () {
      const value = getter ? getter.call(obj) : val
      // 依赖收集的重要步骤
      if (Dep.target) {//如果存在 Dep.target 这个全局变量不为空，表示是在新建 Watcher 的时候调用的，代码已经保证
        dep.depend()    // 依赖收集
        if (childOb) {
          childOb.dep.depend() // 处理好子元素的依赖 watcher
        }
        if (Array.isArray(value)) { // 如果是数组，进一步处理
          dependArray(value)
        }
      }
      return value
    },
    set: function reactiveSetter (newVal) {
      const value = getter ? getter.call(obj) : val
      ......
      childOb = observe(newVal)    // 对新数据重新 observe，更新数据的依赖关系
      dep.notify()   // 通知 dep 进行数据更新，这个方法在前面的 Dep 类中讲过
    }
  })
}
```
defineReactive 是对 Object.defineProperty 方法的包装，结合 observe 方法对数据项进行深入遍历，最终将所有的属性就转化为 getter 和 setter。至此，所有的数据都已经转换为 Observer 对象。即数据的读操作都会触发 getter 函数，写操作都会触发 setter 函数。


### 依赖收集

在上面介绍源码的过程中，我们多次提到过依赖收集，这具体是一个什么样的过程，它是如何将模板中的指令与数据关联在一起的呢？接下来我们就对依赖收集过程进行一个总结。

依赖收集是通过属性的 getter 函数完成的，文章一开始讲到的 `Observer`、`Watcher`、`Dep` 都与依赖收集相关。其中 Observer 与 Dep 是一对一的关系， Dep 与 Watcher 是多对多的关系。Dep 则是 Observer 和 Watcher 之间的纽带。依赖收集完成后，当属性变化会执行主题对象（Observer）的 `dep.notify` 方法，这个方法会遍历订阅者（Watcher）列表向其发送消息，Watcher 会执行 `run` 方法去更新视图。依赖的关联关系具体可参考下图：

![依赖收集](http://static.galileo.xiaojukeji.com/static/tms/shield/vue-reactive.jpg)

总结依赖关系建立的步骤：
 > 1. 模板编译过程中的指令和数据绑定都会生成 Watcher 实例，computed 属性和 watch 函数中的对象也会生成 Watcher 实例，在模板编译的过程中，会执行 Watcher 实例的 expOrFn（初始化 Watcher 实例时传入的参数），进入 watcher.js 中的 get 函数 `访问` expOrFn 涉及的所有属性；
 > 2. 访问属性之前，Watcher 会设置 Dep 的静态属性 Dep.target 指向其自身，然后开始依赖收集；
 > 3. 访问属性的过程中，属性的 getter 函数会被访问；
 > 4. 属性 getter 函数中会判断 Dep.target（target 中保存的是第 2 步中设置的 Watcher 实例）是否存在，若存在则将 getter 函数所在的 Observer 实例的 Dep 实例保存到 Watcher 的列表中，并在此 Dep 实例中添加 Watcher 为订阅者；
 > 5. 重复上述过程直至 Watcher 的 expOrFn 涉及的所有属性均访问结束（即 expOrFn 数中所有的数据的 getter 函数都已被触发），Dep.target 被置为 null，依赖收集完成；

以上就是模板中的指令与数据关联起来的步骤。当数据发生改变后，相应的 setter 函数被触发，然后执行 notify 函数通知订阅者（Watcher）去更新相关视图，也会对新的数据重新 observe，更新相关的依赖关系。

## 总结

以上就是响应式原理的源码介绍，一图胜千言，我们看一下下面这张图：

![Vue 2.0 响应式原理](Vue Reactivity.svg)

总结来说就是：

* 在生命周期的 `initState` 方法中将 `data`、`prop` 中的数据劫持，通过 `observe` 方法与 `defineReactive` 方法将相关对象转换为 `Observer` 对象；

* 然后在 `initRender` 方法中解析模板，通过 `Watcher` 对象，`Dep` 对象与观察者模式将模板中的指令与对应的数据建立依赖关系，在这个依赖收集的过程中，使用了全局对象 `Dep.target` ；

* 最后，当数据发生改变时，触发 `Object.defineProperty` 方法中的 `dep.notify` 方法，遍历该数据的依赖列表，执行其 `update` 方法通知 Watcher 进行视图更新。


## 参考链接

* [Vue 实例](https://cn.vuejs.org/v2/guide/instance.html)
* [深入响应式原理](https://cn.vuejs.org/v2/guide/reactivity.html)
* [Vue 响应式原理探析](https://zjy.name/archives/vue-reactive-study.html)
* [Vue原理解析之 observer 模块](https://segmentfault.com/a/1190000008377887)
* [Vue 源码解析：深入响应式原理（上）](http://www.imooc.com/article/14466)



