---
title: C4Droid
tags:
  - Android
  - C4Droid
  - gcc
  - tcc
  - 系统编程
  - 编译器
id: 838
categories:
  - Linux
date: 2012-01-11 09:14:37
---

最近想给Android上写点程序，于是装了C4Droid，这个工具相当于一个手机上的小IDE了。

最让人兴奋的是支持系统编程，套接字什么的也可以，头文件和库文件放在：

/mnt/sdcard/Android/data/com.n0n3m4.droidc/files/tcclibs

它的编译器叫做tcc（Tiny C Compiler), 可执行程序在：

/data/data/com.n0n3m4.droidc/files

就在可执行目录下，你编写的程序会被起名为temp.c,编译之后，生成的可执行文件叫temp，也在该目录下。

&nbsp;

本来，为Android写可执行程序其实用arm gcc即可，但是交叉编译环境的配置略显麻烦。用C4Droid，安装快速还不出问题，对开发小型的linux程序非常有帮助。

&nbsp;