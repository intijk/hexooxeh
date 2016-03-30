---
title: gxemul
tags:
  - arm
  - gxemul
id: 200
categories:
  - 其它
date: 2010-12-26 01:02:41
---

在《Computer Organization and Design》这本书的ARM版本中，没有附带一个好的试验环境，由于其MIPS版是主流，所以在作者在随书光盘中放了spim这个模拟器，用来模拟MIPS环境，但是spim功能太单一，只能模拟MIPS指令集，对于以ARM为学习模型的人很不方便。 于是我找到了 [gxemul](http://gxemul.sourceforge.net/) 。

gxemul 是一个模拟器的framework，也就是说理论上它可以支持无限多的指令集，目前在其上能够模拟的指令集有 ARM, MIPS, Motorola 88K, PowerPC 和 SuperH 。

以下是它的框架结构，可以看到，以root为虚拟根节点，能够构造出 root.machine1.mainbus0.cpu0 这样的节点，而只要实现了这些相应的节点，就能够实现对这种设备的模拟。
[![](http://intijk.com/wp-content/uploads/2010/12/moz-screenshot-300x179.png "moz-screenshot")](http://intijk.com/wp-content/uploads/2010/12/moz-screenshot.png)
也许你会觉得 gxemul 没有 VirtualBox 强大，但是作为教学和调试无疑是一个非常好的工具，而且在gxemul上已经能够运行一个完整的操作系统了，见下图：
[![](http://intijk.com/wp-content/uploads/2010/12/openbsd-pmax-20040710-300x259.png "openbsd-pmax-20040710")](http://intijk.com/wp-content/uploads/2010/12/openbsd-pmax-20040710.png)

利用gxemul，你完全可以做出自己的arm手机操作系统， 但是本文章的目的在于使用教学和汇编调试。
首先到[这里](http://gxemul.sourceforge.net/download.html)下载gxemul的最新版本，解压之后:
[plain]
./configure
make
make install
[/plain]
make install 的时候要有 root 权限（当然了，你也可以指定别的安装层级的目录，但这里简单起见用 root 权限）。
我们创建一个arm语言的汇编文件，取名为 a.s，放在 test 目录下，内容很简单，就是让 r0 这个寄存器的变量自增 1 。
[plain]
.global s
s:
	add r0,r0,#1
[/plain]

为了编译这个 a.s 文件，你需要有一个交叉编译环境，arm-linux-gcc 这个东西就是我们需要的，但是建立交叉编译环境确实比较复杂，如果不熟悉binutils(比如本人-_-!)，你可以使用[这里](http://blog.mcuol.com/User/langke279/Article/39009_1.htm)提供的方法使用一个已经编译好的环境。编译好以后不仅有arm-linux-gcc，还有 arm-linux-as，和 arm-linux-ld 等工具。

为了方便编译流程，我们写一个 makefile 文件，什么？你不知道什么是 makefile？ -_-!! 送佛送到西，见[这里](http://www.chinaunix.net/jh/23/408225.html)。

以下 makefile 文件用来自动编译和生成 arm 的二进制指令文件，写好以后把它放到和 a.s 同在的 test 目录下。
[plain]
all:
	arm-linux-as -g a.s -o a.o
	arm-linux-ld -e s a.o -o a
clean:
	rm a a.o 
[/plain]
只要在 test 目录下执行 make 命令，就会生成可执行的二进制文件 a 。

然后就是加载运行了，前面说过，gxemul是一个模拟器框架，可以支持任意种类的指令集，只要实现了相应的节点即可，目前已经有很多种 cpu 节点和 machine 节点被实现， 所以我们可以直接拿来调试。gxemul -H 可以列出目前支持的所有设备。

[plain]
gxemul -e ipaq -V a
[/plain]
其中 -e 是选择环境， 我们选择 ipaq ，这是个很早的arm的小型掌上电脑（跟小时候的“商务通“一个性质）， -V 是让gxemul 以交互方式，也就是一步一步执行我们的指令，a 就是我们刚才编译出来的二进制文件。
出现如下字样代表已经进入了gxemul的交互模式。
[plain]
GXemul 0.6.0    Copyright (C) 2003-2010  Anders Gavare
Read the source code and/or documentation for other Copyright messages.

Simple setup...
    net:
        simulated network: 10.0.0.0/8 (max outgoing: TCP=100, UDP=100)
        simulated gateway+nameserver: 10.0.0.254 (60:50:40:30:20:10)
        simulated nameserver uses real nameserver 202.117.112.3
    machine:
        memory: 32 MB
        cpu0: SA1110 (I+D = 16+16 KB)
        machine: Compaq iPAQ H3600
        bootstring: a
        loading a
        cpu0: starting at 0x00008054

NOTE: This is a LEGACY emulation mode.

-------------------------------------------------------------------------------

GXemul&gt;  
[/plain]
我们在后面输入命令
[plain]
GXemul&gt;reg
[/plain]
来显示寄存器，会出现：
[plain]
cpu0:  cpsr = nzcvIF   pc = 0x00008054  &lt;s&gt;
cpu0:  r0 = 0x00000001  r1 = 0x01fffe00  r2 = 0x01ffff00  r3 = 0x00000000
cpu0:  r4 = 0x00000000  r5 = 0x00000000  r6 = 0x00000000  r7 = 0x00000000
cpu0:  r8 = 0x00000000  r9 = 0x00000000  sl = 0x00000000  fp = 0x00000000
cpu0:  ip = 0x00000000  sp = 0xc02dfff0  lr = 0x00000000
[/plain]
观察到 r0 寄存器的值是 0x1, 然后输入step执行下一步：
[plain]
GXemul&gt; step
&lt;s&gt;
00008054:  e2800001	add	r0,r0,#1
[/plain]
当输入 step 以后显示的命令就是我们在 a.s 文件中写入的那条命令，显示之后代表此命令已经被执行。
然后再次 reg 我们可以看到寄存器的变化：
[plain]
GXemul&gt; reg
cpu0:  cpsr = nzcvIF   pc = 0x00008058  &lt;__exidx_end&gt;
cpu0:  r0 = 0x00000002  r1 = 0x01fffe00  r2 = 0x01ffff00  r3 = 0x00000000
cpu0:  r4 = 0x00000000  r5 = 0x00000000  r6 = 0x00000000  r7 = 0x00000000
cpu0:  r8 = 0x00000000  r9 = 0x00000000  sl = 0x00000000  fp = 0x00000000
cpu0:  ip = 0x00000000  sp = 0xc02dfff0  lr = 0x00000000
[/plain]
注意到了吗 r0 的值从 0x1 变到了 0x2, 也就是我们的那条指令生效了。

以上就是使用 gxemul 调试程序的整个过程，你也可以输入help命令查看命令列表，或者到主页上查看其相关文档。

如果你要从事 arm 或其它一些 RISC 指令集汇编的学习、开发、教学等工作，gxemul是个很好的选择。已经有人能够在 gxemul 上模拟龙芯了，见[这里](http://www.lemote.com/bbs/viewthread.php?tid=1957)。

That's all.