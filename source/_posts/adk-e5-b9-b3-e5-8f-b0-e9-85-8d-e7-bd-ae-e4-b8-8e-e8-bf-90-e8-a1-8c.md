---
title: ADK平台配置与运行
tags:
  - adk
  - Android
  - app
  - Arduino
  - G1
  - linux
  - 配置
id: 977
categories:
  - android
  - Arduino
date: 2012-02-11 10:28:14
---

<span style="font-size: medium;">作为小车计划的一部分，控制方面，要实现小车和Arduino的通信，于是我采用ADK，它的全名是：</span>

<span style="font-size: medium;">Accessory Development Kit，是在Google I/O 2011大会上宣布的。目前支持2.3.4以上的Android版本。不过不幸的是，很多手机，像G1，需要一些Geeky的方法才能让它run起来，这篇文章就是给你一个框架概念和操作流程，如果你手头上有一套ADK，如何让它跑起来。</span>

<span style="font-size: medium;">本文需要一些前置的知识：</span>

<span style="font-size: medium;"> Arduino的基本知识，写过Arduino程序最好。</span>

<span style="font-size: medium;"> adb工具的使用</span>

<span style="font-size: medium;"> Linux系统下文件的权限</span>

<span style="font-size: medium;"> 会给Android刷机（但你不一定要刷）</span>

<span style="font-size: medium;">**基础知识**</span>

<span style="font-size: medium;"> 一个完整的ADK应用，要有以下几部分组成：</span>

<span style="font-size: medium;"> 1\. 一个宿主Arduino板</span>

<span style="font-size: medium;"> 2\. 一个USB_HOST_Shiled板</span>

<span style="font-size: medium;"> 3\. 一台Android手机</span>

<span style="font-size: medium;"> 4\. 一个运行于Arduino板上的固件</span>

<span style="font-size: medium;"> 5\. 一个运行于Android手机上的App</span>

<span style="font-size: medium;"> 是的，他们的长像就像下面这个样子：</span>

<span style="font-size: medium;">[![](http://intijk.com/wp-content/uploads/2012/02/1.jpg "1")](http://intijk.com/wp-content/uploads/2012/02/1.jpg)</span>

<span style="font-size: medium;">[![](http://intijk.com/wp-content/uploads/2012/02/2.jpg "2")](http://intijk.com/wp-content/uploads/2012/02/2.jpg)</span>

<span style="font-size: medium;">我呢，主要是要用这个板子来控制我的车http://intijk.com/?p=854，你可以用它来做任何你想要的项目。</span>

<span style="font-size: medium;">硬件都可以直接从淘宝上购买，Arduino的种类可以自由选择，现在据我所知，这几种板子的支持都没有问题：</span>

<span style="font-size: medium;"> Arduino Uno</span>

<span style="font-size: medium;"> Arduino Duemilanove</span>

<span style="font-size: medium;"> Arduino ATmega 2560</span>

<span style="font-size: medium;"> 我使用的是Arduino Duemilanove 。</span>

<span style="font-size: medium;">**确认手机支持ADK，否则就得刷Rom**</span>

<span style="font-size: medium;"> 手机我用的是HTC G1，可能你觉得它太老了，但你想，G1如果都能做，就任何Android手机都能做。</span>

<span style="font-size: medium;">首先，你要查看你的手机是否支持ADK，在编译内核的时候，有一个选项，如果没有选，就不支持。所以如果发现你的内核不支持，就需要重新刷rom来解决了。我原来的CM7.1-rc2-0901的rom就不支持，所以刷了[EzGingerBread的Rom](http://forum.xda-developers.com/showthread.php?t=882356)。</span>

<span style="font-size: medium;">那么，如何判断你的内核OK不OK呢？首先请出Android的瑞士军刀：adb工具。</span>

<span style="font-size: medium;">adb pull /proc/config.gz</span>

<span style="font-size: medium;"> 解压，出现config文件</span>

<span style="font-size: medium;">打开config，搜索</span>

<span style="font-size: medium;"> CONFIG_USB_ANDROID_ACCESSORY</span>

<span style="font-size: medium;"> 如果出现的是</span>

<span style="font-size: medium;"> CONFIG_USB_ANDROID_ACCESSORY=y</span>

<span style="font-size: medium;"> That means you are a lucky boy! 你的Rom没有问题！</span>

<span style="font-size: medium;">但如果它被注释掉了，或者没有，那么请节哀，我写的刷机文章在[这里](http://intijk.com/?p=962)，你把SPL刷到hboot-1.33.0013d，把Radio刷到radio-2.22.28.25，把Rom刷成GINGERBREAD-DS-Stable-v1.0-20111204.zip，刷机之后，再次检查/proc/config.gz 直到有CONFIG_USB_ANDROID_ACCESSORY=y</span>

<span style="font-size: medium;">**补全必要的库文件**</span>

<span style="font-size: medium;">有这一步还不代表你的手机就是ADK 无障碍的，还可能缺少运行库。检查是否有如下两个文件，如果没有，说明你需要它。</span>

<span style="font-size: medium;">/system/framework/com.android.future.usb.accessory.jar</span>

<span style="font-size: medium;"> /system/etc/permissions/android.hardware.usb.accessory.xml</span>

<span style="font-size: medium;">我把它放在这里[usb_accessory](http://intijk.com/wp-content/uploads/2012/02/usb_accessory.zip)供你下载，它们是来自我写的[这篇文章](http://intijk.com/?p=948)里的，通过adb从AVD的镜像里抽出来的。</span>

<span style="font-size: medium;">好的，把它们push到你的手机上。在此之前，请注意读写权限问题。强烈推荐你用RE管理器把/system目录挂载为读写，对于高手来说，搜一下android remount，注意，不解决读写问题，无法push文件。</span>

<span style="font-size: medium;">adb push com.android.future.usb.accessory.jar /system/framework/</span>

<span style="font-size: medium;"> adb push android.hardware.usb.accessory.xml /system/etc/permissions/</span>

<span style="font-size: medium;">一般来说，传上去的文件权限是 -rw-rw-rw 而我们需要和其它库文件的权限保持一致-rw-r--r--，所以用adb获得手机的shell，执行：</span>

<span style="font-size: medium;">adb shell</span>

<span style="font-size: medium;"> chmod go-w /system/framework/com.android.future.usb.accessory.jar</span>

<span style="font-size: medium;"> chmod go-w /system/etc/permissions/android.hardware.usb.accessory.xml</span>

<span style="font-size: medium;">然后重启一下。</span>

<span style="font-size: medium;"> 到这里，你就拥有了一台ADK Enable的手机了！很给力哦亲！</span>

<span style="font-size: medium;">**准备固件**</span>

<span style="font-size: medium;"> 我们开始准备固件和App。</span>

<span style="font-size: medium;"> 固件的获得很简单，首先请你按[这篇文章](http://intijk.com/?p=943)配置好0023版本的Arduino和ADK的开发环境，如果你是在Windows下，就只要下载0023的Arduino并配置ADK就可以，不需要理会文中关于avr-gcc的部分。</span>

<span style="font-size: medium;"> 然后从github上下载（什么？你木有github，为什么不呢？Every cool boy have a one! Go to github.com and get it!)https://github.com/felis/USB_Host_Shield_2.0</span>

<span style="font-size: medium;">下载了之后，就有一个USB_Host_Shiled_2.0目录，删掉/libraries/USB_Host_Shield目录，</span>

<span style="font-size: medium;"> 把下载的这个目录后面的_2.0去掉，放在/libraries/下面</span>

<span style="font-size: medium;">启动你的arduino 0023，如果现在打开了，请关闭掉所有的arduino窗口，然后重启。</span>

<span style="font-size: medium;"> 选择File → Examples → USB Host Shield → adk → demokit_20</span>

<span style="font-size: medium;"> [![](http://intijk.com/wp-content/uploads/2012/02/33.png "3")](http://intijk.com/wp-content/uploads/2012/02/33.png)</span>

<span style="font-size: medium;"> Tools → Board选择你对应的Arduino类型。</span>

<span style="font-size: medium;"> 编译上传即可。</span>

<span style="font-size: medium;"> 到这个位置，固件搞定。</span>

<span style="font-size: medium;">**准备App**</span>

<span style="font-size: medium;"> 然后参照这篇文章http://intijk.com/?p=948，编译出App，安装在手机上。</span>

<span style="font-size: medium;">到这里，固件和App就都搞定了。</span>

<span style="font-size: medium;">**运行起来**</span>

<span style="font-size: medium;"> 给Arduino板子上电，用usb线连接手机和板子，然后点一下Shiled板上的Reset键。手机上出现如下画面，点击确定，就运行成功了。</span>

<span style="font-size: medium;"> [![](http://intijk.com/wp-content/uploads/2012/02/42.png "4")](http://intijk.com/wp-content/uploads/2012/02/42.png)</span>

<span style="font-size: medium;">在出现的界面里点out，滑动下面第一个条，可以控制arduino板3号引脚的输出电压，接一个LED上去。就可以看到效果，我录制了一段视频在后面。</span>

<span style="font-size: medium;"> 如果有什么问题不懂或者没做成，直接在下面留言，或者邮箱Gtalk联系我： intijk 爱特 gmail 点 com</span>

<span style="font-size: medium;"><object width="480" height="400" classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="src" value="http://player.youku.com/player.php/sid/XMzUxMzgyMTcy/v.swf" /><param name="quality" value="high" /><param name="allowscriptaccess" value="sameDomain" /><param name="allowfullscreen" value="true" /><embed width="480" height="400" type="application/x-shockwave-flash" src="http://player.youku.com/player.php/sid/XMzUxMzgyMTcy/v.swf" quality="high" allowscriptaccess="sameDomain" allowfullscreen="true" /></object></span>