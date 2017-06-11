---
layout: post 
category : 翻译
date: 2017-02-13
title: 「译」可扩展 Web 宣言
tagline: "The Extensible Web Manifesto" 
tags: [翻译, Web] 
---

#### 推动 Web 发展

我们——已签署这份协议的人——想要改变 Web 标准委员会新增新特性以及给他们排优先级的方式。我们认为这对 Web 的长期健康发展来说至关重要。

我们的目标是缩小 Web 标准与 Web 开发者之间的反馈环路。


<!--more -->

目前，许多新特性需要经历几个月甚至几年的标准化流程，接着又是浏览器厂商的精确实现，然后才是开发者的反馈与迭代。我们更希望先进行 JavaScript 新特性的开发与迭代，然后再是浏览器厂商的实现和标准化过程。

为了让函数库能做更多的事情，浏览器厂商应该提供新的底层原语，尽可能多的暴露访问平台底层的接口。

厂商还应该通过新特性的 JavaScript 实现，来促成开发者对高级 API 的讨论（例如 [Mozilla’s X-Tags](http://www.x-tags.org/) 和 [Google’s Polymer](http://www.polymer-project.org/)）。

* * *

具体而言，我们为可扩展 Web 平台提议以下设计准则：

* 专注于向 Web 平台新增安全高效的**_底层原语_**。
* 暴露底层原语**_解释现有特性_**，例如 HTML 和 CSS，使开发者能理解并复用他们。
* 在 JavaScript 新的高级特性成为标准之前，让 Web 开发者参与其开发，设计和测试的迭代过程。这会在标准和开发者之间形成**_良性循环_**。
* **_优先处理_**符合这些建议的工作，降低不符合这些建议的优先级或重新评估他们。

* * *

专注于标准化**_新的底层原语_**，并基于这些原语来扩展新特性，我们倡导：

* 考虑新的安全领域。
* 浏览器引擎的优化工作应集中在稳定内核，因为内核会影响许多新增的 API。这样做会减少实现成本并提升性能。
* 浏览器厂商和函数库的开发者应共同参与函数库的迭代开发，以提供浏览器友好的高级 API。

* * *

根据**_底层原语_**解释已存在和未来的特性，我们倡导：

*  减缓实现过程中复杂度的增长率，这样 bug 数的增长也会放缓。
*  尽可能给平台的新特性实现 polyfill。
*  新特性应该让开发者付出尽可能少的学习成本。学习资料应该由平台中现有的概念组成。


* * *

让新特性易于理解，易于 polyfill 会形成**_良性循环_**，因为：

* 开发者可以快速了解新的 API，让平台在 API 最具扩展性的时候获得更快的反馈。
* 在开发者使用和函数库作者提供服务的过程中，API 中错误能被快速修正，这给浏览器厂商和平台设计者提供了高保真和很关键的反馈。
* 函数库的作者可以尝试新的 API，为平台发展提供更多的道路。

* * *

**_优先工作_**应该遵循那些准则，我们倡导：

* 放手标准制作的过程（尤其是短期内），致力于有关安全或性能问题的特性，以及仅能在平台级别新增的特性，例如新的硬件。
* 让 Web 开发者和初始化浏览器的函数库去引领代价高昂的探索。
* 那些已经有实现，且在实际项目中已大量使用过的 API ，我们需要简化其冗长的标准化过程。

* * *

我们希望 Web 开发者能写更多声明式代码。这就需要引入新的声明式形式来打破现有标准的限制，并为函数库和框架的作者提供创建他们的工具。

为了让开放式 Web 能够竞争过其铜墙铁壁般的对手，我们需要提供一条清晰的路径，让 Web 开发者能够将他们的好想法变成 Web 基础的一部分。必须让 Web 开发者去构建未来的 Web 世界。

---

如果你支持这些原则，

[请点击这里签名](https://docs.google.com/forms/d/1fqaqAjUZR8uDo5l9p1_80-1kt0SFQNJ_fCjiZPNgj4Y/viewform)

[已签名名单](https://docs.google.com/spreadsheet/pub?key=0AtR1SNsaFeH9dGI5eWZQVGNhZWEyQUhtaUw3ZWl1dVE&single=true&gid=0&output=html)


*   **Brendan Eich**

    Mozilla CTO 和 高级副总裁; JavaScript 创造者

*   **Yehuda Katz**

    TC39, W3C TAG 会员； Ember.js, Rails 核心团队成员

*   **Alex Russell**

    TC39, W3C TAG 会员；Google Chrome 团队成员

*   **Brian Kardell**

    W3C 可扩展的 Web 社区小组主席

*   **François REMY**

    W3C 可扩展的 Web 社区小组联合创始人

*   **Clint Hill**

    W3C 可扩展的 Web 社区小组联合创始人

*   **Marcos Caceres**

    W3C 可扩展的 Web 社区小组联合创始人

*   **Tom Dale**

    Ember.js  核心团队成员

*   **Anne van Kesteren**

    标准编辑，Mozilla 架构师

*   **Sam Tobin-Hochstadt**

    TC39 会员

*   **Domenic Denicola**

    Promises/A+ 标准的联合作者

*   **Chris Eppstein**

    Sass 维护者

*   **Dave Herman**

    Mozilla 研究员，TC39

*   **Alan Stearns**

    CSSWG 会员

*   **Rick Waldron**

    TC39 会员；jQuery, Bocoup 核心团队成员

*   **Paul Irish**

    Google

*   **Ted Han**

    DocumentCloud 开发负责人

*   **Tab Atkins**

    CSSWG 会员； Google

*   **Dan Appelquist**

    W3C TAG 联合主席， Telefónica 开放 Web 倡导者

*   **Isaac Schlueter**

    Node.js 维护者

*   **Allen Wirfs-Brock**

    Mozilla 研究员， TC39 会员， ECMAScript 规范编辑

---

**相关阅读**

*   [Extend the Web Forward](http://yehudakatz.com/2013/05/21/extend-the-web-forward/)，作者 Yehuda Katz，更详细地解释了宣言中提到的理念。
*   [An Extensible Approach to Browser Security Policy](http://yehudakatz.com/2013/05/24/an-extensible-approach-to-browser-security-policy/)，作者 Yehuda Katz，该文为宣言中关于浏览器内容安全策略的 API 设计问题提供了一个实际可行的方案。 
*   [Dropping the F-Bomb on Web Standards](https://briankardell.wordpress.com/2013/05/17/dropping-the-f-bomb/)，作者 Brian Kardell，一份关于开发者社区的想法和语言如何被纳入可交互的标准中的务实讨论。 
*   [Bedrock](http://infrequently.org/2012/04/bedrock/)，作者 Alex Russell，一篇 2012 年的文章，深入探讨以可扩展（“分层”）方式设计平台的理论与实践。 
*   [How the Web Should Work](http://smus.com/how-the-web-should-work/)，作者 Boris Smus，一篇 2012 年的文章，描述了“forward polyfills”（或者 prollyfills）应该如何实际应用。
                