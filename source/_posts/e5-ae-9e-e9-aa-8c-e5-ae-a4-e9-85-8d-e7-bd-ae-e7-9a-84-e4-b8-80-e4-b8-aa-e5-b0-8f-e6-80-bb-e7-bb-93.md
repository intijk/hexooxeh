---
title: 实验室配置的一个小总结
tags:
  - archlinux
id: 151
categories:
  - 其它
date: 2010-11-23 11:17:10
---

这两天把实验室的网关机器换成了archlinux。 arch这个发行版给我的感觉是很干净，轻量级的运行，很简单的设置与操作。
网关机器有两块网卡, 一块是集成的，另一块是新买的 RealTek 螃蟹卡，8139的。

1\. 驱动。 安装archlinux的时候自动安装了集成网卡的驱动，独立的这块8139 用 
<pre class="bush:plain;">
hwdetect --show-net
</pre>
可以看到有8139too这个模块可使用
<pre class="bush:plain;">
modprobe 8139too
</pre>
就成功的安装了网卡。
关于一些通用的配置均放在 /etc/rc.conf , 系统全局配置的一些东西，优先在这里设置。

2\. 包管理， pacman
这个包管理的东西还是刚刚接触，原来在Ubuntu下用的 apt-get 带有自动补全的功能，刚上手发现有些不习惯，不过它的好处就是不用关心具体的包的版本号，只要想安装某个软件，直接
<pre class="bush:plain;">
pacman -S softname
</pre>
即可。比如上面说的 hwdetect 便可以
<pre class="bush:plain;">
pacman -S hwdetect
</pre>
3\. ssh
作为一台服务器，怎么地也得有个ssh才管理起来方便，于是首先安装之
<pre class="bush:plain;">
pacman -S ssh
</pre>
这样就安装了ssh和sshd， 也就是说你同时有了客户端和服务器端，sshd的配置文件放在 /etc/ssh/sshd_config
保证以下设置便能够正常运行：
首先在/etc/ssh/sshd_config
<pre class="bush:plain;">
Port 22
ListenAddress 0.0.0.0
Protocol 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_dsa_key
LoginGraceTime 2m
PermitRootLogin yes
PasswordAuthentication yes
PermitEmptyPasswords no
ChallengeResponseAuthentication no
UsePAM yes
Subsystem sftp /usr/lib/ssh/sftp-server
</pre>
然后就是配置 /etc/hosts.allow 和 /etc/hosts.deny
看名字就知道，这两个文件是用来配置允许访问和拒绝访问的，对于archlinux，这两个文件是所有网络访问的门户，可以在其中设置全局可访问以及禁止访问的主机及服务。
既然要允许访问我们刚刚配置好的ssh，只需在 /etc/hosts.allow
<pre class="bush:plain;">
sshd:ALL
</pre>
相应的，如果你安装了别的服务需要进行允许访问的设置，也可以在这里进行添加, 比如你开启了httpd 和 vsftpd
就添加
<pre class="bush:plain;">
vsftpd:ALL
httpd:ALL
</pre>
后面也可以是非ALL的参数，比如一个网段的访问允许权限可以设置为
<pre class="bush:plain;">
sshd: 192.168.1.0/255.255.255.0
</pre>
/etc/hosts.deny 保证内容是
<pre class="bush:plain;">
ALL: ALL: DENY
</pre>
然后重启服务
<pre class="bush:plain;">
/etc/rc.d/sshd restart
</pre>
即可正常访问
4\. 拨号上网
这里学校很恶心的采用了私有协议802.1x进行认证，用的是h3c， 这个东西在windows下运行良好，但是linux就会有些问题。软件的名称叫做 h3cclient
配置要分3步走
(1). 将windows原来拨号得到的IP先分给网卡
<pre class="bush:plain;">
ifconfig eth0 1.2.3.4
</pre>
(2). 拨号
<pre class="bush:plain;">
h3cclient eth0 -u username/password
</pre>
(3). dhcp重新获取ip
dhclient eth0
这样就重新拿到了一个可以使用的IP，和刚才静态手动分配的IP是在同一网段的。
如此一来，互联网就通了，可以测试一下
<pre class="bush:plain;">
ping g.cn
</pre>
5\. iptables 配置网关
iptables的原理是存储转发，它有filter，mangle，nat三个表，在这里我们需要将所有内网的机器通过这台网关机器共享上网，网关机的eth0 为外网地址1.2.3.4 内网地址为 eth1 192.168.1.1
首先允许ip转发
<pre class="bush:plain;">
echo 1 > /proc/sys/net/ipv4/ip_forward
</pre>
<pre class="bush:plain;">
iptables -t nat -A POSTROUTING -s 192.168.1.0/255.255.255.0 -o eth0 -j MASQUERADE
</pre>
这行命令将192.168.1.0/255.255.255.0 这个网段的所有发往eth0 的数据包进行伪装转发 MASQUERADE，运行之后即可实现共享上网。
6\. 端口映射
还是iptables,  比如内网有 192.168.1.2 的 httpd 服务器，这时可以将所有来自80端口的访问映射到192.168.1.2:80。
<pre class="bush:plain;">
iptables -t nat -A POSTROUTING -d 1.2.3.4 -p tcp --dport -j DNAT --to 192.168.1.2:80
</pre>
7\. 打印机共享
实现打印机共享主要依靠 cups服务
没什么说的，首先装起
<pre class="bush:plain;">
pacman -S cups
</pre>
安装完毕后打开 /etc/cups/cupsd.conf
将
<pre class="bush:plain;">
Listen localhost:631
</pre>
改为
<pre class="bush:plain;">
port 631 #注意，这里不要冒号
</pre>
保证有下面
<pre class="bush:plain;">
# Restrict access to the server...
<Location />
  Order allow,deny
  Allow From localhost
  Allow From 192.168.1.0/255.255.255.0
</Location>

# Restrict access to the admin pages...
<Location /admin>
  Order allow,deny
  Allow From localhost
  Allow From 192.168.1.0/255.255.255.0
</Location>

# Restrict access to configuration files...
<Location /admin/conf>
  AuthType Default
  Require user @SYSTEM
  Order allow,deny
  Allow From localhost
  Allow From 192.168.1.0/255.255.255.0
</Location>
</pre>
就可以在任何一台机器上通过 http://192.168.1.1/631 访问cups的管理界面，进去以后就是点点点的过程了，没什么好说的。
8\. scp命令
这条命令可以方便的将文件从当前机器上拷贝到另外一台机器上，
比如你有一个文件要拷贝到 192.168.1.1 的root用户的home目录，只需
<pre class="bush:plain;">
scp filename root@192.168.1.1: #这里:后面可以写入重命名的文件名，如果不重命名则留空
</pre>

以上就是这两日来配置的一些学习成果，收获很大，尤其是 iptables 的灵活与强大是值得每个网络管理员深入进行了解和学习的。
就到这里。