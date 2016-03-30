---
title: cron
tags:
  - cron
  - crontab
id: 166
categories:
  - 其它
date: 2010-12-10 00:00:36
---

在希腊语中, Chronos 指的是时间,  在Linux下cron这个命令用来安排计划任务。

系统全局的cron设置在/etc/crontab, 配置方法就是以root身份编辑这个文件, 个人的配置文件放在/var/spool/cron/crontabs/yourname

普通用户可以通过 crontab -e 编辑自己的计划任务

计划分为如下几列
minute hour day month week user(*) command
每个任务用一行表示。
其中user这一项在/etc/crontab的全局里有，个人的没有。这个想下也懂，只有root用户才能用别人的身份执行计划任务，而普通用户就只能执行自己的计划任务。
如下一个cron
17 3 23 5 * root echo 'a prime time'
会在每年的五月23日3点17分以root用户身份打印一行 a prime time。 当然了， 计划任务是在后台执行的， 你看不到这个shell打出来的东西。星号处代表了任意匹配， 上面这个例子里就是星期几都可以。
如果你想每小时的第15分钟打印一个
15 * * * * root echo 'every hour a one'
如果你想每分钟打印一个
* * * * * root echo 'every minute a one'
普通用户用crontab -e 得到的和上面的唯一区别就是没有　root　这个字段。

cron 命令在服务器配置中是常用的命令，它经常用来在夜半三更无人访问的时候在后台帮你默默的备份数据。