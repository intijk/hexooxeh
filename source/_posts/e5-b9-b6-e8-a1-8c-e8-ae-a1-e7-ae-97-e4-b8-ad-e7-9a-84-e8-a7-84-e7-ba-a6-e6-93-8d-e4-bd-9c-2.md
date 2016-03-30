---
title: 并行计算中的规约操作
tags:
  - reduce
  - 并行
  - 矢量相加
  - 规约
id: 662
categories:
  - HPC
date: 2011-09-15 02:58:15
---

以数组加为例, 有16个整数需要进行相加,那么在单核的CPU上,需要进行15次操作,而使用并行的规约操作,则可以将时间复杂度降为log(n)即4次.

其过程如下:

Step 1:

[![](http://intijk.com/wp-content/uploads/2011/09/t12.png "t1")](http://intijk.com/?attachment_id=663)

Step 2:

[![](http://intijk.com/wp-content/uploads/2011/09/t22.png "t2")](http://intijk.com/?attachment_id=667)

Step 3:

[![](http://intijk.com/wp-content/uploads/2011/09/t32.png "t3")](http://intijk.com/?attachment_id=666)

Step 4:

[![](http://intijk.com/wp-content/uploads/2011/09/t42.png "t4")](http://intijk.com/?attachment_id=665)

Result:

[![](http://intijk.com/wp-content/uploads/2011/09/t53.png "t5")](http://intijk.com/?attachment_id=670)

规约,就是将线性O(n)的过程,简化为一个log(n)的过程,除了矩阵求和之外,也可以处理求矢量分量的最大值最小值问题,是并行计算中最常用的一个操作.