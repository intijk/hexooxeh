---
title: Bionic libc
tags:
  - Android
  - bionic
  - C4Droid
  - glibc
  - libc
id: 841
categories:
  - Linux
date: 2012-01-12 11:28:54
---

Google 为了保持Android的“清洁”， 没有用庞大而全面的glibc，而专门开发了Bionic libc用来替代glibc。Bionic libc有以下几个特点：

1.  BSD协议

2.  小  200K，但是功能不全，比如我用 bzero和 bcopy这两个函数就没有。不得不用memset和memcpy替代。

3.  快  本来就小，又专门针对频率低的ARM CPU做过优化，所以运行的速度要比glibc快。

上次介绍的[ C4Droid](http://intijk.com/?p=838) 编译器里面带的库，就是bionic libc。