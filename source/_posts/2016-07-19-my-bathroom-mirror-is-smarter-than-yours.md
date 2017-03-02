---
layout: post 
category : 翻译
date: 2016-07-19
title: 「译」我的智能浴室镜
tagline: "My bathroom mirror is smarter than yours" 
tags: [翻译, mirror] 
---


#### 买不到智能镜就自己做一个

去年年底某个时候，我觉得浴室里的镜子得像[电影里](http://i.imgur.com/reGCqve.jpg)未来世界中长的那样。

但似乎市场上没有人卖我想要的这种镜子。然而，组成镜子的每个零部件都很常见。[最近](https://github.com/HannahMitt/HomeMirror)也有很多人做了[类似](http://michaelteeuw.nl/tagged/magicmirror)的[自制](https://learn.adafruit.com/android-smart-home-mirror/)[智能](http://blog.dylanjpierce.com/raspberrypi/magicmirror/tutorial/2015/12/27/build-a-magic-mirror.html)浴室镜，但我想象中的与这些有些不同。


<!-- more -->

所以我自己订购了一个[双面镜](http://www.twowaymirrors.com)，一个[展示板](http://www.amazon.com/dp/B00H0FK2A6/)，一个[控制面板](http://www.ebay.com/itm/360626141655)，外加一些[组件](http://www.amazon.com/gp/product/B0050J1QYA)和[手工艺术品](https://goo.gl/maps/2Y75DQvNsDN2)。实际上，做好这个镜子之前我进行了相当多的实验，许多实验失败了，还是让我们看看完成的（但绝不是最终的）作品：



![](http://p0.qhimg.com/t0117e9d7f93c24a0e3.jpg)

欢迎来到我的浴室，请忽略药柜旁边我已经精心整理过的杂七杂八和那个普通的镜面。

镜子的右边是时间和日期。左边是当前的天气以及 24 小时的预报。下面是最近的新闻头条。来个特写：



![](http://p0.qhimg.com/t0126121b66acb3737b.jpg)

除了多云的天气，其他天气的时候 UI 会有一些色彩，但为了避免太分散注意力，大部分文字和图标都是单色的。

这些 UI 的实现使用了一些简单的安卓 API（例如：[这个](http://developer.android.com/reference/android/widget/TextClock.html)很不错），天气预报的 [API](https://developer.forecast.io) 和[美联社](http://hosted2.ap.org/APDEFAULT/APNewsFeeds)的新闻订阅。

其他我正在尝试的有交通情况，事件提醒，以及任何有 [Google Now card](https://www.google.com/landing/now/#cards) 的东西。我的观点就是你不必与这些 UI 交互。因为他会自动更新，谷歌还提供了一个[开放的语音搜索](http://www.google.com/search/about/features/01)接口，你想搜什么就可以搜什么。



![](http://p0.qhimg.com/t01f3f458aa58635340.jpg)

嵌入双面镜与药柜门之间的展示板只有薄薄的几毫米。这样会很整洁，我还能继续使用柜子里的空间。下图是以一定角度打开门拍的图，你可以看一下边缘处：


![](http://p0.qhimg.com/t012e365c82f6a4a2b6.jpg)
![](http://p0.qhimg.com/t019d6f07ca5f49fdd6.jpg)

这个原型还在不断改进，我还没有花太多时间在软件上。上面提到的 UI 也仅有几百行代码，我还尝试使用不同的设备来运行它——一开始是 [Chromecast](https://www.google.com/chromecast/tv/)，后来使用了 [Nexus Player](https://www.google.com/nexus/player/)，最近用的是 [Fire TV Stick](http://www.amazon.com/dp/B00ZVJAF9G)。

我把这些电子设备固定在平板上并整理了一下，要不然看上去会一团糟：



![](http://p0.qhimg.com/t01cecc285db6a7e521.jpg)

以上是这个项目目前的所有进展。期待实现剩余的一些想法。也许我还会更新制作过程中一些更详细的图片。
                