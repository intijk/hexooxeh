---
title: openSUSE包管理
id: 1187
categories:
  - Linux
  - 笔记
date: 2013-05-16 17:03:24
tags:
---

zypper update和refresh的区别，update等价于apt-get的upgrade，是进行系统升级的。refresh等价于apt-get的update，是刷新源数据的。

列出所有的源

    zypper repos 或 zypper lr
    `</pre>

    删除某个源

    <pre>`zypper removerepo reponame 或 zypper rr reponame
    `</pre>

    添加一个源

    <pre>`zypper addrepo repoURL reponame 或 zypper ar repoURL reponame
    `</pre>

    查询文件属于哪个包

    <pre>`rpm -qf filename
    `</pre>

    查询包里有哪些文件

    <pre>`rpm -ql packagename
    `</pre>

    查询包的详细描述

    <pre>`rpm -qi packagename
    