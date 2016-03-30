---
title: MPI 死锁实验
tags:
  - mpi
  - 死锁
id: 699
categories:
  - HPC
date: 2011-09-15 11:01:06
---

利用下面这样一段代码, MPI貌似就会死锁. 两个进程,分别向对方发送一个数组,并且接收对方传过来的数组.但由于两者都是先发送,所以一开始没有成对的Send/Recv.

[c gutter="true"]
#include &lt;mpi.h&gt;
#include &lt;stdio.h&gt;
int main(int argc,char *argv[])
{
	int a[10],rank;
	int b[10],i;
	MPI_Status status;
	for(i=0;i&lt;10;i++) a[i]=i;
	MPI_Init(&amp;argc, &amp;argv);
	MPI_Comm_rank(MPI_COMM_WORLD,&amp;rank);
	if(rank==0){
		MPI_Send(a,10,MPI_INT,1,1,MPI_COMM_WORLD);
		MPI_Recv(b,10,MPI_INT,1,2,MPI_COMM_WORLD,&amp;status);
	}else if(rank==1){
		MPI_Send(a,10,MPI_INT,0,2,MPI_COMM_WORLD);
		MPI_Recv(b,10,MPI_INT,0,1,MPI_COMM_WORLD,&amp;status);
	}
	printf(&quot;b is:\n&quot;);
	for(i=0;i&lt;10;i++){
		printf(&quot;%d &quot;,b[i]);
	}
	puts(&quot;&quot;);
	MPI_Finalize();
	return 0;
}
[/c]
0号进程在12行向1号进程发送了10个整数, 由1号进程在16行接收.
1号进程在15行向0号进程发送了10个整数, 由0号进程在13行接收.
可以看到, 虽然有相应的接收代码,如果在12行和第15行,两者都在等待对方接收,那么这两个进程就会相互等待,无法继续.但实际运行的结果是:

[bash]
$ mpirun -np 2 a.out
b is:
0 1 2 3 4 5 6 7 8 9 
b is:
0 1 2 3 4 5 6 7 8 9 
[/bash]
这说明, MPI_Send函数并没有挂起了之后等待对方接收, 而是把数据暂存在了一块buffer中,然后执行了后续的代码,从而避免了死锁.

从标准上并没有规定到底使用还是不使用buffer,但大多数实现都采用了Buffer并且使用非阻塞的MPI_Send. 这就是为什么这个死锁实验中并没有造成死锁,但是为了适应更广泛的运行环境,代码还是写成safe的比较好,所谓safe模式,即不做非阻塞Send这样的假设,代码中有显式的在逻辑顺序上出现的一个Send和一个Recv. 以上代码的safe版本如下:
[c gutter="true"]
#include &lt;mpi.h&gt;
#include &lt;stdio.h&gt;
int main(int argc,char *argv[])
{
	int a[10],rank;
	int b[10],i;
	MPI_Status status;
	for(i=0;i&lt;10;i++) a[i]=i;
	MPI_Init(&amp;argc, &amp;argv);
	MPI_Comm_rank(MPI_COMM_WORLD,&amp;rank);
	if(rank==0){
		MPI_Send(a,10,MPI_INT,1,1,MPI_COMM_WORLD);
		MPI_Recv(b,10,MPI_INT,1,2,MPI_COMM_WORLD,&amp;status);
	}else if(rank==1){
		MPI_Recv(b,10,MPI_INT,0,1,MPI_COMM_WORLD,&amp;status);
		MPI_Send(a,10,MPI_INT,0,2,MPI_COMM_WORLD);
	}
	printf(&quot;b is:\n&quot;);
	for(i=0;i&lt;10;i++){
		printf(&quot;%d &quot;,b[i]);
	}
	puts(&quot;&quot;);
	MPI_Finalize();
	return 0;
}
[/c]