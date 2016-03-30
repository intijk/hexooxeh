---
title: 利用pxe+dhcp+tftp 在archlinux下建立多系统的PXE启动服务器
tags:
  - arch
  - archlinux
  - dhcp
  - linux
  - pxe
  - tftp
  - tftpd
  - 多启动
  - 操作系统
  - 无盘启动
  - 网络安装
  - 网络引导
id: 732
categories:
  - Linux
date: 2011-09-18 11:50:29
---

PXE可以让计算机从一块网卡引导启动进入系统, 通过网络的方式,免去了光盘和U盘的麻烦,没有介质的损耗和硬件的更改, 就可以让计算机进入操作系统. 本文的目的在于帮助你建立一个局域网的引导和系统安装部署. 局域网内的计算机接入后,可以引导入一个菜单,在此菜单下,可以选择若干种操作系统的网络安装程序进行后续安装.
完成图:

文中配置均在一台安装有Archlinux的计算机上完成,当前内核版本为3.0\. IP为192.168.1.1

PXE 引导的过程:
![](https://www.ibm.com/developerworks/cn/linux/l-tip-prompt/l-pex/1.gif "pxe 原理")
**1\. 配置dhcp**
[plain]
#pacman -S dhcp
#vim /etc/dhcpd.conf
subnet 192.168.1.0 netmask 255.255.255.0 {
	range dynamic-bootp 192.168.1.201 192.168.1.210;
	allow bootp;
	filename &quot;pxelinux.0&quot;;
        next-server 192.168.1.1;
}
#/etc/rc.d/dhcp4 restart
[/plain]
其中next-server 用来指定tftp服务器的位置,可以不写这行,那么默认该dhcpd服务器就是tftpd服务器.
filename 是bootstrap文件的名称,这里是pxelinux.0
**2\. 配置tftpd**
[plain]
pacman -S tftp-hpa
#/etc/rc.d/tftpd start
[/plain]
这样就成功开启了tftpd服务,默认的tftp根目录是:
[plain]
/var/tftpboot
[/plain]
**3\. 放入bootstrap文件和启动配置**
下载一个archlinux-2011.08-2-archboot.iso
[plain]
#mount archlinux-2011.08-2-archboot.iso /iso
#cp -r /iso/boot /var/tftpboot/
#mkdir -p /var/tftpboot/pxelinux.cfg
#cp /var/tftpboot/boot/syslinux/syslinux.cfg /var/tftpboot/pxelinux.cfg/default
#mv /var/tftpboot/boot/syslinux/* ..
[/plain]
计算机在启动之后,通过dhcp获得了IP地址,然后通过tftp根目录下得到了pxelinux.0文件并加载到内存中执行,然后去寻找tftp根目录下pxelinux.cfg/default这个配置文件,按照这个配置文件的内容执行tftp根目录下的vesamenu.c32创建出目录界面.
现在, 你只需要在局域网内有一台电脑,配置为pxe启动即可成功引导入archlinux的网络安装界面
**4\. 为网络引导添加更多发行版的内核支持**
目前已经可以通过网络引导进入archlinux的网络安装界面, 那么如何添加其它系统呢,只需要把其它系统的引导光盘中的内核vmlinuz与虚拟盘initrd.img取出即可,以CentOS为例:
[plain]
#umount /iso
#mount CentOS-6.0-i386-netinstall.iso /iso
#mkdir -p /var/tftpboot/centos
#cp /iso/images/pxeboot/vmlinuz /var/tftpboot/centos
#cp /iso/images/pxeboot/initrd.img /var/tftpboot/centos
[/plain]
然后编辑/var/tftpboot/pxelinux.cfg/default 文件:
[plain]
#vim /var/tftpboot/pxelinux.cfg/default
加入以下内容
LABEL centosi686
TEXT HELP
Boot the centos 
It allows you to install CentOS Linux or perform system maintenance.
ENDTEXT
MENU LABEL Boot Cent OS i686
LINUX /centos/vmlinuz
APPEND initrd=/centos/initrd.img
[/plain]
其中 MENU LABEL 后面跟的 Boot Cent OS i686是显示在屏幕上的标题, LINUX命令后跟要引导的内核文件, APPEND这句指明了需要引导的虚拟盘映像.
在局域网内再次启动pxe引导的计算机,就会发现引导列表中多了Boot Cent OS i686这一项,选中后进入CentOS的网络安装界面.

参考文章:
1\. [http://blog.vkill.net/read.php/101.htm](http://blog.vkill.net/read.php/101.htm)
2\. [http://88fly.blog.163.com/blog/static/1226803902010112045848273/](http://88fly.blog.163.com/blog/static/1226803902010112045848273/)
3\. [https://www.ibm.com/developerworks/cn/linux/l-tip-prompt/l-pex/](https://www.ibm.com/developerworks/cn/linux/l-tip-prompt/l-pex/)
4\. [http://hi.baidu.com/yuhongchun027/blog/item/8ebd44b7102ee1f331add1b1.html](http://hi.baidu.com/yuhongchun027/blog/item/8ebd44b7102ee1f331add1b1.html)