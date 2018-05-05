---
layout: post 
category : JavaScript
title: 「译」Facebook Prepack -- JavaScript 的下一个杀手级工具
date: 2017-07-13 19:55:59
tags: [JavaScript, Facebook, Prepack, compile] 
---

> 译者：[zhouweicsu](http://www.zcfy.cc/@zhouweicsu)
> 原文：[https://hackernoon.com/facebooks-prepack-the-next-killer-in-the-javascript-zone-d932556ffd8c](https://hackernoon.com/facebooks-prepack-the-next-killer-in-the-javascript-zone-d932556ffd8c)


这几天社交网络上都在热火朝天地讨论 **_Prepack_**。如果你没听过这个名词也很正常！因为这个工具才**开源**没几天。Prepack 由 Facebook 出品，还在活跃的开发阶段。目前还属于实验性质，仍处于早期阶段，但它简直棒极了 😱。

本文会给大家介绍 Prepack，包括 Facebook 借此想要解决的问题，还会列举几个超赞的示例，最后回介绍 Prepack 目前的发展现状。

<!-- more -->

* * *

### Pre.. 什么？

> Prepack 是一个 JavaScript 源代码优化工具。

Prepack 能**消除**那些本可以在**编译阶段**完成的运行时计算。会将 JavaScript 代码包中的**全局代码替换为**一系列等价的赋值语句。这样做能省去很多中间计算和对象的分配工作。

> Prepack，FB 开发的 JavaScript 部分求值器和优化编译器，现在开源啦！https://t.co/IDrNfHhpJL
—— [@sebmck](https://twitter.com/sebmck/status/859821183470112769)

Sebastian McKenzie — Prepack 开发者之一

Prepack 是一个 JavaScript 的**部分求值器（partial evaluator）**。它会重写 JavaScript 包，让代码更高效地执行。对于重初始化的代码（initialization-heavy code），Prepack 在可以有效缓存 JavaScript 解析结果的环境中优化效果最佳。

#### 和 Closure Compiler 有什么区别呢？

> Prepack 更关注于优化后的运行时性能，而 Closure Compiler 侧重于减小 JavaScript 代码量。

Closure Compiler 也优化 JavaScript 代码。但 Prepack 更进一步，它会在初始化阶段就执行全局代码，展开循环和递归。

#### 一段小历史

一年以前，在 2016 React 欧洲会议上，Sebastian McKenzie 提到了如何更快初始化 JavaScript。

[Sebastian McKenzie - Making JavaScript Initialize Faster at react-europe 2016](https://www.youtube.com/embed/xbZzahWakGs)
McKenzie 在 2016 React 欧洲会议上的演讲

看到这里，大家应该对 Prepack 有了进一步的了解。了解了 Prepack 名字的含义及其网站，下面就看看如何使用它。

* * *

### 如何上手？

Prepack 通过 npm 包发布（译者注：Prepack 现在已经改为 yarn），你可以用 Prepack-CLI 工具，也可以调用 Node.js 模块的 API。

#### Prepack CLI

安装 Prepack-CLI，运行

```
npm install -g prepack
```

然后编译文件，运行

```
prepack script.js
```

#### Prepack API

在项目中安装，运行

```
npm install --save-dev prepack
```

js 代码如下：

通过 npm 包使用 Prepack api —— 代码示例（译者注：请点击[原文](https://hackernoon.com/facebooks-prepack-the-next-killer-in-the-javascript-zone-d932556ffd8c)查看此代码）

#### prepack-webpack-plugin

有一个 webpack 插件可以方便地使用 Prepack。

webpack 插件的使用示例（译者注：请点击[原文](https://hackernoon.com/facebooks-prepack-the-next-killer-in-the-javascript-zone-d932556ffd8c)查看使用示例）

* * *

### 好戏开场

下面是一些超赞的示例。示例中左侧是输入，右侧是 Prepack 优化后的输出。

第一个示例是惯例：hello world

![](http://p0.qhimg.com/t01b799c7f640f9bf31.png)

示例 1

如你所见，Prepack 将代码编译成了一个无法再小的版本。

下一个示例有两个函数。代码如下：

![](http://p0.qhimg.com/t019df8c70f49ad38cf.png)

示例 2

编译后的代码由第一个函数生成。`func2` 函数没有被调用，也没有包含任何副作用代码，所以编译后的代码和 `func2` 没有任何关系。

注意，如果代码不是 [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)，则编译后的代码不是完全最优。

![](http://p0.qhimg.com/t015399aa9a73ffff70.png)

不是 IIFE 的示例

这个示例展示了一段 for 循环代码。循环在编译阶段被移除，但是还生成许多不用的代码行。如果是 IIFE，则会是这样：

![](http://p0.qhimg.com/t012f5a25a511113519.png)

IIFE 示例

如你所见，最后只留下了 5 行 log 操作的代码。

#### Playground

你可以在官网中的 [playground](https://prepack.io/repl.html) 中试一试 Prepack。这个 playground 为那些不想在本地安装 Prepack，或者只是感兴趣想尝尝鲜的用户提供了一个选择。

![](http://p0.qhimg.com/t01b5bbf29a340c0b6b.png)

Playground 示例

* * *

### 发展现状

#### Github 指标

Prepack 代码托管在 Github（比❤️），通过 npm 发布。

![](http://p0.qhimg.com/t014d4672afe5def3d7.png)

Github

目前有 15 个 contributor，大概 6500 个 star，本周 npm 上有约 2000 个 download，约 130 个 fork。由于社交网络上开发者对 Prepack 很感兴趣，预计这些统计数据在未来一段时间有望**大幅增长**。

有个很有趣的 issue，你一定要了解下……

>[**Converts 1 meaningless line to 1065 lines of code · Issue #543 · facebook/prepack** _Looks like @bevacqua managed to break it. Try running this modified example from the docs (function() { function fib…_github.com](https://github.com/facebook/prepack/issues/543 "https://github.com/facebook/prepack/issues/543")[](https://github.com/facebook/prepack/issues/543)

但这个无所谓。他们会马上修复这个 bug 的。

#### 测试覆盖率

序列化测试的[代码测试覆盖率](https://446-45147841-gh.circle-artifacts.com/0/tmp/circle-artifacts.hh6xQWZ/coverage-report-sourcemapped/index.html)报告所有人都可以查看。目前的覆盖率是 52.37%。

![](http://p0.qhimg.com/t01d4be8b69a889cb9c.png)

代码测试覆盖率

Prepack 的代码测试覆盖率由 [istanbul](http://istanbul-js.org/) 生成。

#### 未来规划 🎯

Facebook 有一个 Prepack 的[未来规划](https://prepack.io/)。规划分为三个部分：短期，中期和长期。

短期内，Facebook 希望能稳定现有功能集，用于预打包 React Native 代码包。他们也想集成 React Native 工具链。另外，他们还计划基于 React Native 目前使用的模块系统的假设来构建优化。

长期愿景是**利用 Prepack 打造一个平台**。

* * *

### 最后注意

Prepack 不能识别 `document` 和 `window`，所以他们的值会是 `undefined`。因此，我们必须做一点额外的准备工作来解决这个 issue。也许有人愿意自奋告勇写一个 npm package 来解决这个问题？😇

Facebook 号召开发者去使用 Prepack 并给出反馈，修复存在的 bug，在 Github 仓库中提交代码，来帮助他们更好的完善 Prepack。

### 参考文献

以下是一些有用的扩展阅读链接：

*   [Github repository](https://github.com/facebook/prepack)
*   [npm package](https://www.npmjs.com/package/prepack)
*   [Official website](https://prepack.io/)
*   Prepack [playground](https://prepack.io/repl.html)

* * *

### 结论

Prepack 会成为一个“生活变革者(life changer)”。它背后有一个稳定公司 —— Facebook。他们想将这个工具集成到 React 中，并且已经为此工作了一段时间。但未来仍有很长的路要走。Facebook 的愿景是**利用 Prepack 打造一个平台**。这看起来很有希望。我会密切关注它的进展。Prepack 简直棒极了！




