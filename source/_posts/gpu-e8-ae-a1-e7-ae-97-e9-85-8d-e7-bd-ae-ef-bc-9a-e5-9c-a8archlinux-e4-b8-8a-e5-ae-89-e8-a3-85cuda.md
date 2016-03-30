---
title: 'NVIDIA GPU计算配置：在Archlinux上安装cuda OpenCL '
tags:
  - arch
  - archlinux
  - cuda
  - gpu
  - linux
  - nvidia
  - opencl
  - 安装
  - 显卡
  - 配置
  - 驱动
id: 513
categories:
  - HPC
date: 2011-07-16 14:42:39
---

上次看到lv某人使用yaourt，发现无比强大，我yaourt之后，发现cuda在里面，觉得有搞头，于是在一台xw9000的工作站上安装了cuda。虽然这台机器cpu有点多，内存也稍微有点大，但我们都看不上，而主要是用其中的显卡进行GPU计算。闲言少叙，下面是正式过程。
NVIDIA的显卡上，cuda装好opencl就自然有了，所以装cuda=装opencl，回头AMD的怎么装我会另行发贴。

**1\. 安装NVIDIA的闭源驱动**
假设你手头已经有一台archlinux，那么你首先要做的就是关闭所有的X，进入一个字符界面shell，安装一个NVIDIA的闭源驱动。可能会提示你和现有的东西冲突，最可能的就是libgl，你可能需要删除掉它：
pacman -R libgl --cascade
发现删了很多东西，不用担心，回头都能再装回来不影响使用。
安装所需要的kernel头文件
pacman -S kernel26-headers
闭源驱动要装哪个版本呢，要和安装的cudatoolkit使用的内核版本一致，比如cudatoolkit_4.0.17-1对应的驱动是NVIDIA-Linux-x86_64-275.09.07.run， 那么就下载这个驱动，安装之。
wget http://**NVIDIA-Linux-x86_64-275.09.07.run
chmod +x NVIDIA-Linux-x86_64-275.09.07.run
./NVIDIA-Linux-x86_64-275.09.07.run

中间可能会提示你已经装了开源驱动nouveu，cuda的驱动说我们可以在modprobe里面动点手脚干掉开源驱动，你选是就可以了，会看见编译过程绿色进度条闪动等，待完成即可，如果问你装不装它带的gl，你应该选是。
**2\. 安装cudatoolkit**
假设你已经安装了yaourt，那么执行：
yaourt cuda
会发现有许多可以选，选择cuda-toolkit或者cuda，总之名字里带的版本号对的那个就可以，比如我装的这个版本号就是4.0.17-1，有好几个候选包，我选了第15个，因为那个投票的人最多。
然后就开始了自动下载的过程，主要有这两个文件：
cudatoolkit_4.0.17_linux_64_fedora13.run  200M（很显然，这个arch版是从fedora版搞过来的）
cudatools_4.0.17_linux_64.run   3M
你可以用别的软件下载了以后放在 /tmp/yaourt-tmp-username/aur-cuda-toolkit/下面，删除掉这个文件夹下的part文件，然后重新安装来加快下载速度，或者你网络还行，就等它下载完。
后面会问你是否编辑配置文件，一律no过去，是否继续的选项，一路yes过去。
有两个步骤比较耗时，一个是符号处理，一个是压缩，请耐心等待，会自动安装完成。

**3\. 测试cuda是否安装成功**
在/usr/share/cuda-toolkit/cupti下有三个程序，都有Makefile，只需稍作改动就能让它跑起来。
首先对include动动手脚，进入/usr/include/cuda/cupti，把所有文件及目录复制一份到上层目录，即/usr/include/cuda
修改Makefile中的第一行为：
INCLUDES=-I/usr/include/cuda
此时make，它提示gcc版本4.5以上的不支持，打开/usr/include/cuda/host_config.h文件，找到这行：
#if __GNUC__ > 4 || (__GNUC__ == 4 && __GNUC_MINOR__ > 4)
修改成
#if __GNUC__ > 4 || (__GNUC__ == 4 && __GNUC_MINOR__ > 6)
这样它就不认为4.6的gcc版本过高了，再make就过了
执行event_sampling这个程序结果如下：
$ ./event_sampling 0
Usage: ./event_sampling [device_num] [event_name]
CUDA Device Number: 0
CUDA Device Name: Quadro FX 4800
Creating sampling thread
instructions: 0
instructions: 2299449
instructions: 4563349
$ ./event_sampling 1
Usage: ./event_sampling [device_num] [event_name]
CUDA Device Number: 1
CUDA Device Name: Quadro FX 4800
Creating sampling thread
instructions: 0
instructions: 4821668
instructions: 3798804
比如我有两块显卡，分别是0号和1号，就可以
$ ./event_sampling 编号
测下正常不，最起码能得到设备名这个基础信息，证明cuda调用成功。
**4.测试opencl是否安装成功**
那么下面我们测试opengl的程序如何，这里是一个oepncl最简单的程序，如果能编译通过，说明你已经配置好了opencl
[c]
int main()
{
	cl_int status=0;
	size_t deviceListSize;
	cl_uint	numPlatforms;
	cl_platform_id platfomr=NULL;
	status=clGetPlatformIDs(0,NULL,&amp;numPlatforms);
	printf(&quot;status=%d\n&quot;,status);
	return 0;	
}
[/c]
编译CUDA和OpenCL的编译器名称都叫nvcc，可以用以下命令编译,假设你将以上代码存储为 test.c ,那么用以下命令编译：
nvcc test.c -lOpenCL
编译通过，运行得到：
status=0
说明OpenCL配置成功

后续的测试和程序我会发上来，希望对cuda和opencl有兴趣的同仁多多讨论。