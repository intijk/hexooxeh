---
title: Android刷机指南，基于HTC G1/Dream， Under ArchLinux
tags:
  - Android
  - G1
  - HTC
  - 刷机
id: 962
categories:
  - android
  - Arduino
  - Linux
date: 2012-02-11 07:03:31
---

<span style="font-size: medium;">**刷机分哪几个部分？**</span>

1.  <span style="font-size: medium;">SPL</span>
<span style="font-size: medium;"> IPL(Initial Program Loader) 首次引导</span>
<span style="font-size: medium;"> SPL（Second Program Loader) 二次引导</span>
<span style="font-size: medium;"> IPL引导SPL，SPL引导系统，一般IPL不用动，刷SPL即可，相当于pc上刷Bios</span>
2.  <span style="font-size: medium;">Radio</span>
<span style="font-size: medium;"> 手机比电脑多的就是一个通信功能，全靠一个基带芯片，驱动这个基带芯片的就是这个基带程序，叫Radio。</span>
3.  <span style="font-size: medium;">Recovery</span>
<span style="font-size: medium;"> Recovery 是开机的时候方便刷机而进入的一个功能完善的应用，可以在里面格式SD卡，选择zip文件进行刷机，清空系统缓存等等。</span>
4.  <span style="font-size: medium;">ROM</span>
<span style="font-size: medium;"> ROM叫做Read Only Memory只读存储器，它叫这个名字不代表它就不能被写入，而是只能在刷机的时候写入，里面就是android的操作系统，所以刷机，绝大多数指的都是刷rom</span>

<span style="font-size: medium;">**查看各个部件的版本**</span>

<span style="font-size: medium;">[![](http://intijk.com/wp-content/uploads/2012/02/211.png "2")](http://intijk.com/wp-content/uploads/2012/02/211.png)</span>

<span style="font-size: medium;">**刷机有哪几种方法？**</span>

1.  <span style="font-size: medium;">Recovery方法，这种方法用的普遍，是在Recovery界面下，选择zip文件，就刷新到了系统上</span>
2.  <span style="font-size: medium;">fastboot方法，这种方法在命令行下使用，可以直接把文件写到rom里，非常强力。</span>

<span style="font-size: medium;">**G1刷机的注意事项：**</span>

<span style="font-size: medium;">    SHIP 和Engneering 两种SPL是有区别的，Eng版的SPL才能够使用fastboot刷机。详见[这篇文章](http://www.htctips.com/%E7%99%BD%E5%8D%A1%E8%A7%A3%E9%94%81ship-s-off%E4%B8%8E%E8%BD%AF%E8%A7%A3%E9%94%81eng-s-off%E6%98%AF%E4%BB%80%E4%B9%88%EF%BC%9F%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB%EF%BC%9F%E5%9B%BE)：</span>
<span style="font-size: medium;">    [spl-signed.zip](http://attachments.hiapk.com/download/other/spl-signed.zip) 这个是目前比较好用的G1的eng版SPL，版本是hboot-1.33.2005，而hboot hboot-1.33.0013d.img这个是用的最多的SPL，不是ENG而是SHIP的。</span>

<span style="font-size: medium;">    可以在[这里](http://forum.xda-developers.com/showpost.php?p=9055790&amp;postcount=1)查看xda论坛上的刷机帖，包括img都可以从中下载到。</span>

<span style="font-size: medium;">**关于刷机的工具**</span>
<span style="font-size: medium;">    使用adb和fastboot工具非常方便。Adb工具可以从android sdk中获取，fastboot在aur里有，用yaourt安装。</span>

<span style="font-size: medium;">_Adb的常用命令：_</span>
<span style="font-size: medium;"> adb devices 列出当前连接的设备</span>
<span style="font-size: medium;"> adb push /path/to/file/on/pc /path/on/phone/ 从pc向手机拷贝文件或文件夹</span>
<span style="font-size: medium;"> adb pull /path/to/file/on/phone /path/on/pc 从手机向pc拷贝文件或文件夹</span>
<span style="font-size: medium;"> adb install package.apk 安装软件包</span>
<span style="font-size: medium;"> adb install -s package.apk 安装软件包到SD卡</span>
<span style="font-size: medium;"> adb reboot recovery 重启进入恢复模式</span>
<span style="font-size: medium;"> adb reboot bootloader 重启进入引导模式（三色屏）</span>
<span style="font-size: medium;"> 在Recovery模式下，选择USB-MS toggle之后，adb也是可以使用的。</span>

<span style="font-size: medium;"> _fastboot 常用命令_</span>
<span style="font-size: medium;"> fastboot devices 列出当前连接的设备</span>
<span style="font-size: medium;"> fastboot flash recovery recovery-RA-dream-v1.7.0-cyan.img 刷1.7的recovery</span>
<span style="font-size: medium;"> fastboot flash hboot hboot-1.33.0013d.img 刷 1.33.0013d的SPL</span>
<span style="font-size: medium;"> fastboot flash radio radio-2.22.28.25.img 刷2.22.28.25的基带驱动</span>
<span style="font-size: medium;"> fastboot需要进入SPL即三色屏界面之后才能使用。</span>

<span style="font-size: medium;">**刷Radio和SPL的基本步骤：**</span>

<span style="font-size: medium;">刷的时候SPL界面的右侧会出现一个红色的进度条。</span>

<span style="font-size: medium;">**刷Rom**</span>

*   <span style="font-size: medium;">Rom我建议用Recovery刷，可以精确的控制是否要清除各个Cache以及sd-ext分区</span>
<span style="font-size: medium;"> 1\. 把rom的zip文件放在sd卡上</span>
<span style="font-size: medium;"> 2\. 重启进入recovery模式，选择rom对应的zip文件，选中即可开始。</span>