---
title: Python 的进度
id: 37
date: 2010-07-05 19:44:49
tags:
---

Lemire说python是一晚上就学习完的语言, 我看了一半.
一些感触:
          Python 的变量也是可以不用定义直接使用的.
          Python 的动态类型很动态.
          Python 有个for 之后的else, 如果break以后就不执行了, 写过C中一些循环结束判断的应该很清楚这是一种很大的便利.以往自己的做法都是出for之后判断 if(i == N) , 这个else的设计很人性, 考虑很周到.
          Python 用func.__doc__来实现一个函数的文档的编写.
          Python 对于字符串内容的把握很优秀, 一个 r"I need a geek string \n \\ \* \0xaa" , 就能得到想要的无转义字符串, 用 u"中文Unicode字符串" , 就能得到Unicode的字符串.

Python号称是一个完整的, 可以方便操纵包括数据库在内各种模块的语言, 所以我学习一下.