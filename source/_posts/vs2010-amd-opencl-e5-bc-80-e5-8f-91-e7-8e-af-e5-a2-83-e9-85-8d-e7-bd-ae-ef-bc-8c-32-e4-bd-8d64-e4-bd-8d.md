---
title: VS2010 AMD  OpenCL 开发环境配置， 32位+64位
tags:
  - 64位
  - AMD
  - opencl
  - vs2010
  - x64
  - 配置
id: 538
categories:
  - HPC
date: 2011-07-22 06:29:03
---

首先你要装好催化剂显卡驱动。

下载AMP APP SDK安装包，比如64的就叫AMD-APP-SDK-v2.4-Windows-64.exe，安装AMD APP SDK，如图0。
[![](http://intijk.com/wp-content/uploads/2011/07/0.png "0")](http://intijk.com/?attachment_id=540)
安装完之后重新启动。

首先知道一下安装的位置，我这里按默认装在了 C:\Program Files (x86)\AMD APP\ ，如图1。

[![](http://intijk.com/wp-content/uploads/2011/07/1.png "1")](http://intijk.com/?attachment_id=541)图1

系统安装的时候系统已经设置了一个环境变量AMDAPPSDKROOT来指示安装的位置，可以在cmd里查看，如图2。

[![](http://intijk.com/wp-content/uploads/2011/07/2.png "2")](http://intijk.com/?attachment_id=542)图2

系统也已经把可执行路径加到了PATH里，这样就可以找到OpenCL的运行库。如图3。

[![](http://intijk.com/wp-content/uploads/2011/07/3.png "3")](http://intijk.com/?attachment_id=543)图3

打开一个已有的OpenCL项目，或者新建一个项目。
如果新建项目，点击文件->新建，建立一个空项目。加入一个自己写的HellowCL之类的源码，如果没有，我这里写了一个初始化的小源码，诸位可以[自行下载](http://intijk.com/p/OpenclConfig.cpp)添加，结果如图6。OpenclConfig.cpp

[![](http://intijk.com/wp-content/uploads/2011/07/6.png "6")](http://intijk.com/?attachment_id=545) 图6

因为安装包已经在系统的PATH路径里加入了动态链接库的路径，所以就没有必要再在VS里添加了，我们需要做的就是添加需要的包含

目录和库目录，为了同时兼容32位和64位系统，需要对32位系统和64位系统差异对待。如果你只考虑单个平台下的配置，那么只需要

以下内容中相应平台的配置即可。

由于默认的环境是Win32，我们在这里添加一个x64的平台支持，前提是你安装了64位的编译器，如果你不需要64位，那么往下看即可

。
1\. 左侧项目名右键->属性
2\. 看到属性页窗口之后，点击右上方的配置管理器，
3\. 在配置管理器中下拉，选择<新建>
4\. 在键入或选择新平台中下拉，选择x64，确定
以上过程如图7。

[![](http://intijk.com/wp-content/uploads/2011/07/7.png "7")](http://intijk.com/?attachment_id=546)图7

一、通用配置，32位和64位都得有的，打开项目属性页：
（1）左上角配置选所有配置，平台选所有平台
（2）C/C++->常规->附加包含目录 添加$(AMDAPPSDKROOT)include，如图8。
[![](http://intijk.com/wp-content/uploads/2011/07/8.png "8")](http://intijk.com/?attachment_id=547)图8
（3）链接器->输入，附加依赖项里加入OpenCL.lib

二、32位独有的配置，项目属性页中：
（1）左上角配置选所有配置，平台选Win32
（2）链接器->常规 附加库目录，加入$(AMDAPPSDKROOT)lib\x86,如图9。
[![](http://intijk.com/wp-content/uploads/2011/07/9.png "9")](http://intijk.com/?attachment_id=548)图9

三、64位独有的配置，项目属性页中：
（1）左上角配置选所有配置，平台选x64
（2）链接器->常规 附加库目录，加入$(AMDAPPSDKROOT)lib\x86_64,如图10。
[![](http://intijk.com/wp-content/uploads/2011/07/10.png "10")](http://intijk.com/?attachment_id=549)图10

以上配置完成之后即可编译运行，在上面给出的代码中，如果看到图11,说明编译运行成功。
[![](http://intijk.com/wp-content/uploads/2011/07/11 "11")](http://intijk.com/?attachment_id=550)图11

如果32位和64位的都配置好了，可以在工具栏里选择Debug/Release,Win32/x64的任意组合进行编译。

望大家Coding愉快！