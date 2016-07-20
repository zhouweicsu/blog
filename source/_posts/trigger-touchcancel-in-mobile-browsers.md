---
layout: post 
category : mobile 
title: 手机浏览器下触发touchcancel事件的操作 
tagline: "Different ways to trigger touchcancel in mobile browsers" 
tags : [移动开发, touchcancel, mobile browsers, touch] 
date: 2014-12-09 
---

原文链接：[Different ways to trigger touchcancel in mobile browsers][4]

Touchcancel事件在Javascript创建touch-interfaces时常常被忽视。而浏览器厂商也从未正式发布详细说明touchcancel事件何时会被触发的文档，因此，对于许多开发者而言touchcancel事件一直默默无闻。本贴希望能揭开touchcancel的神秘面纱，助你清楚地了解touchcancel事件。

touch事件最初是Apple为Safari浏览器创建的，现在已经被纳入了[W3C Touch 事件规范][1]。原始的[Safari DOM参考文档][2]关于touchcancel事件的信息少的可怜，仅有一句话：

>当系统取消跟踪touch事件时触发。

幸运地是，W3C Touch事件规范提供了更多的细节，包括一些会触发该事件的场景：

>在touch事件被系统自定义行为干扰之后，客户端必须触发touchcancel事件，例如客户端原生的同步事件或操作取消了touch事件，或者触摸点滑出文档窗口移至一个可以处理用户交互的非文档区域（如，客户端原生界面，插件）。另外，当用户放置的触摸点超出了设备可存储的数量，那么最早进入TouchList的Touch对象也将被移除，此时客户端会触发该Touch对象的touchcancel事件。

从上一段描述中我们可以进一步理解touchcancel事件与浏览器行为的关系。这不仅能帮助浏览器精确地引用TouchList中的Touch对象，而且也能被开发者用来实现特定的UI操作，例如在touchstart和touchmove事件处理函数中重置正在使用的变量。当系统取消了跟踪touch事件但touchend事件没有被触发时，touchcancel将会拯救你。

那浏览器什么时候触发这个事件？开始研究之前有必要构建一个简单的事件日志来跟踪所有的touch事件。测试主要记录在touch区域滑动时最后一个被触发的touch事件，这些日志将使我们更加直观地分析到底是哪种交互操作触发了touchcancel事件。

以下是我们在多种手机浏览器上的发现。值得指出的是touchcancel事件很不可预测的，尤其在安卓（2.3.5）上。甚至在iOS5上有时也不能准确预测什么操作会触发touchcancel事件。

###iOS Safari 5.0.1（iPhone 4 & iPad 1）###
在touchstart或touchmove事件被触发时，会触发touchcancel事件的操作：

> * 用户按下home键
> * 用户按下Safari浏览器底部工具栏的操作按钮
> * 用户在屏幕中使用手掌触摸或涂抹，生成了许多不可辨别的单一触摸点（仅iPhone？）
> * 用户在iPad上使用四个或更多手指同时触摸，或使用系统设置中的多任务手势

在touchstart或touchmove事件中不会触发touchcancel事件的操作：

> * 用户按下Safari浏览器底部工具栏中的书签按钮
> * 用户按下Safari浏览器底部工具栏中的前进或回退按钮
> * 用户点击聚焦在Safari浏览器的搜索栏
> * 用户旋转设备的方向
> * 屏幕弹出系统通知，例如WiFi查找或闹钟提醒
> * 屏幕上方弹出iOS5风格的新通知
> * 用户新开了一个tab（iPad）
> * 用户刷新网页
> * 用户按下音量键
> * 用户切换设备静音/振动的开关
> * 用户接到一个来电

在有些情况下以上操作中，浏览器会直接触发一个touchend事件而不是touchcancel事件。在其他情景中（例如旧风格的弹出通知），touchstart和touchmove事件仍然是起作用的（即使内容已经被通知覆盖），除非用户移开手指触发了touchend事件。
值得注意的是当touchstart或者touchmove事件被触发时，部分iOS5系统方法是无法执行的：

> * 用户不能使用Safari浏览器的地址栏
> * 用户不能打开一个新页面（iPhone）
> * 用户不能双击home键打开应用盒子（app tray）
> * 用户不能从屏幕上方往下滑动打开通知中心
> * 用户无法打开屏幕上方出现的iOS5风格的通知

###安卓2.3.5（Samsung Galaxy Y GT-S5363）内置浏览器###
在touchstart或touchmove事件被触发时，会触发touchcancel事件的操作：

> * 用户按下锁屏键
> * 此种情况下，似乎没有明显的原因touchcancel事件也会被随机触发，这种现象在重复快速点击和滑动的时候尤其显著

在touchstart或touchmove事件中不会触发touchcancel事件的操作：

> * 用户按下home键
> * 用户旋转设备的方向
> * 用户按下音量键

当touchstart或者touchmove事件被触发时，部分安卓2.3.5系统方法是无法执行的：

> * 用户无法按下菜单键和回退键
> * 用户无法打开通知菜单
> * 用户无法聚焦到浏览器地址栏
> * 用户无法点击书签按钮

### Opera Mobile ###

安卓版Opera Mobile 11.50 

也测试了。然而浏览器似乎支持touchcancel事件，实际上他并未触发这个事件。例如在按下home键的时候，Opera Mobile似乎直接触发了touchend事件。

###黑莓平板电脑 (1.0.8.6067)###
尽管浏览器声称支持touchcancel事件，但该事件似乎并未被触发。即使按下电源键使设备待机，touchcancel事件也未被触发。同样的，按下浏览器工具条的按钮或聚焦在地址栏都未触发此事件。另外值得注意的是，当触发touchstart或touchmove事件后，平板电脑不会响应任何滑动的手势。

##结论##

从测试结果看，touchcancel事件仍然是不可预测的。然而确定在哪种情况下事件会被触发还是很容易的，在哪种情况下不被触发也几乎能确定。那么这个事件的作用也无需出现在本次研究中，因为在所有可预期的情况下，开发者目前似乎无法依赖这个事件的触发。

然而可以肯定的是，你无法在每台设备上都仅仅只依赖touchend事件被的触发。你需要编写处理touchcancel事件的代码。例如在iPad上尤其如此，假如你写了个多点触控的app或联机的HTML5游戏。由于程序没有办法辨别用户是否启用了多任务手势，这时你的app就需要优雅地处理touchcancel事件。安卓浏览器不可预测的特性也是使得处理这个事件变得尤为重要，不这么做将会导致你的UI出现bug。

所以，当你下次处理touch事件时，别忘记处理touchcancel事件。尽管许多浏览器支持此事件，但在处理某些意想不到的用户交互时还是非常方便的。


译者 [@zhouweicsu][3]     
2014 年 12月 09日    

[1]: http://www.w3.org/TR/2011/WD-touch-events-20110505/
[2]: https://developer.apple.com/library/safari/documentation/UserExperience/Reference/TouchEventClassReference/TouchEvent/TouchEvent.html#//apple_ref/doc/uid/TP40009358
[3]: https://github.com/zhouweicsu
[4]: http://alxgbsn.co.uk/2011/12/23/different-ways-to-trigger-touchcancel-in-mobile-browsers/