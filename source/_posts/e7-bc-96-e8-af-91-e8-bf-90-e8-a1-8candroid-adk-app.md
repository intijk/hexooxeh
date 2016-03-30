---
title: '编译运行Android ADK app '
tags:
  - adk
  - app
  - Arduino
  - demokit
  - eclipse
id: 948
categories:
  - Arduino
date: 2012-02-10 14:31:40
---

<span style="font-size: medium;">ADK既然是Android和Arduino的通信，那就说明手机上有一个app，Arduino上有一个Firmware，下面我记录了编译手机端的Android ADK app程序的步骤：</span>

<span style="font-size: medium;">**1\. 安装应有的库**</span>
<span style="font-size: medium;"> 执行 /tools/android</span>
<span style="font-size: medium;"> ， 出现图形界面，安装Android 2.3.3(API 10)下面的SDK Platform和 Google APIs by Google Inc.</span>

<span style="font-size: medium;">[![](http://intijk.com/wp-content/uploads/2012/02/110.png "1")](http://intijk.com/wp-content/uploads/2012/02/110.png)</span>

<span style="font-size: medium;">**2\. 编译Demo工程**</span>
<span style="font-size: medium;"> 打开Eclipse ， File → new → Android Project → Next</span>

<span style="font-size: medium;">[![](http://intijk.com/wp-content/uploads/2012/02/210.png "2")](http://intijk.com/wp-content/uploads/2012/02/210.png)</span>

<span style="font-size: medium;">在Project 处写入DemoKit，选择Create project from existing source , 点击Browse 选中/app目录，点击Next下一步</span>

<span style="font-size: medium;">[![](http://intijk.com/wp-content/uploads/2012/02/32.png "3")](http://intijk.com/wp-content/uploads/2012/02/32.png)</span>

<span style="font-size: medium;">Select Build Target 里选择Google APIs， Platform 2.3.3 ， API Level 10，点击Next</span>

<span style="font-size: medium;">[![](http://intijk.com/wp-content/uploads/2012/02/41.png "4")](http://intijk.com/wp-content/uploads/2012/02/41.png)</span>

<span style="font-size: medium;">在Minimum SDK中选择 10 (Google APIs (Google Inc.))，点击Finish</span>
<span style="font-size: medium;"> [![](http://intijk.com/wp-content/uploads/2012/02/51.png "5")](http://intijk.com/wp-content/uploads/2012/02/51.png)</span>

<span style="font-size: medium;">**3\. 在AVD里测试运行情况**</span>
<span style="font-size: medium;"> 点击工具栏中的AVD管理图标。</span>
<span style="font-size: medium;"> [![](http://intijk.com/wp-content/uploads/2012/02/61.png "6")](http://intijk.com/wp-content/uploads/2012/02/61.png)</span>

<span style="font-size: medium;">选择要生成的AVD的参数</span>

<span style="font-size: medium;">[![](http://intijk.com/wp-content/uploads/2012/02/71.png "7")](http://intijk.com/wp-content/uploads/2012/02/71.png)</span>

<span style="font-size: medium;">在左侧的Package Explorer上面右键点击 DemoKit， Run as → Android Application</span>
<span style="font-size: medium;"> [![](http://intijk.com/wp-content/uploads/2012/02/81.png "8")](http://intijk.com/wp-content/uploads/2012/02/81.png)</span>
<span style="font-size: medium;"> 结果如图所示：</span>
<span style="font-size: medium;"> [![](http://intijk.com/wp-content/uploads/2012/02/91.png "9")](http://intijk.com/wp-content/uploads/2012/02/91.png)</span>