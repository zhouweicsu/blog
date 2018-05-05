
---
layout: post 
category : JavaScript
title: Vue：记一次诡异无效的点击 toggle 效果
date: 2017-07-01 19:55:59
tags: [JavaScript, Vue, computed, 响应式] 
---


### 问题描述
最近在项目中遇到一个问题，后端返回一个如下格式的数据 `list`：
```
[
  {
    "status": 1,
    "detail": [
        {"startDate": "2017-05-12"},
        {"startDate": "2017-05-12"}
    ]
  },
  {
    "status": 1,
    "detail": [
        {"startDate": "2017-05-12"},
        {"startDate": "2017-05-12"}
    ]
  }
]

```


<!-- more -->

前端做数据展现，因为数据量很大，所以需要将二级数据隐藏，即 `detail` 数组字段中的列表数据默认不展示，需要一个 toggle 效果，用户点击之后展示，再点击则隐藏，根据 Vue 数据驱动的思想，给 `list` 中每个对象新增一个属性 `isShow`，默认为 false。用该属性控制 `detail` 属性中数据的展现与隐藏，点击将 `isShow` 置为 true，再点击置为 false。但是问题出来了，点击之后 `isShow` 属性的值改过来了，但是页面的却没有展示 `detail` 数组中的数据。

### 问题分析

为了分析以上出现的问题，简化一个 demo1：
``` HTML
<ul id="app1">
  <li v-for="item in list">
    <p @click="toggle(item)">{{item.startDate}}</p>
    <p>{{item.isShow}}</p>
    <div v-show="item.isShow">I'm show</div>
  </li>
</ul>
```
```
new Vue({
  el: '#app1',
  data: {
    olist: [{startDate: 'click me'}, {startDate: 'click me'}]
  },
  computed: {
    list() {
      const list = []
      this.olist.forEach(item => {
        item.isShow = false
        list.push(item)
      })
      return list
    }
  },
  methods: {
    toggle(item){
      item.isShow = !item.isShow
    }
  }
})
```
[demo1 地址](http://jsbin.com/tehikihaxi/edit?html,js,output)
我们发现点击页面的 `click me`，`I'm show` 没有显示。用 Chrome 的 Vue 插件工具调试，发现 isShow 属性已经变为 true，即 isShow 的变化未触发界面的更新。

进一步调试，发现在点击同时修改 `startDate`，isShow 的效果会生效。demo2：
``` javascript
new Vue({
  el: '#app2',
  data: {
    olist: [{startDate: 'click me'}, {startDate: 'click me'}]
  },
  computed: {
    list() {
      const list = []
      this.olist.forEach(item => {
        item.isShow = false
        list.push(item)
      })
      return list
    }
  },
  methods: {
    toggle(item){
      item.startDate = Math.random()  //新增这一行 isShow 会生效
      item.isShow = !item.isShow
    }
  }
})
```
[demo2 地址](http://jsbin.com/naqofakaja/edit?html,js,output)
到这一步，猜测 computed 中的新对象 list 只监控了 startDate 属性，未监控 isShow 属性。即只监控 olist 中已有的属性，新增属性未加入响应式列表，因为 olist 是响应式的，而  在生成 list 的过程中直接 push 的 item 是一个对象，那么 list 中的对象指针与 olist 中的对象指针指向同一个对象，那么 olist 中的对象是 Observer 对象，那 list 中也是，而新增的 isShow 属性未执行 defineProperty 过程。为了验证上面的猜测，再来看看 demo3：
```javascript
new Vue({
  el: '#app3',
  data: {
    olist: [{startDate: 'click me',isShow: false}, {startDate: 'click me', isShow: false}]  //将 isShow 加入 olist 中
  },
  computed: {
    list() {
      const list = []
      this.olist.forEach(item => {
        //item.isShow = false  // 去掉这一行，将 isShow 直接放入 olist 中
        list.push(item)
      })
      return list
    }
  },
  methods: {
    toggle(item){
      //item.startDate = Math.random()
      item.isShow = !item.isShow
    }
  }
})
```
[demo3 地址](http://jsbin.com/lakefopedu/edit?html,js,output)
我们看到将 isShow 直接放入 olist 中，toggle 效果是有的。

### 问题原因
通过以上分析，我们知道 toggle 效果未生效的原因就是**新增的 isShow 属性未被监控**，没有触发响应式，找到原因就可以给出解决办法。

### 解决方案
查看 Vue 的官方文档[深入响应式原理](http://vuejs.org/v2/guide/reactivity.html)一节中关于“向已有对象上添加一些属性”的介绍，我们可以通过以下的方案一和方案二解决上述问题。而 Vue 2.0 中提供的一个新方法也可以解决 DOM 未更新的问题。

#### 方案一：splice + Object.assign
利用 Vue 中重写数组的 splice 方法，与 Object 的 assign 方法，可以将 olist 中的数据重新变成 Observer 对象。demo4：
```javascript
new Vue({
  el: '#app4',
  data: {
    olist: [{startDate: 'click me'}, {startDate: 'click me'}]
  },
  mounted() {
    this.dealData()
  },
  methods: {
    toggle(item){
      //item.startDate = Math.random()
      item.isShow = !item.isShow
    },
    dealData() {
      this.olist.forEach((item,index) => {
        // item.isShow = false
        // this.olist.splice(index,1,item)
        this.olist.splice(index,1,Object.assign({isShow: false}, item))
      })
    }
  }
})
```
[demo4 地址](http://jsbin.com/kataliwawa/edit?html,js,output)
在实现的过程中我发现，同样使用 splice + Object.assign 方法去 computed 一个新的 list 无法达到同样的效果。具体查看 [demo5 地址](http://jsbin.com/lezunudoja/1/edit?html,js,output)。读者可以想想为什么。其实很简单.jpg

本次业务场景中，只需要展示该数据，不需要处理返回，就不用 computed 一个新数组，而直接使用服务端返回来的对象。


#### 方案二：$set
Vue 官方文档的[深入响应式原理](http://vuejs.org/v2/guide/reactivity.html)一节中提到，我们可以使用 Vue.set 或者 vm.$set 方法将响应属性添加到嵌套的对象上，看一下 demo6：
```
new Vue({
  el: '#app6',
  data: {
    olist: [{startDate: 'click me'}, {startDate: 'click me'}]
  },
  mounted() {
    this.dealData()
  },
  methods: {
    toggle(item){
      item.isShow = !item.isShow
    },
    dealData() {
      this.olist.forEach((item,index) => {
         this.$set(this.olist,index, Object.assign({isShow: false}, item))
      })
    }
  }
})
```
[demo6 地址](http://jsbin.com/cisetupuxa/edit?html,js,output)

#### 方案三：$forceUpdate()
Vue 在 2.0 的版本中新增了 \$forceUpdate() 方法，迫使 Vue 实例重新渲染。类似于 Angular 中的 $scope.$apply()。看一下 demo7：
```
new Vue({
  el: '#app7',
  data: {
    olist: [{startDate: 'click me'}, {startDate: 'click me'}]
  },
  computed: {
    list() {
      const list = []
      this.olist.forEach(item => {
        item.isShow = false
        list.push(item)
      })
      return list
    }
  },
  methods: {
    toggle(item){
      item.isShow = !item.isShow
      this.$forceUpdate() // 强制刷新，类似于 Angular 的 $scope.$apply() 方法
    }
  }
})
```
[demo7 地址](http://jsbin.com/lofuheheho/edit?html,js,output)

### 总结
以上就是这个问题的解决方案，我给所有的 demo 做了一个合集[地址](http://jsbin.com/kigizowiju/7/edit?html,js,output)。其中 $forceUpdate() 是同事分享的新方法，如果还有别的解决方案，记得留言告诉我。