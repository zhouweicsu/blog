---
layout: post
category : HTML5
title: 「译」别再丢用户与应用程序的状态了，赶紧用 Page Visibility 吧
tagline: "Don't lose user and app state, use Page Visibility"
tags : [HTML5, Page Visibility]
---

**优秀的应用从不丢失用户的进度与应用程序状态。**而是在不中断用户的情况下自动保存相关数据，然后在必要的时候（例如：从后台状态或者意外关机中回来）恢复这些数据。

不幸的是，许多 Web 应用因为没考虑移动端的生命周期从而导致了这个错误：它们错误地监听了也许永远不会被触发的事件，或者完全忽略这个问题从而导致代价高昂的极差用户体验。不过公平地说，Web 平台提供的诸多不同的事件（[visibilityState](http://w3c.github.io/page-visibility/#introduction)、 [pageshow](https://developer.mozilla.org/en-US/docs/Web/Events/pageshow)、 [pagehide](https://developer.mozilla.org/en-US/docs/Web/Events/pagehide)、 [beforeunload](https://developer.mozilla.org/en-US/docs/Web/Events/beforeunload)、 [unload](https://developer.mozilla.org/en-US/docs/Web/Events/unload)）也使问题变得复杂。我们到底应该使用哪个事件，什么时候用？

<!-- more -->

![事件流][1]

**你在移动平台不能依赖 `pagehide`、 `beforeunload`、 和 `unload` 事件。**因为也许在你喜欢的浏览器中使用没有 bug，但应用程序得运行在所有的移动操作系统中。活动的应用程序可以通过不同的方式进入到“后台状态”：

- 用户点击了一个通知，切换到另一个应用。
- 用户唤起了任务切换栏，去了另一个应用。
- 用户摁了 home 键去了首屏。
- 操作系统切换应用，例如，来了个电话。

应用程序一旦被切换到后台，随时可能被干掉。例如，操作系统会终止该进程以回收系统资源，用户也可以在任务切换栏中将它清除。因此，你应该认为触发 `pagehide`、 `beforeunload`、 和 `unload` 事件的“正常关闭”就是个例外。

为了提供可靠流畅的用户体验，无论是在 PC 端还是移动端，应用程序必须使用 [Page Visibility API](http://w3c.github.io/page-visibility/#introduction)，根据 `visibilityChange` 状态的变化保存或恢复当前会话。这是应用程序唯一可以依赖的事件。

```javascript
// query current page visibility state: prerender, visible, hidden
var pageVisibility = document.visibilityState;

// subscribe to visibility change events
document.addEventListener('visibilitychange', function() {
  // fires when user switches tabs, apps, goes to homescreen, etc. 
    if (document.visibilityState == 'hidden') { ... }

    // fires when app transitions from prerender, user returns to the app / tab.
    if (document.visibilityState == 'visible') { ... }
});
```

如果你使用 `unload` 事件来保存状态，记录和报告分析数据，并执行其他相关逻辑，那你将会丢失大部分移动端会话数据，因为很多 unload 事件不会被触发。同样，如果你依赖 `beforeunload` 事件来提示用户没保存数据，那你忘记了刚才说的“正常关闭”就是一个意外。

**使用 Page Visibility API，忘记其他事件，就当它们都不存在。**将每次应用程序切换到 `visible` 都当做一个新的会话：恢复之前的状态，重置你的分析状态等等。然后，当应用程序切换至 `hidden` 时结束该会话：保存用户与应用程序状态，标记你的分析数据，以及做好所有其他必要的工作。

*如果有必要，你可以做一些额外的工作，将这些基于可见性的会话都汇聚到记录应用程序与标签切换的用户流中，例如，给服务器汇报每一个会话，在服务器端将多个会话合并在一起。*

### 实际实现中的问题

长期来说，你只要 Page Visibility API 就够了。但现在你必须得与另一个事件 ———— `pagehide` 一起用，目的是为了考虑“页面要被卸载了”的情况。下表记录了当前每个浏览器下哪个操作会触发哪个事件的详细数据（基于我的[手动测试](http://output.jsbin.com/zubiyid/latest/quiet)）。

![测试数据][2]

- `visibilityChange` 可靠反映移动端任务切换。
- `beforeunload` 价值有限，只能在 PC 浏览器上被触发。
- `unload` 在移动端和 PC 端的 Safari 中都不会被触发。

可喜的是 Page Visibility 依赖覆盖了所有平台和浏览器厂商的任务切换场景。但是，到现在为止 Firefox 是唯一一个在页面 unloaded 的时候还会触发 `visibilityChange` 事件的浏览器，这里是[Chrome](https://code.google.com/p/chromium/issues/detail?id=554834)、 [WebKit](https://bugs.webkit.org/show_bug.cgi?id=151234)、 [Edge](https://github.com/w3c/page-visibility/issues/18#issuecomment-156031906) 有关这个问题的 bug。只要这些 bug 被修复，`visibilityState` 将是提高用户体验唯一需要的事件了。


  [1]: https://1-ps.googleusercontent.com/sk/bYSmB63yuhjL_l7bPRuu4R3ENi/www.igvita.com/posts/15/xlifecycle-events.png.pagespeed.ic.CwbcWo2upgP_fc8JwyaO.png
  [2]: https://1-ps.googleusercontent.com/sk/bYSmB63yuhjL_l7bPRuu4R3ENi/www.igvita.com/posts/15/xlifecycle-events-testing.png.pagespeed.ic.mLbvU6UX-AQJSwkOff9e.png
