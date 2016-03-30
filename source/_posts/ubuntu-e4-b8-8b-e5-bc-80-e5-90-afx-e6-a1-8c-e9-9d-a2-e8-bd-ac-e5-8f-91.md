---
title: Linux 下开启X桌面转发
tags:
  - linux
  - X
  - 桌面
  - 转发
id: 502
categories:
  - Linux
date: 2011-07-12 02:13:50
---

首先明确两个概念，什么是X里的Server和Client。
有一台你正在操作的计算机A，一台远程计算机B，你需要让B上的程序运行并显示在A的屏幕上，那么A这时候是Server，因为它接收处理绘图请求，B是Client，它请求“把我绘制在桌面上吧”。

在Server，即你眼前的这台计算机A上，编辑/etc/gdm/custom.conf,让DisallowTCP=false，这样就允许了TCP连接,内容如下。
[daemon]
DisallowTCP=false
重启gdm
# /etc/init.d/gdm restart
加xhost允许
xhost Client的IP

此时，ssh到B上，执行如下命令，测试是否OK
export DISPLAY=Server的IP：0
gnome-calculator
如果一个计算器程序出现在你眼前，说明Client机B上的X已经被转发。

更多的，如果你希望远程登录后自动开启一个转发，那么在上面的基础上，进行如下操作。
保证B计算机的/etc/ssh/sshd_config里有：
X11Forwarding yes
然后重启sshd
/etc/rc.d/sshd restart
加-X选项登录机器B：
ssh B -X
运行图形程序：
gnome-calculator
你可以在B那里看看自己的DISPLAY：
echo $DISPLAY
结果是：
localhost:10.0
说明B处的10号桌面通过ssh的转发到达了A。