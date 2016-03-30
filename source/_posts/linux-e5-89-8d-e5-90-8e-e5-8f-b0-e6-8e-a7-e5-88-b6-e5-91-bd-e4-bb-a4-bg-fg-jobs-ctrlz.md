---
title: 'linux 前后台控制命令 bg fg jobs Ctrl+Z &'
tags:
  - bg
  - Ctrl+z
  - fg
  - jobs
  - 前后台
id: 504
categories:
  - 其它
date: 2011-07-15 10:56:43
---

如果有一个任务执行时间很长，你不想让它占用你的交互shell，可以把它放到后台去，只需在命令后加 & 号比如：
[plain]
solo@solo:~$ sleep 100 &amp;
[1] 14128
[/plain]
如果执行了一下，发现这个进程死皮赖脸霸住前台执行好久，可以Ctrl+z把它放到后台
[plain]
solo@solo:~$ sleep 100 
^Z
[1]+  Stopped                 sleep 100
[/plain]
注意，这时任务是暂停执行（Stopped），然后放到了后台，如果要让它在后台运行就要在后台启动它，可以用bg命令：
bg %N ;N是后台编号，就是上面方括号里的编号,不带参数就对最后一个任务生效

可以用jobs命令查看后台执行的所有任务：
[plain]
solo@solo:~$ jobs
[1]+  Running                 sleep 100 &amp;
[/plain]
如果需要把某个任务调回到前台，就用fg命令：
fg %N ;N是后台编号，不带参数时，就把最后一个任务拉回前台
[plain]
[1]+  Running                 sleep 100 &amp;
solo@solo:~$ fg
sleep 100
[/plain]
总结如下：
1\. 把任务放到后台用 & 和 Ctrl+z
2\. 让后台任务从停止状态转为运行状态用 bg %N
3\. 把后台任务调回到前台用 fg %N
4\. 查看所有任务用jobs