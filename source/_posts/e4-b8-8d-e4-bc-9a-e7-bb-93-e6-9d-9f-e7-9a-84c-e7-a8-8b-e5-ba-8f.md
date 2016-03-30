---
title: 不会结束的C程序
tags:
  - c
  - 汇编
id: 102
categories:
  - 其它
date: 2010-10-30 03:10:18
---

如果你这样这样：

[cpp]
#include &lt;stdio.h&gt;
int *p;
void add(){
	int i,j=0;
	for(i=0;i&amp;lt;10;i++){
		(*p)--;
		j++;
	}
}
void init(){
	int a;
	p=&amp;amp;a;
}
int main()
{
	init();
	add();
	return 0;
}
[/cpp]

然后用gcc编译运行一下：

[bash]gcc test.c
./a.out
[/bash]

然后你会发现，这个程序结束不了。

这就是某血汗公司的面试题了，为什么程序不会结束呢？

标准答案如下：函数调用的帧结构是固定的，当调用init时，全局指针p指向了一个局部变量a的地址。尽管程序退出，a已经完成了它的历史使命，但是全局变量依然坚守着指向栈中这个位置，当你再调用add时，新分配的变量i就被放在了这个地址里面，而p此时实际指向了i的地址，所以在add函数中，每次i++都和(*p)--相互抵消，i永远到不了10，程序永远不会结束。

然而我要告诉你，出题的人都是想当然的，那就是耍小聪明的时候也会bug的。

如果你这样这样：

[bash]gcc test.c -O1
./a.out
[/bash]

诶？它可以结束，这是为什么呢？

为什么进行O1优化之后程序顺利结束了呢？(为什么我不用O2优化呢，O2以后连init和add都浮云了，主函数知道它们没用，连调都不给调用)

我们可以把编译的汇编代码搞出来看看，只要这样这样：

[bash]gcc test.c -O1 -S
vim test.s
[/bash]

你会看到这样几行，也就是add函数的汇编代码：

[c]add:
	pushl	%ebp
	movl	%esp, %ebp
	movl	$0, %eax
.L2:
	movl	p, %edx
	subl	$1, (%edx)
	addl	$1, %eax
	cmpl	$10, %eax
	jne	.L2
	popl	%ebp
	ret
[/c]

你看到了吧，movl	$0, %eax，是将0放入寄存器%eax里， addl	$1, %eax     和   cmpl	$10, %eax   就是我们c代码的  i++  和  i&lt;10  ,整个过程i变量都一直存储在寄存器%eax里，和内存什么关系都没有，所以 (*p)-- 就不能再修改i的值了。程序顺利结束。

Peter van der Linden 在 Expert C programming 里说：
如果有哪本书声称调用函数的时候参数是按从右到左的次序压入堆栈,撕下那一页烧掉；如果你有那样的一个编译器，把编译器的那几行源代码删掉。参数在传递过程中首先尽可能的存放到寄存器中。

同样，为了加快速度，程序优化的时候会将内存变量代之以寄存器变量。

我相信，对于这个题目，以上解答是比标准答案更好的一个回答。