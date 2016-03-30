---
title: svn 的编译
tags:
  - svn
id: 142
categories:
  - 其它
date: 2010-11-17 07:12:25
---

今日在Debian上更新svn, 出现了以下问题：
1\. apr 包下载了提示找不到 configure ，进入此目录进行观察，发现有 buildconf 存在，于是 apt-get install autoconf 并运行 ./buildconf 解决. 此处便可顺利 make & make install (注：默认安装位置在/usr/local/bin/svn 所以如果你 make install 以后发现依然不给力，可能是路径的问题导致旧版本没有被覆盖，可以手动显式调用 /usr/local/bin/svn
2\. Unrecognized URL scheme "http://不和谐不和谐不和谐/trunk" 导致这个问题的原因是没有 ra_neon 这个库，解决的方法是外部安装一个 apt-get install libneon27 , 重新 make & make install 问题解决。
运行 [bash]/usr/local/bin/svn --version[/bash] 显示有
[plain]ra_neon:Module for accessing a repository via WebDAV protocol using Neon
- handles 'http' scheme
- handles 'https' scheme[/plain]
表示成功。