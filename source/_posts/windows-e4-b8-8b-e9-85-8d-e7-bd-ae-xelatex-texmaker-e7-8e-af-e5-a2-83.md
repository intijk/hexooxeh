---
title: Windows 下配置 xeLaTeX + Texmaker 环境
tags:
  - Texmaker
  - W32TEX
  - windows
  - xeLaTex
id: 295
categories:
  - 其它
date: 2011-01-31 07:30:10
---

如果你需要写论文，选用哪种LaTeX环境好呢？ （什么？你说word？好了，你可以关闭本页面了）推荐 xeLaTeX ，中文名叫“邪恶”LaTeX. （什么？你用的 Linux 系统？ 恩，那么这篇文章也不是给你写的，但是可以帮助你在类似“学校机房”这种副本里刷怪练野）。
官方没有相应的二进制安装包，用 TeXLive 这种动辄上几百M的神器也太考验我们的硬盘了，为了解救 Windows 的芸芸众 Reseacher 和广大 Paper translater， 日本友人Akira Kakuto 搞出了 W32TeX 这个发行包，它提供最新编译包括 XeTeX 在内的多种环境。
我们可以到 [W32TeX的主页](http://w32tex.org/)上进行膜拜。

作者制作了一个小巧的安装包来安装所有的软件：
http://w32tex.org/current/texinst2010.zip
将它下载下来，解压在一个目录中，如：D:\texinst2010
设置系统的环境变量，我的电脑->右键->属性->高级->环境变量->选中上面的path->编辑.
如果没有path这个变量就新建，并且输入刚才的解压目录（此处是D:\texinst2010).如果已经有这个变量就在后面添加此路径和别的路径之间用分号 ;  隔开。
（注：每次修改环境变量之后，必须重新启动命令提示符使新的修改生效）。
下载安装包，为简便，将他们放在一个统一的目录下，如 D:\temp 我将所有的安装包罗列如下：
http://w32tex.org/current/latex.tar.xz
http://w32tex.org/current/mftools.tar.xz
http://w32tex.org/current/platex.tar.xz
http://w32tex.org/current/ptex-3.1.11-w32.tar.xz
http://w32tex.org/current/pdftex-w32.tar.xz
http://w32tex.org/current/web2c-2010-lib.tar.xz
http://w32tex.org/current/web2c-2010-w32.tar.xz
http://w32tex.org/current/xetex-w32.tar.xz
以上包为必选，以下包为可选。
http://w32tex.org/current/dvipdfm-w32.tar.xz
http://w32tex.org/current/dvipsk-w32.tar.xz
http://w32tex.org/current/jtex-2.2-w32.tar.xz
http://w32tex.org/current/ltxpkgs.tar.xz
http://w32tex.org/current/luatex-w32.tar.xz
http://w32tex.org/current/makeindex-w32.tar.xz
http://w32tex.org/current/manual.tar.xz
http://w32tex.org/current/oldinputs.tar.xz
http://w32tex.org/current/t1fonts.tar.xz
http://w32tex.org/current/timesnew.tar.xz
http://w32tex.org/current/txpx-pazofonts.tar.xz
http://w32tex.org/current/vf-a2bk.tar.xz
http://w32tex.org/current/xindy-w32.tar.xz

全部下完以后也有 142MB 了，不过比 TeXLive 的 600MB 要小多了。
在命令提示符下切到一个合适的安装目录下,比如 D:\W32TeX ,执行安装命令,参数是以上这许多安装文件的位置，本文中是（D:\temp)
D:\W32TeX> texinst2010 D:\temp
然后开始吐屏，过一会就OK了，最后会提示要你创建环境变量，很简单，还是按刚才的方式：
设置系统的环境变量，我的电脑->右键->属性->高级->环境变量->选中上面的path->编辑.
加入D:\W32TeX\bin 到环境变量中，多个路径用分号 ; 隔开。

然后执行命令：
fc-cache -v

这样就大功告成了！在命令提示符下（不要忘了重新启动一下）输入
xelatex
我们会看到
This is xeTeX, ....  等字样。

环境配置好了，我们也许需要一个好的编辑器，当然，如果你用记事本，也是可以写 TeX 文档的，但是建议你使用一个集成的文档环境。 CTeX 论坛上长期提供的编辑工具 WinEdt 是个共享软件，31天试用期，真不明白他们怎么还捆绑这个东东。这里教大家怎么用更漂亮更华丽更加支持 UTF-8 的 Texmaker。

首先，到[这里](http://www.xm1math.net/texmaker/download.html)下载最新的安装包，当前的最新版本是2.2.1，安装好之后，设置相关的参数，为了简便，我只设置两个。

打开软件之后，选项->配置Texmaker->左侧大图标“快速构建”->选中单选项“用户”
输入以下命令：
xelatex -interaction=nonstopmode %.tex
之后你就可以写 xeLaTeX 文档并且用快捷键 F1 自动生成 pdf 文件了，点击F7可以快速浏览文件。
如果你需要改用其它pdf浏览器来查看文档，比如我用Foxit，那么在
选项->配置Texmaker->pdf查看器 里选中External Viewer 并且在后面填入相应的路径，我这里是
"D:\Program Files\Foxit\App\FoxitReader\Foxit Reader.exe" %.pdf
如此一来，就可以用快捷键 F7 查看你刚刚生成的 pdf 文档了。
Texmaker 需要配置成UTF-8的编码才能正确的显示，在
选项->配置Texmaker->左侧大图标“编辑器”->编辑器字体编码->UTF-8

OK，大功告成，让我们上手编辑第一份文档吧，有图有真相：
![](http://intijk.com/wp-content/uploads/2011/01/texmaker.png "texmaker")</a>