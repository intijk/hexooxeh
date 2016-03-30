---
title: 在Ubuntu中关闭ipv6
tags:
  - ipv6
  - ubuntu
id: 98
categories:
  - 其它
date: 2010-10-28 05:07:54
---

将/etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
改为
GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=1 quiet splash"

这其实是从grub启动时向内核发了消息告诉内核: 嘿,ipv6那个模块不好使,别启动它了.
之后
sudo update-grub
重启OK
查看ifconfig如果没有inet6的信息就说明被关闭了