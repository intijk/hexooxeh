---
title: Windows 和 Linux 时间不同步的问题 (Sync time between Linux and Windows)
tags:
  - 8小时
  - linux
  - 时间
  - 系统
  - 错位
id: 1113
categories:
  - Linux
  - 笔记
date: 2012-10-25 16:15:41
---

Linux 认为CMOS里存储的时间是UTC+0 ,然后系统中存储了时区信息，通过这两个值，生成当地时间。
_Linux treat the Time saved in CMOS as the UTC+0, after calculating with the TimeZone info in system, you get the local time._

Windows 认为CMOS里存储的就是本地时区的时间。于是双系统的同学总是有8小时穿越的神奇经历。
_Windows treat the Time saved in CMOS as local time. So those who have dual or more systems would find time shifting after rebooting from another system._

解决方法是让Windows也把CMOS里存储的时间作为UTC+0,修改方法是新建注册表键值：
_Solution is to set Windows treating the CMOS time as UTC+0\. Create a key in regedit,_

**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation\RealTimeIsUniversal**

类型设置为DWORD,值设置为1.
_Type set as DWORD and value as 1._

MSDN的原文在[这里](http://msdn.microsoft.com/en-us/library/ff794720(v=winembedded.60).aspx)。
_You can check the description on MSDN [here](http://msdn.microsoft.com/en-us/library/ff794720(v=winembedded.60).aspx)._