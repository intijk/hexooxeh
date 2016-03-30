---
title: 计算机图形学
tags:
  - canvas
  - html5
  - 计算机图形学
id: 148
categories:
  - Web
date: 2010-11-20 08:40:27
---

这个学校的计算机图形学据说很强大，但是看看本科生教学的质量就知道惨不忍睹。
好吧，作业是做一个能够将自己名字进行平移, 缩放, 旋转, 错切等变换的程序。大多数人采用MFC这种山洞里的原始人的做法，我想到了html5的canvas，用来绘图是很好的载体，于是研究了一下，canvas 的 context 对象直接支持修改变换矩阵，这给我带来很大的方便。
成品在[这里](http://intijk.com/p/test.html)，但是 IE<=8 的用户无法正常显示，是由于 html5 的支持问题，Firefox，Chrome，Opera 均显示正常。