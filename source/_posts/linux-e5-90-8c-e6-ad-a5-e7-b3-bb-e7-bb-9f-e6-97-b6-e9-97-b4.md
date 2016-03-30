---
title: Linux 同步系统时间
id: 604
categories:
  - Linux
date: 2011-08-27 11:01:01
tags:
---

pacman -S ntp
ntpdate 210.72.145.44 (国家授时中心)
hwclock --hctosys   (写入主板上的ROM内)