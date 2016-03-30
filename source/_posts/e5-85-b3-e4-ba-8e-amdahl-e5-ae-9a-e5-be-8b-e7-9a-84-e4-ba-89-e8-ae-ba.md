---
title: 关于 Amdahl 定律的争论
id: 424
categories:
  - 其它
date: 2011-03-17 07:49:09
tags:
---

当然，我这是旧事重提了，Amdahl 一辈子也就发了[9篇论文](http://academic.research.microsoft.com/Author/10559477)，但是它的
《Validity of the single processor approach to achieving large scale computing capabilities》（1967）引出了叫做 Amdahl 定理的式子，此式子在论文中并没有出现，是后来的人按他论文的观点给总结出来的，散见于各大系统结构教材：
Amdahl 定律：
[latex]r=(s+t*p)/(s+t)[/latex]
其中 s 是系统不可优化的部分， t 是可优化的部分，p 是优化系数，r 是加速比。

它这篇文章其实想说：预言并行是未来计算机体系的神棍们，你们放弃吧，单处理器的单体能力提高才是王道！

若干年后（1988）John L. Gustafson 在[《Reevaluating Amdahl's Law》](http://www.scl.ameslab.gov/Publications/Gus/AmdahlsLaw/Amdahls.html)
发出了反击，说，如果系统的线性部分（即存在依赖的顺序执行部分）很小的话，并行是很有搞头的！

[latex]Speedup=(s+p)/(s+p/N)=1/(s+p/N)[/latex]

其中令 [latex]s+p=1[latex][/latex] 做了归一化处理，说，在s很小的时候，这个Speedup的增长速度是相当惊人的。
当然了，John L. Gustafson，还是有料的，他们搞了1024 个处理器的计算机，做了一个带屏蔽的波形表面模拟程序，得到了很好的加速比。

其实这就是个小函数在不同曲线段的争论，我觉得搞科研的有时候对于些简单东西用了 overpower 的精力去论证了。

旧事重提，done.