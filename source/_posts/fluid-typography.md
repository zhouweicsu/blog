---
layout: post 
category : CSS 
title: 使用 vh 和 vw 实现正真的流式文字排版 
tagline: "Truly Fluid Typography With vh And vw Units" 
tags : [CSS, Typography, fluid] 
date: 2016-06-29 
---

原文：[Truly Fluid Typography With vh And vw Units](https://www.smashingmagazine.com/2016/05/fluid-typography/)

*   作者： [Michael Riethmuller](https://www.smashingmagazine.com/author/michaelriethmuller/)

*   2016-05-10

实现流式文字排版或许比你想象的更简单。他具有广泛的浏览器支持，实现简单，而且实现之后并不会让你失去对设计的整体把控。

不像响应式文字排版，文字只在指定的几个分界点才会调整，**流式文字排版则能平滑地调整文字**来适应任何宽度的设备。当网站需要支持几乎无限个屏幕尺寸的时候，流式排版就是不二之选。然而，由于一些原因，他的普及率却仍远不及响应式技术。

也许是因为文字排版受印刷排版的古老历史影响太深。“流式”这个概念常常有违这些传统。在印刷业，尺寸通常是固定的，而 web 上却没有固定。这就是为什么我认为流式文字排版是 web 的完美搭档。他是截然不同的媒介的另一种处理方式。

但这并不意味着我们需要抛弃所有惯例和关于文字排版的一切。我们只需以稍微不同的方式**学习如何应用一些我们已知的技术**。谨慎对待每一个细节将确保我们在所有屏幕尺寸中都获得完美的设计体验。

### 流式文字排版快速入门

视口单位是 web 上能应用流式文字排版的重要元素。视口单位指的是浏览器视口尺寸的百分比。例如，1 `vw` 等于视口宽度的 1%。与单位百分比不同，他们总是相对于视口，而百分比是相对于元素的父容器。

这意味着，与所有其他单位类型不同，视口单位与基础的字体大小没有任何关系。这个区别意义重大，也让这个单位变得有趣而独特。


可用的四个视口单位：
*   `vw`: 视口宽度
*   `vh`: 视口高度
*   `vmin`: 视口宽度和高度的较小值
*   `vmax`: 视口宽度和高度的较大值

应用流式字体排版技术最简单的方法就是给 `html` 元素上的 `font-size` 设置一个流式单位：

```
html { font-size: 2vw; }
```

在这个例子中，我们给根元素设置了 `2vw`。因此，我们改变了 “root em”。因为所有使用了 `em` 和 `rem` 单位的元素都直接或间接的与这个 root em 有关，所以他们也将变成流式的。例如：

```
h1 { font-size: 2em; }
```

`2em` 的标题大小现在等于 `4vw`，因为这是当前基础字体大小 `2vw` 的两倍。

单独使用视口相关的单位也会带来一些弊端。例如我们无法精确控制缩放速率；无法设置最小或最大的字体大小；还有，与像素一样，一行声明也许会覆盖用户的 font-size 偏好设置。但幸运的是，总有方法来克服这些缺陷。

### 控制视口单位来设置最小与最大字体大小

给 `html` 元素设置 `2vw` 让所有元素都流动起来然后收工回家，这么做当然简单，但这达不到最佳效果。视口单位是一个钝器，要得到可行结果需要一些外力辅助。

因为视口单位通常与视口有着直接的关系，所以在很小的屏幕下，你会看见小的离谱的字。

理论上，我们应该设置一个最小的字体大小，可惜 CSS 中并没有 `min-font-size` 这个属性。但换个角度思考一下，我们采用一些不同的方法也可以达到同样的效果。

第一，我们可以使用 `calc()` 表达式：
```
html { font-size: calc(1em + 1vw); }
```

在这个例子中，如果视口宽度为 0，那 `font-size` 就是 `1em`。当屏幕变大，`1vw` 的值将会与最小字体大小 `1em` 相加。但这个技巧并不是完美的；通常我们希望能在某个屏幕大小时设置一个最小的字体大小，而不是让他等于 0。使用媒体查询可以解决这个问题：

```
@media screen and (min-width: 50em) {
    html {
        font-size: 2vw;
    }
}
```

在这个例子中，当视口宽度达到 50 em 的时候，字体大小就会变成动态的。这个方案效果很好，但在固定值和动态值之间通常会有一个抖动。为了消除这个抖动，我们要算出动态值与固定值刚好相等的那个视口宽度，然后在这个地方设置分界点。

如果默认的字体大小是 16 像素，`2vw` 是视口宽度的 2%，那么计算分界点的计算式就是 `16 ÷ (2 ÷ 100)`。结果是 800 像素。
> 2% * y = 16
> y = 16 ÷ (2 ÷ 100) 
> ——译者注

如果想在媒体查询中使用 em 单位，那就需要将 px 转换为 em。用 800 除以 16（或其他等于 rem 的像素值）： `800 ÷ 16 = 50`。如果你觉得这更简单，也可以直接使用 em 作为单位来计算：`1 ÷ (2 ÷ 100) = 50`。如上例所示，设置一个 `2vw` 的字体大小和 `50em` 的分界点，可以使固定值与动态值无缝衔接。

同样的计算式也可计算出字体大小的最大值。如果想让字体大小最大不超过 24px，我们应该这样计算：`24 ÷ (2 ÷ 100) = 1200px`。使用 em 计算式就是：`1.5 ÷ (2 ÷ 100) = 75`（1.5 = 24 ÷ 16——译者注）。因此，超过 75em 的宽度后，字体大小就是固定值 1.5em。

```
@media screen and (min-width: 75em) {
    html {
        font-size: 1.5em;
    }
}
```

这些计算式都不难，表格有助于直观的查看不同视口单位的分界点和字体缩放比例。表格头部是视口单位的值，表格左侧是设备解决方案。

|- | 1vw | 2vw | 3vw | 4vw | 5vw |
| ------   | :-----:  | :----:  | :----:  | :----:  | :----:  |
| **400px** | 4px | 8px | 12px | 16px | 20px |
| **500px** | 5px | 10px | 15px | 20px | 25px |
| **600px** | 6px | 12px | 18px | 24px | 30px |
| **700px** | 7px | 14px | 21px | 28px | 35px |
| **800px** | 8px | 16px | 24px | 32px | 40px |

通过表格可以看出，我们几乎无法控制视口单位大小改变时字体大小的改变速率。单独使用视口单位，字体大小就完全受限于表格的某一单列。

### 控制缩放速率

如果想让字体大小在屏幕分辨率 400px 时为 16px，分辨率达到 800px 时调整到 24px，使用分界点实现不了这个需求。上面我们为最小和最大字体大小计算过分界点，不是选择他们。

那我们如何解决这个问题呢？答案就是[使用 calc()](https://www.smashingmagazine.com/2015/12/getting-started-css-calc-techniques/)。将 `calc()` 和视口单位结合使用可以得到进阶版流式文字排版，字体会在指定的视口范围内的指定像素值之间完美缩放。我们只需简单的创造一个基本的数学公式。

![Image of a function: calc(16px + (24 - 16) * (100vw - 400px) / (800 - 400))](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/advanced-calc-500-opt.png)

[点击查看大图片](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/advanced-calc-800-opt.png)

这个函数取指定范围内的一个值，然后计算出应用在不同范围的新值。所以，如果取数字 1 和 100 还有值 50，那么应用到 1 和 200 之间时，新值就是 100。而且这些值在范围中任何点都是正确的。

这是个很普通的映射函数，在 JavaScript 处理数据时已经使用过很多次。我很好奇将函数与 `calc()` 一起使用的可能性，当发现这就是纯数学函数时，我知道就缺一个变量了。这是使用视口的关键区别：视口大小就是变量。`100vw` 的值就是这个方程式的变量，因为当视口变化时 `100vw` 的值也是变化的。有了这个初始想法，我花了一段时间使得这个函数工作。`calc()` 函数以特定方式处理方程式中的单位类型。如果你对这类事情感兴趣，我极力推荐阅读[ W3C 规范：单位的类型与值](https://www.w3.org/TR/css3-values/)；如果不出意外，掌握这个你就可以去显摆了。

尽管这个计算式看起来有点复杂，但其实相当简单。我们选定最小字体大小和最大字体大小，最小屏幕尺寸和最大的屏幕尺寸，不是选定字体应该缩放的大小，然后将选定的值代入方程中。我们可以使用任何类型的单位，包括 em，rem 或 px。在文章的例子中我喜欢使用 px 这个单位，因为 px 让概念更容易理解，但在实际工作中我更喜欢使用 rem。但无论你选择哪个单位，都要保证方程式中所有的值都使用相同类型的单位，你必须剥离单位的影响，如例子所示。

如果你不想手写，网上有很多工具，比如 [Sass](http://www.sassmeister.com/gist/7f22e44ace49b5124eec) 和 [LESS](http://codepen.io/MadeByMike/pen/RWJyML) 的 mixins，还有 [PostCSS](https://www.npmjs.com/package/postcss-responsive-type) 的插件。你想怎么玩，就怎么玩。

### 维持完美行宽

Robert 在他的书《印刷风格要素》中建议，一个令人感觉舒适的行宽大概是 45 到 75 个字符。

> 对于在正文中使用衬线字体（译者注：英语中的一种格式）的单栏页面来说，45 到 75 个字符被广泛认为是令人满意的行宽。66 个字符（包括字符与空格）则被公认为完美行宽。

这个规则可以直接复用到流式文字排版中，另外在许多情况下，随着文字的缩放实现一致的行宽也是有可能的。

在响应式方案中，我们通过设置分界点来调整字体大小，还会经常任意调整容器宽度来维持正确的行宽。然而，在流式文字排版中，指定分界点处的文字调整已经没有必要了。现在只需要设置容器宽度随着字体大小的变化速率而缩放就行。可以在 `width` 属性中使用 `calc()` 技术，与 `font-size` 的设置一样简单。这样就能轻松维持完美行宽——同时我也注意到样式表更容易阅读和维护了。

保持完美行宽在非常小的屏幕下是不切实际的。为了维护“完美的”阅读宽度而缩小字体，当缩小到某个点后总会损害可读性。我们只能接受这个事实，然后针对移动设备为某些关键内容的容器设置宽度。

### 实现一个模块化比例尺

模块化比例尺就是一系列相互之间比例协调的数字。一图胜千言：

[![Image of the senctence What is modular scale repeated six times but in different sizes](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/what-is-ms-500-opt.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/what-is-ms-800-opt.png)

每个标题都比前一个标题的缩小了 1.2 倍（译者注：原文写的是 bigger，应该是错误的）。（[点击查看大图](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/what-is-ms-800-opt.png)）

不同的比例尺在不同尺寸的屏幕下效果更好。


[![Comparison of a modular scale on a smartphone in portrait and a desktop screen](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/different-scales-500-opt.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/different-scales-800-opt.png)


（[点击查看大图](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/different-scales-800-opt.png)）

在小屏幕中，标题大小应该更统一；因为大屏幕有更多的空间应付更多样的变化。在不同的模块化比例尺之间，可以使用上文提到的动态变化的文字排版技术。只需为小屏幕选择一个比例尺，为大屏幕选择另一个比例尺，然后为每一个级别的标题计算出最小字体大小与最大字体大小。

为了计算出模块化比例尺，我们先取基础的字体大小与选定比例相乘得到一个更大的数字。除以选定比例得到一个较小的数字。根据得到的数字再重复此步骤，就会得到缩放过程中每个必需的值。

#### 放大
*   1em × 1.125em =  1.125em

*   1.125em × 1.125em = 1.266em

*   1.266em × 1.125em = 1.424em

*   1.424em × 1.125em = 1.602em

#### 缩小

*   1em ÷ 1.125em = 0.889em

*   0.889em ÷ 1.125em = 0.790em

*   0.790em ÷ 1.125em = 0.702em

*   0.702em ÷ 1.125em = 0.624em

我们取 1.125 作为最小缩放比例，取 1.250 作为最大缩放比例 ，然后做重复计算。然后在不同级别的标题中使用比例尺中的不同比例。

*   最小缩放比例： 1.125

*   最大缩放比例：1.250


| 标题级别| 最小字体大小 | 最大字体大小 |
| -----  | ----  | ----  |
| h1 | 1.602em | 2.441em |
| h2 | 1.424em | 1.953em |
| h3 | 1.266em | 1.563em |
| h4 | 1.125em | 1.250em |

我在 Codepen 上实现了一个[流式模块化比例尺标题](http://codepen.io/MadeByMike/pen/VvwqvW)的例子。你还可以在 Tim Brown 的文章[《更有意义的文字排版》](http://alistapart.com/article/more-meaningful-typography)中阅读更多关于模块化比例尺的内容。

如果你在选择合适的比例尺时需要帮助，我推荐 Jeremy Church 的网站 [Type Scale](http://type-scale.com/) 和 Tim Brown 与 Scott Kellum 的网站 [Modular Scale](http://www.modularscale.com/)。

### 保持垂直节奏

垂直节奏就是让页面上的元素之间保持一致且成比例的间距。你可以在 Espen Brunborg 的文章[《CSS 基线：好的，坏的和丑的》](https://www.smashingmagazine.com/2012/12/css-baseline-the-good-the-bad-and-the-ugly/)中找到更多关于垂直节奏的信息。

在流式布局中要保持垂直节奏，我们需要将每个元素的垂直间距设置为基准值的某个比例值。响应式文字排版中令许多人感到挑战的是如何找到在小屏幕和大屏幕下都能工作的垂直节奏。通常情况下，可以为小屏幕与大屏幕设置不同的基准值，也可以使用不同的比例。

在流式文字排版中，基准值也可以像字体大小一样设置为动态的。其实，如果已经给根元素设置了一个动态值，那我们就可以直接使用单位 `em` 或者 `rem`，也可以使用 `calc()` 。

假设基准值是 `1.5rem`。那么元素 `body` 的 padding 值就是 `1.5rem`。

```
body {
    padding: 1.5rem;
}
```

同样的，我们将 line-height 和 margin 也设置为 1.5rem。

```
p {
    line-height: 1.5rem;
    margin-bottom: 1.5rem;
}
```

对于标题元素，我想设置一个不同的行高。然而，我又想让行高加上 margin 之后等于基准值的增量。这时 `calc()` 就派上用场了。

```
h1 {
    font-size: 2rem;
    line-height: 2rem;
    margin-top: calc((1.5rem - 2rem) + 1.5rem);
    margin-bottom: 1.5rem;
}
```

在这个例子中，标题元素的总高度加上 margin 值将会是 `3rem`，刚好等于基准值的两倍。

我在 Codepen 上使用 Sass 变量写了一个[例子](http://codepen.io/MadeByMike/pen/0dbc9b82769c3e2a0cbfc3192bcaabd3?editors%3D0100)，你可以设置不同的基准值来看看它是如何工作的。

未来，自定义 CSS 属性将会使得这个技巧更有趣。那时我们可以在 `calc()` 中使用 CSS 变量来改变媒体查询中的基线值。

### 如何处理限制条件

也许你想在有布局限制的网站上实现流式文字排版，又或许你不得不使用 WordPress 或 Bootstrap 中的预定义布局。在这些情况下，容器很有可能不是流式的，如果是，大概也不会与字体大小以同速率变化。

我最近为澳大利亚政府民政部门的网站实现流式文字排版。这是个充满挑战的大网站，我们还必须基于现有的设计工作。在这样一个访问量巨大的网站上实现流式文字排版，我主要关注两个问题。

第一，我如何避免破坏布局？尤其是当字体大小以不同速率变化时我以为导航容器中的内容会溢出。但令我惊讶的是，这根本不是个问题。

实际上，内容可以自然地调整以适应容器，而在之前需要指定分界点来调整。总之，样式表需要更少的媒体查询，并且在大部分情况下，组件也需要的样式声明比原来少得多。

这是个意外收获，我们还在许多地方抛弃了对平板电脑的优化设计，因为桌面版现在也能在更小的屏幕上工作了。

下面是一个之前需要在不同屏幕尺寸中需要若干调整的导航组件实例。虽然在小屏幕下不完美，但字体紧凑，功能健全，也没有破坏布局。

[![A colorful navigation component adjusting to the viewport-width](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/component-example-500-opt.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/component-example-full-opt.png)

([点击查看大图](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/05/component-example-full-opt.png))

我的第二个问题是，有些元素是固定宽度，我也知道不可能每一行都达到完美行宽。实际上，我将字体大小调整为手机和平板上面的完美长度 —— 我认为这是最重要的。

还有不得不接受的情况是，由于主容器没有以和文字以同速率缩放，一些文字可能在浏览器调整大小的时候不可避免地回流。之所以称之为一个问题，是因为我不想让读者在旋转设备时丢失他们的阅读焦点。但因为我们无法避免这个问题，所以我们唯一能做的就是在指定设备上测试一下有多少影响。很幸运的是回流在移动设备上几乎很难被注意到，甚至在大的平板电脑上这个影响也是极小的。

我们对最终的效果很满意。然而我们可以做更多来提升字体排版，毫无疑问这么做提升了网站的可读性。鉴于从实现流式字体排版中吸取的教训，该网站设计的下一个迭代应主要关注字体如何排版以及网站的可读性方面。

### 在已有网站上实现流式文字排版的建议

*  认真地选择缩放比例、最小和最大字体大小，确保能与你的设计完美契合。

*  字体大小使用单位 em。如果你需要在某一特定容器中禁用流式文字排版，例如导航栏，那么可将容器的字体大小设为一个固定值。这时该容器中单位为 em 的值将与这个固定值相关。

*   同样地，如果你需要给某一特定容器设置流式字体排版。那你可以给该容器的字体大小设置一个不定值，那容器中单位为 em 的值会变成流式的。

*   如果文字回流只发生在浏览器调整大小或设备旋转的时候，那没必要把它当作一个坏事情。

*   保持完美行宽是不可能的。主要考虑手机和平板电脑的完美行宽就行。

### 浏览器支持与 Bugs

我经常听说 Safari，尤其是移动版 Safari，在使用视口单位和 `calc()` 表达式时都有严重的 bug。然而，很多时候都没有提到具体问题，所以我想我得自己测试。

我分别测试了 `calc()` 表达式和视口单位，以及结合了视口单位和 `calc()` 的进阶版流式字体排版技术。但我没有发现任何阻止我使用这些技术的问题。

`calc()` 和视口单位在高级浏览器上都能正常工作。但是在低于 Safari8 和 IE11 的版本里，当 `calc()` 表达式中使用了视口单位时，浏览器窗口改变时不会重新计算结果。

你可以通过额外的媒体查询来强制表达式重新计算：

```
/* 老版本浏览器 */
html { font-size: 16px; }

/* 高级浏览器只需要下面这个*/
@media screen and (min-width: 25em){
    html { font-size: calc( 16px + (24 - 16) * (100vw - 400px) / (800 - 400) ); }
}

/* Safari <8 和 IE <11 */
@media screen and (min-width: 25em){
    html { font-size: calc( 16px + (24 - 16) * (100vw - 400px) / (800 - 400) ); }
}
@media screen and (min-width: 50em){
    html { font-size: calc( 16px + (24 - 16) * (100vw - 400px) / (800 - 400) ); }
}
```

这个技术强制低于 Safari8 和  IE11 的浏览器在指定分界点处重新计算字体大小。

在 JavaScript 中使用窗口重绘事件来强制低版本浏览器重新布局也许可以，但我还没有找到一个可依赖方法来这么做，还有这样做可能会影响性能。在大多数情况下，在指定分界点处重新计算是这些浏览器可接受的一个降级方案。

对于那些[不支持视口单位](http://caniuse.com/#feat=viewport-units)和 [calc() 表达式](http://caniuse.com/#feat=calc)的古董浏览器，流式技术可以直接忽略。如果设置了合理的默认值，这些技术也可以作为渐进增强方案。

### 今天如何使用流式字体排版

如果你计划实现流式字体排版，首先你得确定采用哪种方法。

如果整个设计都是流式的，那你可以考虑将 root em 设置为流式的，通过在元素 `html` 上声明一个使用流式单位的字体大小。然后，整个设计的其他部分都使用 `em` 或者 `rem` 单位就可以了。

认真地选择最小和最大的字体大小。在这一点上，你需要决定是否直接使用视口单位，还是更精确控制缩放速率。如果选择后者，那么[Sass](http://www.sassmeister.com/gist/7f22e44ace49b5124eec)或者 [LESS](http://codepen.io/MadeByMike/pen/RWJyML) 的 mixin 或者 [PostCSS](https://www.npmjs.com/package/postcss-responsive-type) 插件会帮你省不少工作。

一开始就选择好最小和最大的字体大小。这是最重要的选择。因为其他所有组件的字体大小都依赖 root em。在项目后期修改 root em，意味着你必须调整所有的一切。

在使用流式技术之前别忘了声明的一个默认的字体大小。这个默认值会在不支持流式单位的浏览器上工作，它不用等于最小的字体大小。

最后，你还需要考虑设计稿的限制，以及如何处理如标题级别和行宽等事情。看看文章中提到的这些技术。如果你想让组件而不是标题以与常规文本不同的速率缩放，那在添加 `calc()` 表达式之前考虑可读性和样式表的维护。

希望你看完本文能有所启发，考虑下个项目是否适合使用流式字体布局。

#### 必读

*   “[Fit to Scale](http://trentwalton.com/2011/05/10/fit-to-scale/),” Trent Walton

*   “[Fluid Type](http://trentwalton.com/2012/06/19/fluid-type/),” Trent Walton

*   “[Viewport Sized Typography](https://css-tricks.com/viewport-sized-typography/),” Chris Coyier


#### 相关阅读

*   “[Responsive Typography With SASS Maps](https://www.smashingmagazine.com/2015/06/responsive-typography-with-sass-maps/),” Jonathan Suh

*   “[Benton Modern: A Case Study on Art-Directed Responsive Web Typography](https://www.smashingmagazine.com/2015/05/benton-modern-typography-case-study/),” Marko Dugonjić

*   “[Advanced Web Typography: Responsive Web Typography](http://www.elliotjaystocks.com/blog/responsive-web-typography/),” Elliot Jay Stocks


#### 扩展阅读

*   “[Everything I Know About Responsive Web Typography](http://www.zell-weekeat.com/responsive-typography/),” Zell Liew

*   “[Responsive Typography With REMs: How to Build a Scalable Typographic Foundation in Three Steps](http://blog.bugsnag.com/responsive-typography-with-rems),” Max Luster

*   “[Fluid Typography for Responsive Websites](http://academy.bindtuning.com/fluid-type-for-responsive-websites/),” Ana Sampaio

*   “[Responsive Typography](https://responsivedesign.is/design/responsive-typography),” Responsivedesign.is














