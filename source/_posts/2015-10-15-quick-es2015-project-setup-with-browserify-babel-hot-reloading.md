---
layout: post
category : JavaScript
date: 2015-10-15
title: 「译」用 Browserify， Babel & hot reloading 快速构建 ES2015 工程
tagline: "Quick ES2015 project setup with Browserify, Babel & hot reloading"
tags : [JavaScript, ES6, Browserify, Babel, hot reloading]
---


现在有很多 ES2015 和 React 工程的新手工具包。如果你想快速构建一个原型或者你只是想玩一玩代码，那你肯定希望配置越少越好。必备工具集有： transpiler（编译工具）， bundler（打包工具） 和 hot reloader（热重载）（因为是2015，所以你肯定不希望在重新加载页面上浪费时间）。Browserify 是一个非常棒并且使用起来很简单的 bundler 。更重要的是——它是模块化的，这样你就能在任意时间连接 plugins 和 transforms。Babel 是我们选择的 transpiler，无论你是否使用React，它都很好用。hot reloader 是为了提高开发效率。相对于手动或自动重新加载整个页面，hot reloader 仅仅让浏览器更新部分代码。因此它会很快，并且可以保持应用程序状态，所以你不需要重复之前的操作回到原来的UI状态。本文的例子不是介绍另一个新手工具包，而是教你如何以最少的必备工具开始 ES2015 工程。

<!-- more -->

新建一个 ```package.json``` 并且安装下面的 packages：

```
$ npm i -D browserify babelify watchify serve
```

前三个是 bundler， Babel插件 和 文件监视器， `serve` 包是一个静态服务器。

将如下代码添加至`package.json`，这样 Browserify 就会在指定的 stage 下使用 Babel 编译器。

```
{
  "browserify": { "transform": [ ["babelify", {"stage": [0]}] ]
}
```

最后，这里有一段 NPM 脚本使用 source maps 来启动 bundler 并进入监视模式，且在指定的目录下启动静态服务器。

```
"scripts": {
  "start": "watchify src/index.js -o public/js/bundle.js -dv & serve public"
}
```

在热模块替代方面，我选择 Browserify 安装如下插件与必备包来重新加载 React 组件：
```
$ npm i -D livereactload react-proxy babel-plugin-react-transform
```

新建 `.babelrc` 文件并添加以下内容。这个会告诉 Babel transform 使用已经安装过的插件。

```
{
  "env": {
    "development": {
      "plugins": [ "react-transform" ],
      "extra": {
        "react-transform": {
          "transforms": [{
            "transform": "livereactload/babel-transform",
            "imports": ["react"]
          }]
        }
      }
    }
  }
}
```

现在将`livereactload`插件加入 NPM 脚本，然后你就可以开始愉快的玩耍了。

```
"scripts": {
  "start": "watchify src/index.js -o public/js/bundle.js -dv -p livereactload & serve public"
}
```

若要开发产品或者获取更多深入资料，请访问 GitHub, 你会找到海量现成的模板。

原文链接：[Quick ES2015 project setup with Browserify, Babel & hot reloading](https://medium.com/@roman01la/quick-es2015-project-setup-with-browserify-babel-hot-reloading-d2ff3fb88032)
