---
title: mysql如何补全
id: 820
categories:
  - Linux
date: 2011-11-09 06:52:26
tags:
---

**1.**
在配置文件里/etc/mysql/my.cnf 
#no-auto-rehash 注掉
并且加上
auto-rehash

**2\. **
（1）
$mysql --auto-rehash
或
mysql>\#
（2）
然后use某个数据库（本来应该没有这一步，但实验发现得这样），如
use mysql
就可以了。
（3）使用大写命令，因为SQL标准要求必须大写，小写没有补全。
mysql>SEL<tab>
mysql>SELECT