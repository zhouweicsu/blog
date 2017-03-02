---
layout: post
category : JavaScript
title: 「译」2015 年度 JavaScript 开发者调查结果
tagline: "JavaScript Developer Survey Results"
tags : [JavaScript, 调查]
---

年关将至，JavaScript 开发者问卷调查也已经结束了。本次调查收到了超出我想象的回复，我也很高兴与你们分享调查结果。

截至目前，有**超过 5000 人**参与了问卷调查，准确的说是 5350 人，我真的迫不及待想与大家分享详细这次调查的细节。分享之前先感谢参与调查的每一个人。这是一个 JavaScript 社区的伟大时刻，我真的对未来的事情感到无比激动。

<!-- more -->

我没有想到大家这么积极，下次我一定会对表单做一些改进。下次我会先将调查问卷放在 GitHub 上，这样在调查开始前，社区有一两个星期的时间来收集问题和选项。这样，我就可以得到更准确地结果，也可以避免出现“你竟然没有包含Emacs！”这样的抱怨。

现在，基于调查结果。我将保持中立的给大家分析结果，这样大家就可以得出自己的无偏见结论。

## 1、你写什么类型的 JavaScript？

有高达 97.4% 的受访者用 JavaScript 写 web 浏览器端程序，其中有 37% 写移动端 Web 应用。超过 3000 人（56.6%）也使用 JavaScript 写服务端。在这些参与调查的人中间，有 5.5% 的人还在一些嵌入式环境中使用 JavaScript，例如 Tessel 或 Raspberry Pi（树莓派）。

少数受访者反馈他们还在其他地方使用了 JavaScript，主要有 CLI 和桌面应用的开发。还有少数提到了 Pebble 和 Apple TV。这些都归类在**其他**选项中，占总票数的 2.2%。


![An screenshot of the percentages for the first question](https://i.imgur.com/c0q4LvI.png)


## 2、你在哪使用 JavaScript？

不出所料，94.9% 的投票者在工作中使用 JavaScript。但是，也有 82.3% 的投票者在其他项目中使用。其他回复包括教学，好玩，和非盈利目的使用。


![An screenshot of the percentages for the second question](https://i.imgur.com/K5nSsyr.png)

## 3、你写 JavaScript 多长时间了？

超过 33% 的受访者有超过 6 年 JavaScript 代码的编程经验。除了这些，还有 5.2% 的人 1 年前开始写JavaScript，12.4% 的人是 2 年前，15.1% 的人是 3 年前。这说明在 5350 个投票者中有 32.7% 的人是近几年才开始写 JavaScript 的。

![An screenshot of the percentages for the third question](https://i.imgur.com/P5ev9fL.png)

## 4、如果可以的话，你会使用以下哪种 compile-to-JavaScript 语言？

有高达 **85%** 的受访者表示他们将使用 ES6 编译成 ES5。同时依然有 15% 的人用 CoffeeScript，15.2% 的人用 TypeScript，只有区区 1.1% 的人在使用 Dart。

这是我想进一步探讨的问题之一，因为有 13% 的人选择了*“其他”*。选择其他的绝大部分答案是 ClojureScript，eml，Flow，和 JSX。

![An screenshot of the percentages for the fourth question](https://i.imgur.com/12mL6u6.png)

## 5、你会选择以下哪种 JavaScript 编程风格？

绝大多数（79.9%）的 JavaScript 开发者在这个问题上选择分号。相反，11% 的开发者选择不使用分号。逗号方面，44.9% 的开发者选择将逗号放在表达式末尾，然而有 4.9% 的开发者喜欢先写逗号。缩进方面，65.5% 的开发者喜欢空格，另外 29.1% 的则更喜欢制表符。


![An screenshot of the percentages for the fifth question](https://i.imgur.com/xwFVmS1.png)

## 6、你使用以下哪些 ES5 特性？

79.2% 的受访者都使用过数组功能函数，76.3% 的开发者使用严格模式，30% 的开发者选择了 Object.creat，而仅有 28% 的开发者选择 getters 和 setters。


![An screenshot of the percentages for the sixth question](https://i.imgur.com/W9pUOua.png)

## 7、你使用以下哪些 ES6 特性？

值得指出的是，投票表明箭头函数是使用最多的 ES6 特性，有 79.6%。Let 和 const 加一起一起有77.8% 的得票率，promises 也有 74.4% 的开发者选择。不出所料，仅有 4% 的参与者使用过 proxies。仅有 13.1% 的用户表示他们使用 symbols，同时有超过 30% 的人使用 iterators。

![An screenshot of the percentages for the seventh question](https://i.imgur.com/okcvuos.png)

## 8、你写测试吗？

有 21.7% 的开发者从不写任何测试，大部分人偶尔写测试，34.8% 的人经常写测试。


![An screenshot of the percentages for the eighth question](https://i.imgur.com/0C944YL.png)

## 9、你运行持续集成测试吗？

与 CI 类似，许多人不使用 CI 服务器（超过40%）。但 60% 的受访者有时候会使用 CI，有 32% 的人在 CI 服务器上跑测试。


![An screenshot of the percentages for the ninth question](https://i.imgur.com/P04bJHG.png)

## 10、你怎么测试？

59% 的人喜欢使用 PhantomJS 或类似工具运行自动化浏览器测试，51.3% 的人还在 Web 浏览器上手动测试。有 53.6% 的人会在服务端进行自动化测试。


![An screenshot of the percentages for the tenth question](https://i.imgur.com/v09gVdQ.png)

## 11、你使用过以下哪个单元测试库？

似乎大部分投票者选择的不是 Mocha 就是 Jasmine 来运行 JavaScript 测试用例，Tape 有 9.8% 的投票。


![An screenshot of the percentages for the eleventh question](https://i.imgur.com/20nUzJu.png)

## 12、你使用过以下哪个代码质量检测工具？

似乎受访者在这个问题上被分为 ESLint 派和 JSHint 派，但是 JSLint 还有几乎 30% 的人支持，这么多年依然坚挺很是令人惊奇。


![An screenshot of the percentages for the 12th question](https://i.imgur.com/RC8ePwr.png)

## 13、你是如何处理客户端依赖问题？

npm 接管了客户端依赖管理系统的天下，60% 的支持率就是证据。Bower 依然有 20% 的观众，而普通的老式 &lt;script&gt; 下载和插入则获得了 13.7% 的选票。

![An screenshot of the percentages for the 13th question](https://i.imgur.com/TOQiSZP.png)

## 14、你首选的脚本构建方案是什么？

构建工具的选择很分散，部分原因应该是我们有太多选择。Gulp 最流行，有超过 40% 的选票。紧接着是 27.8% 的 npm run，Grunt 有 18.5% 的支持者。



![An screenshot of the percentages for the 14th question](https://i.imgur.com/xXlEE3E.png)


## 15、你首选的 JavaScript 模块加载工具是什么？

目前，似乎大部分开发者都在 Browserify 和 Webpack 之间徘徊，而后者高出 7 个百分点。29% 的用户表示他们在选择前面两个提到的工具打包模块之前，会先使用 Babel。



![An screenshot of the percentages for the 15th question](https://i.imgur.com/pQPMC7V.png)


## 16、你使用以下哪些库？

现在回想起来，这是个得益于协同编辑的问题之一。jQuery 得到了超过 50% 的支持，表现依然强势。Lodash 与 Underscore 在参与投票的 JavaScript 使用者中也占据显著地位，XHR 微型库仅获得了 8% 的票数。



![An screenshot of the percentages for the 16th question](https://i.imgur.com/7jAwy05.png)


## 17、你使用以下哪些框架？

React 和 Angular 的遥遥领先并不令人奇怪。有 22.8% 得票的 Backbone 依然处于安全位置。



![An screenshot of the percentages for the 17th question](https://i.imgur.com/zpSAISK.png)

## 18、你使用 ES6 吗。。。

这个问题的答案比较分散，有近 20% 的人几乎从不使用 ES6，超过 10% 的人只写 ES6，近 30% 的人广泛使用，近 40% 的人经常使用。



![An screenshot of the percentages for the 18th question](https://i.imgur.com/hAnbtfN.png)

## 19、你知道 ES2016 会有什么特性吗？

大致说来，有一半的投票者不知道 ES2016 将会有什么特性，另外一半对新特性有所了解。


![An screenshot of the percentages for the 19th question](https://i.imgur.com/DxxOnco.png)


## 20、你了解 ES6 吗？

超过 60% 的受访者了解基本的概念，10% 的人完全不了解 ES6，有超过 25% 的人认为他们对 ES6 非常了解。

![An screenshot of the percentages for the 20th question](https://i.imgur.com/w6obK3X.png)

## 21、你认为 ES6 是一个进步吗？

几乎有 95% 的受访者认为 ES6 是 JavaScript 的一个进步。下次碰到 TC39 的会员我得祝贺他们！


![An screenshot of the percentages for the 21th question](https://i.imgur.com/c0RtfVK.png)


## 22、你首选的编辑器是什么？

又一次，因为选择太多导致答案很分散。超过一半的受访者喜欢 Sublime Text，超过 30% 的人喜欢用 Atom 和它的开源克隆版。超过 25% 的票投给了 WebStorm，vi/vim 也有超过 25% 的得票率。


![An screenshot of the percentages for the 22th question](https://i.imgur.com/Vt8ve7s.png)



## 23、你首选的开发操作系统是什么？

 Mac 有超过 60% 的投票者使用，Linux 和 Windows 的用户都接近 20%。



![An screenshot of the percentages for the 23th question](https://i.imgur.com/PmLbtAo.png)

## 24、你如何搜索可重用代码，库和工具？

受访者最喜欢 Github 和搜索引擎，但通过博客，Twitter 和 npm 网站搜索也占了很大一部分。



![An screenshot of the percentages for the 24th question](https://i.imgur.com/HpmV9yz.png)

## 25、你参加 JavaScript 社会活动吗？

有近 60% 的人至少参加过一次会议，74% 的人表示他们喜欢参加聚会。



![An screenshot of the percentages for the 25th question](https://i.imgur.com/EnQWGzf.png)

## 26、你的 JavaScript 应用支持哪些浏览器？

相当分散的答案，但还好大部分受访者不再考虑 IE6 的使用者了。


![An screenshot of the percentages for the 26th question](https://i.imgur.com/BV3eU0X.png)


## 27、你会定期了解 JavaScript 的最新特性吗？

有大概 80% 的受访者会实时了解最新的 JavaScript 特性。


![An screenshot of the percentages for the 27th question](https://i.imgur.com/5TZUW2i.png)


## 28、你在哪了解最新的 JavaScript 特性？

不出所料，[Mozilla 开发者网络](https://developer.mozilla.org)在文档和新闻方面处于领先地位。[JavaScript 周刊](http://javascriptweekly.com/)也是一个非常受欢迎的新闻和文章的直接来源，有超过 40% 的支持率。



![An screenshot of the percentages for the 28th question](https://i.imgur.com/7Jlg7zh.png)

## 29、你听说过以下哪些特性？

超过 85% 的投票者听说过 ServiceWorker，但我很想知道这些人有没有使用它！


![An screenshot of the percentages for the 29th question](https://i.imgur.com/8o3Jq2R.png)


## 30、除了 JavaScript，你还主要使用哪些语言？

有太多的语言可供选择，我肯定会漏那么几个，但结果不言自明。


![An screenshot of the percentages for the 30th question](https://i.imgur.com/Tv9NciV.png)

谢谢！

最后，感谢每一位参与这次调查的人。本次问卷调查受到了超出我预期的欢迎，期待明年再进行一次类似的调查。我希望，那会是一个更多样性，也许再少一点偏见的调查。

> 这次问卷调查你获得了什么？



作者：[Nicolás Bevacqua](https://ponyfoo.com/)，时间：2015-12-11

[原文链接: https://ponyfoo.com/articles/javascript-developer-survey-results](https://ponyfoo.com/articles/javascript-developer-survey-results)

译者：[zhouweicsu](http://zhouweicsu.github.io/)
