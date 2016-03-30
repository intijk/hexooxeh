---
title: OpenCV的配置与效果图
id: 74
categories:
  - 其它
date: 2010-09-24 10:21:24
tags:
---

将OpenCV进行了编译和安装，具体的编译过程在[这里](http://opencv.willowgarage.com/wiki/InstallGuide)。
但是Ubuntu装好之后还会出现一些问题，就是libml这个库找不到，其实这个库已经编译出来了，只不过安装以后没有识别到相应的链接路径
处理方法：
在~/.bashrc里面加入
`export LD_LIBRARY_PATH=/usr/local/lib`
然后
`source ~/.bashrc`
用命令
` env | grep LD_L`
查看结果，出现
`LD_LIBRARY_PATH=/usr/local/lib`
就OK了，详情可以参考[这里](http://se.cs.ait.ac.th/cvwiki/opencv:tutorial:install:ubuntu)。
这里是实例程序中的edge程序，我将原来的照片换成了自己的魔方图片进行了重编译。

[![](http://intijk.com/wp-content/uploads/2010/09/cubic1-300x132.png "cubic")](http://intijk.com/wp-content/uploads/2010/09/cubic1.png)