---
title: Archlinux下配置adk开发板
id: 943
categories:
  - Arduino
date: 2012-02-10 09:43:05
tags:
---

首先说一点，就是目前的adk开发需要的arduino版本号和avr-gcc的版本号都不是最新版，要旧版本才可以运行流畅，为这个我鼓捣了一天。我列出我配置中的这几个软件的版本号，下载配置即可，助你少走弯路。

arduino-0023
avr-gcc 4.6.0
adk_release_0512

下载
[arduino-0023.tgz](http://arduino.googlecode.com/files/arduino-0023.tgz)
[gcc-avr-4.6.0-3-i686.pkg.tar.xz](file:///home/solo/Downloads/gcc-avr-4.6.0-3-i686.pkg.tar.xz)
[adk_release_0512.zip](file:///home/solo/Downloads/adk_release_0512.zip)

1\. 安装gcc-avr 4.6.0
pacman -U gcc-avr-4.6.0-3-i686.pkg.tar.xz
pacman -S avr-libc

2\. 解压arduino-0023
tar zxf arduino-0023.tgz

3\. 解压adk_release_0521
unzip adk_release_0512.zip

4\. 安装库文件
cp -r ADK_release_0512/firmware/arduino_libs/* arduino-0023/libraries/

5\. 执行
cd arduino-0023
./arduino

6\. 编译
File → Open → ADK_release_0512/firmware/demokit/demokit.pde → OK
Tools → board → Arduino Mega 2560

点击verify
如果编译无误，说明成功。

[![](http://intijk.com/wp-content/uploads/2012/02/ArduinoAdk.png "ArduinoAdk")](http://intijk.com/wp-content/uploads/2012/02/ArduinoAdk.png)