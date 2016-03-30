---
title: socket 上的I/O
tags:
  - a+
  - csapp
  - fclose
  - fdopen
  - fflush
  - io
  - linux
  - rio
  - socket
id: 847
categories:
  - Linux
date: 2012-01-13 10:40:31
---

socket和一般的I/O可不一样，它像是一个消耗型的管道。

如果你在某文件中，写入，并且读出来，对一般的文件，没有什么问题，但是对于socket，这个就不成立，写入的数据是传递到了socket的另一头，所以你这个进程是读不出来的。

如何"优雅的"读写套接字呢？那当然是用标准I/O函数了。用fdopen可以把文件描述符打开为一个文件指针，然后就可以用fgets 和fputs读取和写入。

但在csapp（《深入理解操作系统》）中的11.9节对这个问题有讨论，提议不要用标准库函数，所以它实现了一套叫做rio（robust I/O的机制），为什么不要用标准库呢？

因为有限制，如果不fflush，那么在写入之后读，或者读之后写，都是不可以的，所以得显示的fflush()，所以书中给出的方案是：

rfp = fdopen(confd, "r");

wfp = fdopen(confd, "w");

即打开两个文件指针，一个用来读，一个用来写。

那么，这样的问题就是，当关闭的时候，你需要：

fclose(rfp);

fclose(wfp);

而这两个文件指针指向的是同一个文件描述符，这样就会造成两次关闭，就会出bug。

然而我发现了比书上更好的方法，就是用a+模式。

在描述符转文件指针的时候：

fp = fdopen(confd, "a+);

a是append的意思，  用这种模式打开的时候，读就从头读，自动移动文件指针，写则永远从文件尾端写入。

而关闭过程，也简洁优雅：

fclose(fp);

所以，用'a+'模式可以完美的解决socket的读写问题。推荐。

&nbsp;