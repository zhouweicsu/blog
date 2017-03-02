---
layout: post 
category : JavaScript
title: 图解 JavaScript 中的 __proto__ 与 prototype
date: 2017-03-02 16:22:13
tags: [JavaScript, 面试] 
---

正真理解 JavaScript 中的原型模式是掌握 JavaScript 基础的关键。原型模式是 JavaScript 实现面向对象和继承的基础。而原型中的 \__proto__ 与 prototype 又是理解原型模式的关键。

本文用代码结合图片的方式梳理一下 ____proto____ 和 **prototype** 之间的区别和关系。

<!-- more -->

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain) 中写到：
>  当谈到继承时，JavaScript 只有一种结构：对象。每个对象都有一个内部链接到另一个对象，称为它的**原型 prototype**。该原型对象有自己的原型，等等，直到达到一个以 null 为原型的对象。根据定义，null 没有原型，并且作为这个原型链 prototype chain 中的最终链接。

《JavaScript 高级程序设计》第 6 章 6.2.3 原型模式中写到：
> 当调用构造函数创建一个新实例后，该实例的内部将包含一个指针（内部属性），指向构造函数的原型对象。ECMA-262 第 5 版中称这个指针叫 **[[Prototype]]**。

大多数浏览器得 ES5 实现中都支持通过属性 ____proto____ 来访问 [[Prototype]] 指针。


先看一段代码：
```javascript
class Point{
  constructor(x,y)  {
    this.x = x;
    this.y = y;
  }
}

class ColorPoint extends Point {
  constructor(x,y,color)  {
    super(x, y);
    this.color = color;
  }
}

var p1 = new Point(2,3);
var p2 = new ColorPoint(3,4, 'green');

console.log(p1.__proto__ === Point.prototype);                   //true
console.log(p1.__proto__.__proto__ === Object.prototype);        //true
console.log(p2.__proto__ === ColorPoint.prototype);              //true
console.log(p2.__proto__.__proto__ === Point.prototype);         //true
console.log(Point.__proto__ === Function.prototype);             //true
console.log(ColorPoint.prototype.__proto__ === Point.prototype); //true
console.log(ColorPoint.__proto__ == Point);                      //true
console.log(Function.prototype === Function.__proto__);          //true
console.log(Function.prototype.__proto__ === Object.prototype);  //true
console.log(Object.prototype.__proto__)                          //null
console.log(Object.prototype.prototype)                          //undefined
console.log(p1.prototype)                                        //undefined
```

Class 可看作为构造函数的语法糖。

根据这段代码我们可以绘制出一张关系图：

![__proto__&prototype 图解](proto-prototype.png)

从图中可以看到实例、函数和原型对象之间的关系，得到以下结论：

* 实例、构造函数和原型对象都有 \__proto__ 属性（因为 JavaScript 中一切皆对象），指向该对象的构造函数的原型对象；
* 构造函数不仅有 \__proto__ 属性，还有 prototype 属性（函数独有属性），prototype 指向该方法的原型对象（**Note**：通过 Function.prototype.bind 方法构造出来的函数是个例外，它没有 prototype 属性）；
* 原型链最终指向一个以 null 为原型的对象。null 没有原型，并且作为这个原型链 prototype chain 中的最终链接；
* Function 的 prototype 和 \__proto__ 都指向 Function.prototype；



参考文章：
[js 中 \__proto__ 和 prototype 的区别和关系？](https://www.zhihu.com/question/34183746)

