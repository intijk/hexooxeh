---
title: 配置GDB使支持STL
tags:
  - gdb
  - stl
id: 83
categories:
  - 其它
date: 2010-10-04 12:12:32
---

<pre name="code" class="c++">
#include
using namespace std;
int main()
{
vector a;
a.push_back(1);
return 0;
}</pre>
在GDB中，如果直接访问一个vector，你会看到如下的信息：
<pre name="code" class="c++">
(gdb)p a
$1 = { &gt;&gt; = {_M_impl = {&gt; = {&lt;__gnu_cxx::new_allocator&gt; = {}, },
 _M_start = 0x804c008, _M_finish = 0x804c00c,_M_end_of_storage = 0x804c00c}}, }
</pre>
这样的信息对于调试是没有益处的，我们需要查看的是vector所存储的变量的信息。
最简单的方法如下：（来自[这里](http://bbs.chinaunix.net/viewthread.php?tid=878006))
<pre name="code" class="c++">define print_vector
print $arg0._M_impl._M_start[$arg1]
end</pre>
这样，如果你要访问a中的元素，只需：
<pre name="code" class="c++">(gdb)print_vector a 0</pre>
但是每次需要手动敲入这个定义，显得繁琐，我们将其保存在某个文件中，如gdbinit.gdb，在启动gdb时，用以下命令：
<pre name="code" class="c++">gdb a.out -x gdbinit.gdb</pre>

以上方法用来简单解决查看vector的信息内容，面对复杂的stl容器与对象，该如何查看其信息呢？
目前有个关于gdb的增强扩展包，可以格式打印stl的内容，其详细介绍见[这里](http://sourceware.org/gdb/wiki/STLSupport)

1.  Python libstdc++ printers
2.  gdb-stl-views
3.  gdb++
        下面简要介绍下Python libstdc++ printers

这是一个python的扩展包，所以你得先装好python。然后装好svn，若你使用的是ubutnu，那么只需
           <pre name="code" class="c++">sudo apt-get install python svn</pre>
           之后进入一个合适的目录（如~/.gdb/，执行：
           <pre name="code" class="c++">svn co svn://gcc.gnu.org/svn/gcc/trunk/libstdc++-v3/python</pre>
           就安装好了这个扩展包。
           然后将以下写入~/.gdbinit
          <pre name="code" class="python">
python
import sys
sys.path.insert(0, '/home/yourname/.gdb/python')
from libstdcxx.v6.printers import register_libstdcxx_printers
register_libstdcxx_printers (None)
end</pre>
 注意，上面的/home/yourname,一定要用绝对路径，相对路径会导致python无法找到模块。
在之后的调用中，你就可以使用：
<pre name="code" class="c++">gdb a.out -x ~/.gdbinit</pre>
来每次自动加载这个模块。你也可以将启动gdb的过程写入到你的vim的配置文件中，在编译的时候可以一键启动编译命令，这样就省去了每次敲键盘的苦恼。
       