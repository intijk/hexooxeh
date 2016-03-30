---
title: Makefile 学习笔记
id: 1206
categories:
  - Linux
date: 2013-05-24 23:55:55
tags:
---

终于花了几天时间认真看完了《跟我学makefile》，为了防止自己睡着，一边看一边写了些笔记，整理了下放上来。

## Makefile 变量

    objects = main.o kbd.o command.o
    edit: $(objects)
    `</pre>

    ## Makefile的组成元素

    Makefile里主要包含了五个东西：

1.  显式规则2.  隐晦规则3.  变量定义4.  文件指示
5.  注释

    ## Makefile的文件查找顺序

*   GNUMakefile
*   makefile
*   Makefile

    ## 文件包含

    <pre>`include &lt;filename&gt;;
    -include &lt;filename&gt;; #找不到文件的时候不发生错误  
    `</pre>

    查找目录

1.  当前目录
2.  --include-dir指定的目录
3.  <prefix>/include目录

    ## 环境变量MAKEFILES

    用空格隔开的makefile文件，等价于include，影响所有makefile

    ## 通配符

    <pre>`* ? [...]
    ~ $? 
    $(wildcard *.o)
    `</pre>

    .

    <pre>`objects = *.o
    `</pre>

    objects的值是"*.o"

    <pre>`objects = $(wildcard *.o)
    `</pre>

    objects的值是"a.o b.o c.o"
    wildcard提供的是通配符的展开机制

    ## 文件搜寻

    **VPATH环境变量**
    相对路径文件在Makefile里，只会在当前目录下搜索，如果有VPATH这个环境变量，相对路径文件就会在VPATH指定的路径里搜索。

    VPATH=src:../headers
    冒号隔开，前面的优先。

    vpath关键字:
    1、

    <pre>`vpath &lt;pattern&gt;; &lt;directories&gt;; 
    `</pre>

    为符合模式<pattern>;的文件指定搜索目录<directories>;。 
    2、

    <pre>`vpath &lt;pattern&gt;; 
    `</pre>

    清除符合模式<pattern>;的文件的搜索目录。 
    3、

    <pre>`vpath 
    `</pre>

    清除所有已被设置好了的文件搜索目录。

    vpath %.h ../headers #在当前目录找不到.h文件的时候，去../headers找

    ## 伪目标

    <pre>`clean:
        rm *.o
    `</pre>

    这就是一个伪目标，但是有什么不好呢？如果当前目录有clean这么个文件的话，它就不是伪目标了。所以显式的定义clean是个伪目标，不管有没有这个文件，clean都是伪目标。

    <pre>`.PHONY: clean
    clean:
        rm *.o
    `</pre>

    另一个例子：

    <pre>`all:prog1 prog2 prog3
    .PHONY:all
    `</pre>

    .PHONY:all这种写法搞得好像.PHONY是个目标一样，但是实测.PHONY是个关键字，不会因为在第一行而被识别为默认目标，也不会和同名.PHONY文件产生冲突

    ## 多目标

    $@是一个自动变量

    <pre>`bigoutput littleoutput: text.g
        command $@
    `</pre>

    会被展开为

    <pre>`bigoutput: text.g
        command bigoutput
    littleoutput: text.g
        command littleoutput
    `</pre>

    所以$@是所有目标的集合，不仅仅是个字符串，还会让make以集合元素进行命令展开。

    ## 静态模式

    两个自动变量：
    $@ 表示目标集
    $&lt; 表示依赖集
    以下这个模式，被成为静态模式，它的特点是两个冒号分割开三个字段

    <pre>`&lt;targets ...&gt;;: &lt;target-pattern&gt;;: &lt;prereq-patterns ...&gt;; 
                &lt;commands&gt;; 
                ... 
    `</pre>

    可以这么理解：targets是一个大集合，从这个大集合中，选出满足targe-pattern的，作为目标，目标的依赖是prereq-patterns

    举例:

    <pre>`objects = a.o b.o c.pyc d.pyc
    $(objects):%.o:%.c:
        $(CC) -c $(CFLAGS) $&lt; -o $@
    `</pre>

    会展开成为：

    <pre>`a.o:a.c
        $(CC) -c $(CFLAGS) a.c -o a.o
    b.o:b.c
        $(CC) -c $(CFLAGS) b.c -o b.o
    `</pre>

    当objects变量中有非常多不同类型的目标时，即可分别处理：

    <pre>`objects = a.o b.o c.pyc d.pyc
    $(objects):%.o:%.c:
        $(CC) -c $(CFLAGS) $&lt; -o $@
    $(objects):%.pyc:%.py:
        py2pyc $&lt; $@
    `</pre>

    要注意的是，%&lt;和%@的展开是连动的，一一对应的，设目标有n个，不会先展开$&lt;再战开$@,那样展开式就有n^2个了。因为是连动展开，所以一个$&lt;的元素消耗一个$@的元素，最后产生n个展开式。

    ## 自动生成依赖

    自动生成依赖主要说的针对源文件中来回包含的.h文件，改动一个.h，就要改动好几处Makefile，是很难维护的。所以编译器提供了这个功能，可以自动产生一个源文件的依赖。因为make不负责处理源文件的语法，所以该功能应该，也只能由编译器来提供。

    main.c 内容如下：

    <pre>`#include &lt;stdio.h&gt;
    int main(int argc, char *argv[])
    {
        printf("Hello World\n");
        return 0;
    }
    `</pre>

    .

    <pre>`cc -M main.c
    `</pre>

    产生输出:

    <pre>`main.o: main.c /usr/include/stdio.h /usr/include/features.h \
     /usr/include/stdc-predef.h /usr/include/sys/cdefs.h \
     /usr/include/bits/wordsize.h /usr/include/gnu/stubs.h \
     /usr/include/gnu/stubs-64.h \
     /usr/lib64/gcc/x86_64-suse-linux/4.7/include/stddef.h \
     /usr/include/bits/types.h /usr/include/bits/typesizes.h \
     /usr/include/libio.h /usr/include/_G_config.h /usr/include/wchar.h \
     /usr/lib64/gcc/x86_64-suse-linux/4.7/include/stdarg.h \
     /usr/include/bits/stdio_lim.h /usr/include/bits/sys_errlist.h
    `</pre>

    怎么这么冗长？标准库都进来了？这里的cc是gcc，所以会包含进来标准库，如果不希望用标准库，那么用-MM选项

    <pre>`cc -MM main.c
    `</pre>

    输出

    <pre>`main.o: main.c
    `</pre>

    又有问题出现了，这个依赖关系是动态生成的，makefile也要更新吗？makefile没法更新，更不可能让makefile依赖于源文件。。。

    所以makefile建议编译器把依赖生成到.d文件中，makefile则包含这些.d文件。

    <pre>`sources = a.c b.c
    include $(sources:.c:.d)
    `</pre>

    这里的include一行，包含的是把souces里的元素的.c换成.d之后的文件，即和源文件同名的.d文件

    .d 的生成规则是:

    <pre>`%.d: %.c 
        @set -e; rm -f $@; \ 
        $(CC) -M $(CPPFLAGS) $&lt; &gt;; $@.$$$$; \ 
        sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' &lt; $@.$$$$ &gt;; $@; \ 
        rm -f $@.$$$$ 
    `</pre>

    其思路是产生了一个临时文件$@.$$$$,然后用sed做了一下处理，最后删掉临时文件

    ## 在同一行上写命令

    <pre>`targets: prerequisites; command
    `</pre>

    命令写在同一行上，用分号隔开。

    ## 书写命令

    默认的shell是/bin/sh，也可以给定一个其它shell。

    不显示命令

    <pre>`@command
    `</pre>

    只显示命令，不执行命令

    <pre>`make -n 或 make --just-print
    `</pre>

    全面禁止命令(等价于每行都加了@）

    <pre>`make -s 或 make --silent
    `</pre>

    ## 命令执行

    对cd命令尤其要注意

    <pre>`exec:
        cd /somedir
        pwd
    `</pre>

    pwd 并不会打印出 /somedir, 因为cd命令在一个shell空间执行，pwd在另一个shell空间执行。

    <pre>`exec:
        cd /somedir; pwd
    `</pre>

    此时pwd才会打印出 /somedir， 因为分号隔开的两个命令将会在同一个shell空间执行。

    * * *

    make 会首先从$SHELL 这个环境变量找执行的shell

    * * *

    上一条命令返回值不为0，下一条命令就不会执行，为改变这种行为，在上一条命令前加-即可。

    <pre>`clean:
        -rm -f *.o
        -rm -f *.pyc
    `</pre>

    * * *

    make -i 或 make --ignore-errors 会忽略所有错误，相当于所有命令前加-。
    规则如果以.IGNORE作为目标，该规则中所有命令都相当于加了-
    make -k 或 make --keep-going 如果某规则中的命令出错，那么中止该规则，跳到下一规则继续执行。

    ## 嵌套执行make

    <pre>`subsystem:
        cd subdir &amp;&amp; $(MAKE)
    `</pre>

    等价于：

    <pre>`subsystem:
        $(MAKE) -C subdir
    `</pre>

    ## 变量传递

    变量可用export传递到下级
    $SHELL变量和$MAKEFLAGS变量必然会传递到下级，如果有$MAKEFILES变量存在，也会传递到下级。

    make命令不会往下传递的选项： -C,-f,-h,-o,-W
    make -w 参数会使得make打印目录选项:

    <pre>`Entering directory '/somedir'
    ...
    Leaving directory '/somedir'
    `</pre>

    使用-C选项时，-w自动打开
    -s(--silent)或--no-print-directory时，-w总是关闭

    ## 定义命令包

    <pre>`define run-yacc
    yacc $(firstword $^)
    mv y.tab.c $@
    endef

    foo.c: foo.y
        $(run-yacc)
    `</pre>

    ## 变量

    要求

1.  不能用':','#','='
2.  大小写敏感
3.  $用$$来表示

    ## 变量的赋值

    <pre>`A=$(B)
    B=C
    `</pre>

    是可以的,前面的变量可以使用后面变量的值进行赋值.

    也可能发生递归:

    <pre>`A=$(B)
    B=$(A)
    `</pre>

    为避免复制号=产生的这些问题, 引入了:=赋值号.
    它不能使用后面的变量进行定义.

    * * *

    **$MAKELEVEL** 如果有嵌套执行,该变量记录了嵌套执行的make层数.
    顶层make的嵌套层数是0

    一些tricky

    空格在哪里粗线?

    <pre>`    space:= #comment
        space的值是空串.

        space:=s #comment
        space的值是's '

        space:= s #comment
        space的值是's '

        nullstring:=
        space=$(nullstring) #comment
        space的值是' ' 

    所以, 串结束后到#开始中间的空白都是变量的一部分
    `</pre>

    空白会造成的问题

    <pre>`    dir := /foo/bar    # directory to put the frobs in 
        $(dir)/file 的值是'/foo/bar    /bar'
    `</pre>

    这也许没什么,但是看这个:

    <pre>`base:=/usr  #Hey buddy, you are in trouble 
    tmpdir:=$(base)/mytmpdir
    clean:
        rm -rf $(tmpdir)
    `</pre>

    命令这行展开成:

    <pre>`    rm -rf /usr  /mytmpdir 
    `</pre>

    背后一凉啊

    ## ?=用法

    <pre>`FOO?=bar
    `</pre>

    如果FOO没有被定义过, 那么定义FOO的值为bar,如果FOO定义过,那么不会产生任何效果.

    ## 变量替换

    <pre>`foo:=a.o b.o c.o
    bar:=$(foo:.o=.c)
    `</pre>

    bar 的值是

    <pre>`a.c b.c c.c
    `</pre>

    可以把变量的值作为变量名做替换

    <pre>`foo=bar
    x=foo
    y=$($(x))
    `</pre>

    y的值是bar

    ## 赋值左边也可以用变量:

    <pre>`foo=aaa
    $(foo)_bbb=ttt
    `</pre>

    等于定义了变量aaa_bbb, 它的值是ttt

    ## +=

    <pre>`obj:=a.o b.o c.o
    obj+=d.o
    `</pre>

    obj的值现在是:

    <pre>`a.o b.o c.o d.o
    `</pre>

    ## override指示符

    如果有变量是通常make的命令行参数设置的，那么Makefile中对这个变量的赋值会被忽略。要修改的话:

    <pre>`override foo=bar
    override foo:=bar
    override foo+=bar
    `</pre>

    多行变量的情况:

    <pre>`override define foo
    aaa
    bbb
    zzz
    endef
    `</pre>

    ## 命令包

    命令包的例子其实质就是用字符串定义命令, 然后调用.

    <pre>`define run-yacc
    yacc $(firstword $^)
    mv y.tab.c $@
    endef

    foo.c: foo.y
        $(run-yacc)
    `</pre>

    ## 目标变量

    针对特定目标起作用的变量

    <pre>`prog : CFLAGS = -g 
    prog : prog.o foo.o bar.o 
        $(CC) $(CFLAGS) prog.o foo.o bar.o 

    prog.o : prog.c 
        $(CC) $(CFLAGS) prog.c 

    foo.o : foo.c 
        $(CC) $(CFLAGS) foo.c 

    bar.o : bar.c 
        $(CC) $(CFLAGS) bar.c 
    `</pre>

    在这个示例中，不管全局的$(CFLAGS)的值是什么，在prog目标，以及其所引发的所有规则中（prog.o foo.o bar.o的规则），$(CFLAGS)的值都是“-g”

    ## 模式变量

    含有%的变量, 如%.o, 会产生展开动作

    ## 条件模式

    <pre>`ifeq 'cond1' 'cond2'
    ifeq "cond1" "cond2"
    ifeq (cond1,cond2)
        ...
    else
        ...
    endif
    `</pre>

    同理有ifneq

    <pre>`ifdef foo
        ...
    else
        ...
    endif
    `</pre>

    同理有ifndef

    ## 函数

    调用形式

    <pre>`$(&lt;function&gt; &lt;aruments&gt;)
    `</pre>

    .

    <pre>`comma:= , 
    empty:=
    space:= $(empty) $(empty) 
    foo:= a b c 
    bar:= $(subst $(space),$(comma),$(foo)) 
    `</pre>

    以上代码把foo从'a b c'替换为'a,b,c'
    $(subst <from>,<to>,<text>)

    * * *

    <pre>`$(patsubst &lt;pattern&gt;,&lt;replacement&gt;,&lt;text)
    $(patsubst %.c,%.o,x.c.c bar.c)
    `</pre>

    输出

    <pre>`x.c.o bar.o
    `</pre>

    等价于

    <pre>`$(x.c.c bar.c,%.c,%.o)
    `</pre>

    * * *

    <pre>`$(strip &lt;string&gt;)
    `</pre>

    去掉字符串开头和结尾的空字符

    * * *

    <pre>`$(findstring &lt;find&gt;,&lt;in&gt;)
    $(findstring a,a b c)
    `</pre>

    返回a

    <pre>`$(findstring a,b c)
    `</pre>

    返回空串

    * * *

    <pre>`$(filter &lt;pattern&gt;,&lt;text&gt;)
    sources := foo.c bar.c baz.s ugh.h 
    foo: $(sources) 
    cc $(filter %.c %.s,$(sources)) -o foo 
    `</pre>

    $(filter %.c %.s,$(sources))返回的值是“foo.c bar.c baz.s”。

    <pre>`$(filter-out &lt;pattern&gt;,&lt;text&gt;)
    `</pre>

    找到不符合模式的字串, 和$(filter)正好相反

    * * *

    <pre>`$(sort &lt;list&gt;)
    `</pre>

    排序,注,会去重

    * * *

    <pre>`$(word &lt;n&gt;,&lt;text)
    `</pre>

    取词组中第n个单词
    $(word 2, foo bar baz)返回值是“bar”。

    * * *

    <pre>`$(wordlist &lt;s&gt;,&lt;e&gt;,&lt;text&gt;)
    `</pre>

    从词组中取一串单词

    * * *

    <pre>`$(words &lt;text&gt;)
    `</pre>

    取单词的个数

    * * *

    <pre>`$(firstword &lt;text&gt;)
    `</pre>

    等价于

    <pre>`$(word 1,&lt;text&gt;)
    `</pre>

    * * *

    <pre>`override CFLAGS += $(patsubst %,-I%,$(subst :, ,$(VPATH)))
    `</pre>

    从**VPATH**环境变量里取出路径,替换冒号为空格,并逐一按模式替换,加上-I前缀

    ## 文件名操作函数

    <pre>`$(dir &lt;names)
    `</pre>

    取目录函数, 最后一个/之前的部分.

    * * *

    <pre>`$(notdir &lt;names&gt;)
    `</pre>

    取非目录部分, 最后一个/之后的部分.

    * * *

    <pre>`$(suffix &lt;names&gt;)
    `</pre>

    取各个文件名的后缀
    $(suffix src/foo.c src-1.0/bar.c hacks)返回值是“.c .c”

    * * *

    <pre>`$(basename)
    `</pre>

    取文件名的前缀部分
    $(basename src/foo.c src-1.0/bar.c hacks)返回值是“src/foo src-1.0/bar hacks”

    * * *

    <pre>`$(addsuffix &lt;suffix&gt;,&lt;names&gt;)
    `</pre>

    加后缀函数
    $(addsuffix .c,foo bar)返回值是“foo.c bar.c”

    * * *

    <pre>`$(addprefix &lt;prefix&gt;,&lt;names&gt;)
    `</pre>

    加前缀函数
    addprefix

    * * *

    <pre>`$(join &lt;list1&gt;,&lt;list2)
    `</pre>

    连接函数
    $(join aaa bbb , 111 222 333)返回值是“aaa111 bbb222 333”

    * * *

    foreach函数

    <pre>`names:= a b c d
    files:=$(foreach n,$(names),$(n).o)
    `</pre>

    files的值是a.o b.o c.o d.o , foreach的返回值会以空格隔开组成一个整字符串

    * * *

    if函数

    <pre>`$(if &lt;condition&gt;;,&lt;then-part&gt;;)  
    $(if &lt;condition&gt;;,&lt;then-part&gt;;,&lt;else-part&gt;;) 
    `</pre>

    * * *

    call函数

    <pre>`$(call &lt;expression&gt;,&lt;parm1&gt;,&lt;parm2&gt;,&lt;parm3&gt;)
    `</pre>

    .

    <pre>`reverse =  $(2) $(1) 
    foo = $(call reverse,a,b) 
    `</pre>

    reverse在这里被定义成了一个函数, $(1)和$(2)是它的第一个和第二个参数.

    call只是调用了一下已定义的函数

    * * *

    origin函数
    告知变量产生的来源

    <pre>`$(origin &lt;variable&gt;)
    `</pre>

    返回值有:

    <pre>`"undefined" 未曾定义
    "default"   默认定义,如CC
    "environment" 被环境变量定义
    "file"      在文件中定义
    "command line"  在命令行定义
    "override"  被override关键字定义
    "automatic" 自动化变量
    `</pre>

    一个例子,重写来自环境变量的值,而不重写来自命令行的值.

    <pre>`ifdef bletch 
    ifeq "$(origin bletch)" "environment" 
    bletch = barf, gag, etc. 
    endif 
    endif 
    `</pre>

    ## shell函数

    相当于调用了shell中的反引号

    <pre>``command`
    `</pre>

    .

    <pre>`contents:=$(shell command)
    files:=$(shell echo *.c)
    `</pre>

    由于这个调用会开辟一个新shell子进程,会影响运行效率,因此使用要谨慎

    ## 控制make函数

    <pre>`$(error &lt;text&gt;)
    `</pre>

    输出一个错误提示串,然后退出

    <pre>`ifdef ERROR_001 
    $(error error is $(ERROR_001)) 
    endif 

    ERR = $(error found an error!) 
    .PHONY: err 
    err: ; $(ERR)
    `</pre>

    * * *

    <pre>`$(warning &lt;text&gt;)
    `</pre>

    和error一样,但是不会退出

    ## make的执行

    指定配置文件执行

    <pre>`make -f mymake.mk
    `</pre>

    多个-f参数

    <pre>`make -f a.mk -f b.mk -f c.mk
    make将会把这些文件串在一起形成新makefile然后执行
    `</pre>

    ## MAKECMDGOALS环境变量

    存放终极目标的列表

    <pre>`sources=foo.c bar.c
    ifneq ($(MAKECMDGOALS), clean)
    include $(sources:.c=.d)
    endif
    `</pre>

    如果目标不是clean,则包含入.d文件

    ## 常见的伪目标

    “all” 
       这个伪目标是所有目标的目标，其功能一般是编译所有的目标。 
    “clean” 
       这个伪目标功能是删除所有被make创建的文件。 
    “install” 
       这个伪目标功能是安装已编译好的程序，其实就是把目标执行文件拷贝到指定的目标中去。 
    “print” 
       这个伪目标的功能是例出改变过的源文件。 
    “tar” 
       这个伪目标功能是把源程序打包备份。也就是一个tar文件。 
    “dist” 
       这个伪目标功能是创建一个压缩文件，一般是把tar文件压成Z文件。或是gz文件。 
    “TAGS” 
       这个伪目标功能是更新所有的目标，以备完整地重编译使用。 
    “check”和“test” 
       这两个伪目标一般用来测试makefile的流程。

    ## 检查规则

    “-n” 
    “--just-print” 
    “--dry-run” 
    “--recon” 
    不执行参数，这些参数只是打印命令，不管目标是否更新，把规则和连带规则下的命令打印出来，但不执行，这些参数对于我们调试makefile很有用处。

    “-t” 
    “--touch” 
    这个参数的意思就是把目标文件的时间更新，但不更改目标文件。也就是说，make假装编译目标，但不是真正的编译目标，只是把目标变成已编译过的状态。

    “-q” 
    “--question” 
    这个参数的行为是找目标的意思，也就是说，如果目标存在，那么其什么也不会输出，当然也不会执行编译，如果目标不存在，其会打印出一条出错信息。

    “-W <file>;” 
    “--what-if=<file>;” 
    “--assume-new=<file>;” 
    “--new-file=<file>;” 
    这个参数需要指定一个文件。一般是是源文件（或依赖文件），Make会根据规则推导来运行依赖于这个文件的命令，一般来说，可以和“-n”参数一同使用，来查看这个依赖文件所发生的规则命令。

    ## make的参数

    <pre>`-B 或 --always-make
    `</pre>

    所有的目标都更新

    <pre>`-C &lt;dir&gt; 或 --directory=&lt;dir&gt;
    `</pre>

    读取makefile的目录

    ## 隐含规则

    <pre>`foo: foo.o bar.o
        cc -o foo foo.o bar.o $(CFLAGS) $(LDFLAGS)
    `</pre>

    这里foo.o和bar.o的生成规则可以不写,就是隐含规则的功劳.

    .c自动生成.o是一种后缀规则,它是模式规则的一种.

    * * *

    后缀列表
    默认的后缀列表是：.out, .a, .ln, .o, .c, .cc, .C, .p, .f, .F, .r, .y, .l, .s, .S, .mod, .sym, .def, .h, .info, .dvi, .tex, .texinfo, .texi, .txinfo,.w, .ch .web, .sh, .elc, .el。

    * * *

    编译C的隐含规则

    <pre>`&lt;n&gt;.o作为目标, 会推导依赖&lt;n&gt;.c, 并调用命令:
    $(CC) -c $(CPPFLAGS) $(CFLAGS)
    `</pre>

    * * *

    C++

    <pre>`&lt;n&gt;.o -&gt; &lt;n&gt;.cc &lt;n&gt;.C
    $(CXX) -c $(CPPFLAGS) $(CFLAGS)
    `</pre>

    * * *

    Pascal

    <pre>`&lt;n&gt;.o -&gt; &lt;n&gt;.p
    $(PC) -c $(PFLAGS)
    `</pre>

    * * *

    Fortran/Ratfor

    <pre>`&lt;n&gt;.o -&gt; &lt;n&gt;.f &lt;n&gt;.F &lt;n&gt;.r
    .f "$(FC) -c $(FFLAGS)"
    .F "$(FC) -C $(FFLAGS) $(CPPFLAGS)"
    .r "$(FC) -c $(FFLAGS) $(RFLAGS)"
    `</pre>

    * * *

    汇编和汇编预处理

    <pre>`&lt;n&gt;.o -&gt; &lt;n&gt;.s 
    默认编译器as,命令
    $(AS) $(ASFLAGS)

    &lt;n&gt;.s -&gt; &lt;n&gt;.S
    默认使用C预编译器cpp,命令是
    $(AS) $(ASFLAGS)
    `</pre>

    * * *

    链接Object文件的隐含规则

    <pre>`&lt;n&gt; -&gt; &lt;n&gt;.o
    默认使用ld进行链接,命令
    $(CC) $(LDFLAGS) &lt;n&gt;.o $(LOADLIBES) $(LDLIBS)
    `</pre>

    * * *

    <pre>`x: y.o z.o
    这个规则将默认加一条依赖链 x-&gt;x.o-&gt;x.c
    `</pre>

    * * *

    ## Yax

    <pre>`&lt;n&gt;.c -&gt; &lt;n&gt;.y
    $(YACC) $(YFLAGS)
    `</pre>

    * * *

    ## Lex

    <pre>`&lt;n&gt;.c -&gt; &lt;n&gt;.l
    $(LEX) $(LFLAGS)
    `</pre>

    * * *

    ## 隐含规则使用的变量

    大致分为命令变量和参数变量两类

    ## 命令变量

    <pre>`AR  
        函数库打包程序。默认命令是“ar”。  
    AS  
        汇编语言编译程序。默认命令是“as”。 
    CC  
        C语言编译程序。默认命令是“cc”。 
    CXX  
        C++语言编译程序。默认命令是“g++”。 
    CO  
        从 RCS文件中扩展文件程序。默认命令是“co”。 
    CPP  
        C程序的预处理器（输出是标准输出设备）。默认命令是“$(CC) –E”。 
    FC  
        Fortran 和 Ratfor 的编译器和预处理程序。默认命令是“f77”。 
    GET  
        从SCCS文件中扩展文件的程序。默认命令是“get”。  
    LEX  
        Lex方法分析器程序（针对于C或Ratfor）。默认命令是“lex”。 
    PC  
        Pascal语言编译程序。默认命令是“pc”。 
    YACC  
        Yacc文法分析器（针对于C程序）。默认命令是“yacc”。 
    YACCR  
        Yacc文法分析器（针对于Ratfor程序）。默认命令是“yacc –r”。 
    MAKEINFO  
        转换Texinfo源文件（.texi）到Info文件程序。默认命令是“makeinfo”。 
    TEX  
        从TeX源文件创建TeX DVI文件的程序。默认命令是“tex”。 
    TEXI2DVI  
        从Texinfo源文件创建军TeX DVI 文件的程序。默认命令是“texi2dvi”。 
    WEAVE  
        转换Web到TeX的程序。默认命令是“weave”。 
    CWEAVE  
        转换C Web 到 TeX的程序。默认命令是“cweave”。 
    TANGLE  
        转换Web到Pascal语言的程序。默认命令是“tangle”。 
    CTANGLE  
        转换C Web 到 C。默认命令是“ctangle”。 
    RM  
        删除文件命令。默认命令是“rm –f”。 
    `</pre>

    ## 命令变量

    下面的这些变量都是相关上面的命令的参数。如果没有指明其默认值，那么其默认值都是空。

    <pre>`ARFLAGS  
        函数库打包程序AR命令的参数。默认值是“rv”。 
    ASFLAGS  
        汇编语言编译器参数。（当明显地调用“.s”或“.S”文件时）。  
    CFLAGS  
        C语言编译器参数。 
    CXXFLAGS  
        C++语言编译器参数。 
    COFLAGS  
        RCS命令参数。  
    CPPFLAGS  
        C预处理器参数。（ C 和 Fortran 编译器也会用到）。 
    FFLAGS  
        Fortran语言编译器参数。 
    GFLAGS  
        SCCS “get”程序参数。 
    LDFLAGS  
        链接器参数。（如：“ld”） 
    LFLAGS  
        Lex文法分析器参数。 
    PFLAGS  
        Pascal语言编译器参数。 
    RFLAGS  
        Ratfor 程序的Fortran 编译器参数。 
    YFLAGS  
        Yacc文法分析器参数。  
    `</pre>

    ## 中间目标

    强制指定目标为中间目标

    <pre>`.INTERMEDIATE: mid
    `</pre>

    阻止make自动删除中间目标

    <pre>`.SECONDARY: sec
    `</pre>

    按模式阻止make自动删除中间目标

    <pre>`.PRECIOUS: %.o
    `</pre>

    隐含规则链里同一个目标不能出现两次或以上

    <pre>`t -&gt; t.o -&gt; t.c -&gt; t.o X
    `</pre>

    t.o出现两次, 可能会发生递归

    * * *

    规则优化

    <pre>`t -&gt; t.o -&gt; t.c
    `</pre>

    命令如下:

    <pre>`cc -o t.o t.c
    cc -o t t.o
    `</pre>

    优化之后

    <pre>`cc -o t t.c
    `</pre>

    这样也不会产生中间文件

    ## 运行时机

    "%"的展开发生在变量和函数的展开之后，变量和函数的展开发生在make载入Makefile时，而模式规则中的"%"则发生在运行时。

    ## 模式规则(目标是一个模式)

    <pre>`%.o: %.c
        $(CC) -c $(CFLAGS) $(CPPFLAGS) $&lt; -O $@

    %.tab.c %.tab.h: %.y
        bison -d $&lt;
    `</pre>

    ## 自动变量

    下面是所有的自动化变量及其说明：

    <pre>`$@ 
        表示规则中的目标文件集。在模式规则中，如果有多个目标，那么，"$@"就是匹配于目标中模式定义的集合。 

    $% 
        仅当目标是函数库文件中，表示规则中的目标成员名。例如，如果一个目标是"foo.a(bar.o)"，那么，"$%"就是"bar.o"，"$@"就是"foo.a"。如果目标不是函数库文件（Unix下是[.a]，Windows下是[.lib]），那么，其值为空。

    $&lt; 
        依赖目标中的第一个目标名字。如果依赖目标是以模式（即"%"）定义的，那么"$&lt;"将是符合模式的一系列的文件集。注意，其是一个一个取出来的。 

    $? 
        所有比目标新的依赖目标的集合。以空格分隔。 

    $^ 
        所有的依赖目标的集合。以空格分隔。如果在依赖目标中有多个重复的，那个这个变量会去除重复的依赖目标，只保留一份。 

    $+ 
        这个变量很像"$^"，也是所有依赖目标的集合。只是它不去除重复的依赖目标。 

    $*  
       这个变量表示目标模式中"%"及其之前的部分。如果目标是"dir/a.foo.b"，并且目标的模式是"a.%.b"，那么，"$*"的值就是"dir/a.foo"。这个变量对于构造有关联的文件名是比较有较。如果目标中没有模式的定义，那么"$*"也就不能被推导出，但是，如果目标文件的后缀是make所识别的，那么"$*"就是除了后缀的那一部分。例如：如果目标是"foo.c"，因为".c"是make所能识别的后缀名，所以，"$*"的值就是"foo"。这个特性是GNU make的，很有可能不兼容于其它版本的make，所以，你应该尽量避免使用"$*"，除非是在隐含规则或是静态模式中。如果目标中的后缀是make所不能识别的，那么"$*"就是空值。 
    `</pre>

    当你希望只对更新过的依赖文件进行操作时，"$?"在显式规则中很有用，例如，假设有一个函数库文件叫"lib"，其由其它几个object文件更新。那么把object文件打包的比较有效率的Makefile规则是：

    <pre>`lib : foo.o bar.o lose.o win.o 
            ar r lib $? 
    `</pre>

    在上述所列出来的自动量变量中。四个变量（$@、$&lt;、$%、$*）在扩展时只会有一个文件，而另三个的值是一个文件列表。这七个自动化变量还可以取得文件的目录名或是在当前目录下的符合模式的文件名，只需要搭配上"D"或"F"字样。这是GNU make中老版本的特性，在新版本中，我们使用函数"dir"或"notdir"就可以做到了。"D"的含义就是Directory，就是目录，"F"的含义就是File，就是文件。

    下面是对于上面的七个变量分别加上"D"或是"F"的含义：

    <pre>`$(@D) 
        表示"$@"的目录部分（不以斜杠作为结尾），如果"$@"值是"dir/foo.o"，那么"$(@D)"就是"dir"，而如果"$@"中没有包含斜杠的话，其值就是"."（当前目录）。 

    $(@F) 
        表示"$@"的文件部分，如果"$@"值是"dir/foo.o"，那么"$(@F)"就是"foo.o"，"$(@F)"相当于函数"$(notdir $@)"。 

    "$(*D)" 
    "$(*F)" 
        和上面所述的同理，也是取文件的目录部分和文件部分。对于上面的那个例子，"$(*D)"返回"dir"，而"$(*F)"返回"foo" 

    "$(%D)" 
    "$(%F)" 
        分别表示了函数包文件成员的目录部分和文件部分。这对于形同"archive(member)"形式的目标中的"member"中包含了不同的目录很有用。 

    "$(&lt;D)" 
    "$(&lt;F)" 
        分别表示依赖文件的目录部分和文件部分。 

    "$(^D)" 
    "$(^F)" 
        分别表示所有依赖文件的目录部分和文件部分。（无相同的） 

    "$(+D)" 
    "$(+F)" 
        分别表示所有依赖文件的目录部分和文件部分。（可以有相同的） 

    "$(?D)" 
    "$(?F)" 
        分别表示被更新的依赖文件的目录部分和文件部分。 
    `</pre>

    最后想提醒一下的是，对于"$&lt;"，为了避免产生不必要的麻烦，我们最好给$后面的那个特定字符都加上圆括号，比如，"$(&lt;)"就要比"$&lt;"要好一些。

    还得要注意的是，这些变量只使用在规则的命令中，而且一般都是"显式规则"和"静态模式规则"（参见前面"书写规则"一章）。其在隐含规则中并没有意义。

    ## 模式匹配

    关于%的行为
    %.c %是匹配体
    目录匹配性质
    src/eat 会被e%t匹配
    如果命令是 c%t , 则会构造出src/cat , 即绕过了目录部分

    ## 重建内建规则

    通过复写模式规则可以重建隐含规则

    <pre>`%.o : %.c 
        $(CC) -c $(CPPFLAGS) $(CFLAGS) -D$(date) 
    `</pre>

    * * *

    取消内建规则：

    <pre>`%.o:%.c
    `</pre>

    不写命令即可

    ## 后缀规则

    双后缀
    ".c.o"相当于"%o : %c"

    单后缀
    隐含规则都是单后缀

    伪目标
    make知道一些特定的后缀，我们可以使用伪目标".SUFFIXES"来定义或是删除，如：

    <pre>`.SUFFIXES: .hack .win 
    `</pre>

    把后缀.hack和.win加入后缀列表中的末尾。

    <pre>`.SUFFIXES:              # 删除默认的后缀 
    .SUFFIXES: .c .o .h   # 定义自己的后缀
    `</pre>

    ## 函数库文件

    函数库文件的目标比较特殊，目标写法是

    <pre>`archive(member): prerequisites
    `</pre>

    意思是member这个目标要打包成archive这个目标
    如果member存在，就会直接打包

    <pre>`ar cr archive member
    `</pre>

    如果member不存在，就先生成member，然后打包，然后删除member。

    举例：

    <pre>`foolib(hack.o) : hack.o 
        ar cr foolib hack.o 
    `</pre>

    函数库文件的双目标写法是：

    <pre>`.c.a:
        $(CC) $(CFLAGS) $(CPPFLAGS) -c $&lt; -o $*.o 
        $(AR) r $@ $*.o 
        $(RM) $*.o 
    