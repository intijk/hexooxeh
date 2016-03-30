---
title: '云计算学习: EC2 Eucalyptus MapReduce Hadoop SaaS PaaS IaaS等名词之间的关系和区别'
tags:
  - EC2
  - Elastic Compute Cloud
  - Eucalyptus
  - Hadoop
  - HPC
  - IaaS
  - MapReduce
  - PaaS
  - SaaS
  - 云计算
  - 区别
id: 530
categories:
  - HPC
date: 2011-07-21 07:25:33
---

什么是云计算？从最早的概念炒作到现在，云计算已经出现了一些切实可行的计算平台方案。
当前的云计算服务分为三种方式：
**Software as a Service（SaaS)**
软件即服务，大家使用的WebQQ就是一个例子，Gmail也是个例子，服务商提供软件给用户使用，和传统的C/S架构一致。
**Platform as a Service (PaaS)**
平台即服务，典型的代表有Google App Engine,提供一套完整的API供使用，适合技术人员。MapReduce就是这一层上的技术，Hadoop是一个开源的MapReduce实现，它实现了一个分布式文件系统HDFS并提供了一组函数，开发者可以调用之形成自己的分布式应用。
**Infrastructure as a Service(IaaS)**
架构即服务，最典型的就是亚马逊的Amazon Elastic Compute Cloud (Amazon EC2)， EC2可以让你自己选择CPU类型，磁盘大小，操作系统种类，生成一个虚拟机，用户用远程桌面登录即可使用，就像自己花钱买了一台电脑一样,完全有自己操控，想装什么软件装什么软件，想进行什么计算进行什么计算，按CPU的类型和使用的时间收费。对应EC2，有一个开源实现，是加利福尼亚大学做的Eucalyptus。

如果公司或学术机构有一个大型集群，想要建立私有云提供计算能力和存储能力，就可以这样做。首先用Eucalyptus建立一个私有云，以IaaS的方式把若干节点租用给用户。系统在私有云的若干节点上架设Hadoop应用，提供MapReduce模型的计算API，然后可以在此基础上部署各种应用，实现SaaS.

总之，三种服务类型是有一个层次架构的，从下到上依次为IaaS->PaaS->SaaS。

这里只是一个直观上的介绍，更详细的介绍推荐大家看看这篇文章：
[http://hi.baidu.com/www100/blog/item/81ff10172dd7b00f4a90a703.html](http://hi.baidu.com/www100/blog/item/81ff10172dd7b00f4a90a703.html)