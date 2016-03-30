---
title: Ubuntu 安装 Win7 后找回 grub 的过程
tags:
  - grub
  - ubuntu
  - win7
  - 找回
id: 472
categories:
  - 其它
date: 2011-05-27 09:33:38
---

方法有点繁琐，但是不用任何光盘U盘之类的东西。

1\. 下载 grub4dos 4.4
将其中 grldr grldr.mbr 两个文件拷贝到 C:\ 
新建文件 C:\boot.ini 写入
[boot loader]
[operating systems]
c:\grldr.mbr="Grub4Dos"

2\. 重启，启动项选中 Grub4Dos
grub4dos界面中选中commandline
grub>root (hd0,6) 
注意，这里因人而异，hd0代表第一块硬盘，6代表第7个分区（从0计数），这个分区是原来Ubuntu的 / 分区
grub>kernel /boot/vmlinuz-2.6.32-21-generic
grub>initrd /boot/vmlinuz-2.6.32-21-generic
这里文件名可能有不同，反正你会用Tab
grub>boot
然后就进入了 busybox 的虚拟盘

3\. 挂载重装grub
这一步把 /usr 分区挂载上（我的/usr和/在不同的分区）
(initrmfs) mkdir /usr
(initrmfs) mount -t ext4 /dev/sda8 /usr
挂载 /
(initrmfs) mkdir /mnt
(initrmfs) mount -t ext4 /dev/sda7 /mnt
接下来重装grub
/usr/sbin/grub-install --root-directory=/mnt /dev/sda

grub装好了，重启可见
(initrmfs) reboot