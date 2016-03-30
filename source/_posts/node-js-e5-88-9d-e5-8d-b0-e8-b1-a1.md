---
title: Node.js 初印象
id: 1148
categories:
  - Web
  - 笔记
date: 2012-12-15 00:18:45
tags:
---

今天草草的看了node.js的介绍，得出一些初步印象：

1.  node.js是在服务端跑的js， 用google的V8引擎, 少了所有浏览器的限制。
2.  node.js出名靠的是它的异步事件机制，由于javascript本身对事件处理很强，V8引擎又很快，所以异步的效率很高。
3.  node.js最受诟病的是它的单线程机制，无法运用多核的优势。
4.  纯性能比较nginx强过node.js，一种推荐是nginx+node.js的组合，优势很大。
5.  超大型网站里github和twitter有一部分使用node.js,[这里](https://github.com/joyent/node/wiki/Projects,-Applications,-and-Companies-Using-Node)可以看试用node.js的网站列表