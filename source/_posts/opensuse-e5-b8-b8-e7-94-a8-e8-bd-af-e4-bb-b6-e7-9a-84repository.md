---
title: openSUSE常用软件的repository
tags:
  - openSUSE
  - source
  - 源
id: 1286
categories:
  - openSUSE
date: 2013-06-01 06:15:58
---

<!--:zh-->openSUSE的很多软件都没有进默认的源, 比如axel, dstat 等, 但其实他们都有官方的打包人员在维护。这个帖子列出我遇到需要用的软件的源，以后逐渐添加，以飨后人。

更加通用的做法是到 [https://build.opensuse.org/search](https://build.opensuse.org/search) 进行搜索，开头不是home的源基本都是可信的。

# 一、预备信息

## 源地址说明

[http://download.opensuse.org/repositories/](http://download.opensuse.org/repositories/) 是源的公共前缀，源的名字是 user:type 形式，而系统版本如果是openSUSE_12.3, 那么组成的源地址就是：

    [http://download.opensuse.org/repositories/user:/type/openSUSE_12.3](http://download.opensuse.org/repositories/user:/type/openSUSE_12.3)
    `</pre>
    user:type 形式

    opensuse 里有很多public project，这些项目是各个不同的组织和类别的包，但进入标准源不太合适。如Cloud:OpenStack 它是云计算(Cloud)类别的项目，具体的项目名称是OpenStack,再如home:intijk ，所有的用户自己的playground都是home:开头的，intijk是用户名。

    以下就只写源的名字了，对应系统系统版本的源是否存在，需要点开
    <pre>`[http://download.opensuse.org/repositories/user:/type/](http://download.opensuse.org/repositories/user:/type/)
    `</pre>
    自己确认一下。

    ## 添加源的方法

    <pre>`sudo zypper ar [http://download.opensuse.org/repositories/user:/type/openSUSE_12.3](http://download.opensuse.org/repositories/user:/type/openSUSE_12.3) user-type
    `</pre>

    # 二、源列表

    **wget**
    <pre>`network:utilities
    `</pre>
    **dstat**
    <pre>`server:monitoring`</pre><!--:--><!--:en-->openSUSE的很多软件都没有进默认的源, 比如axel, dstat 等, 但其实他们都有官方的打包人员在维护。这个帖子列出我遇到需要用的软件的源，以后逐渐添加，以飨后人。

    更加通用的做法是到 [https://build.opensuse.org/search](https://build.opensuse.org/search) 进行搜索，开头不是home的源基本都是可信的。

    # 一、预备信息

    ## 源地址说明

    [http://download.opensuse.org/repositories/](http://download.opensuse.org/repositories/) 是源的公共前缀，源的名字是 user:type 形式，而系统版本如果是openSUSE_12.3, 那么组成的源地址就是：
    <pre>`[http://download.opensuse.org/repositories/user:/type/openSUSE_12.3](http://download.opensuse.org/repositories/user:/type/openSUSE_12.3)
    `</pre>
    user:type 源形式

    opensuse 里有很多public project，这些项目是各个不同的组织和类别的包，但进入标准源不太合适。如Cloud:OpenStack 它是云计算(Cloud)类别的项目，具体的项目名称是OpenStack,再如home:intijk ，所有的用户自己的playground都是home:开头的，intijk是用户名。

    以下就只写源的名字了，对应系统系统版本的源是否存在，需要点开
    <pre>`[http://download.opensuse.org/repositories/user:/type/](http://download.opensuse.org/repositories/user:/type/)
    `</pre>
    自己确认一下。

    ## 添加源的方法

    <pre>`sudo zypper ar [http://download.opensuse.org/repositories/user:/type/openSUSE_12.3](http://download.opensuse.org/repositories/user:/type/openSUSE_12.3) user-type
    `</pre>

    # 二、源列表

    **wget**
    <pre>`network:utilities
    `</pre>
    **dstat**
    <pre>`server:monitoring
<!--:-->