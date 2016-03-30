---
title: 'Lex & yacc'
id: 457
categories:
  - 其它
date: 2011-04-07 23:30:40
tags:
---

Lex 和 Yacc 介绍

1\. 介绍

	Mortice Kern Systems.(MKS) 是个商业的实现, 售价为$300.
	GNU flex+bison 免费.
	Ming和Cygnus是将GNU移植到32位Windows上的移植版.

	Lex 将输入流变成记号流, 记号是字符串的数值表示. Lex将流与符号表比对匹配,每个符号也有自己的数据类型和内存地址.
	Yacc 从Lex获取输入流创建一个语法树. 操作符优先级和结合行就在这棵树里定义好了. 然后代码生成器做一次深度优先搜索创建出代码.

	设工程名为bas,则会有两个文件 bas.l bas.y,生成程序的过程是:
	<pre>	
	yacc -d bas.y
	lex bas.l
	cc lex.yy.c y.tab.c -obas.exe
	</pre>
	yacc -d bas.y 这一句会生成两个文件:
	<pre>	
	y.tab.h
	y.tab.c
	</pre>
	其中 y.tab.h 定义了符号表, y.tab.c 是语法分析器的代码. 这部分代码要调用Lex产生的函数 yylex, 就实现了yacc和Lex的连接 

	Lex 生成的文件名是：

	lex.yy.c 它用来识别源文本并产生记号流给 yacc

	主函数 main 里调用 yacc 生成的 yyparse 函数, 就实现了应用程序和yacc的交互,所以用户程序和yacc,lex之间的调用关系是这样的:
	<pre>
	main -> yyparse -> yylex	
	</pre>
2\. Lex

	Lex 将正则表达式转换为Finite Status Automation(FSA).

	Lex 的FSA没有栈, 所以无法识别如括号匹配的嵌套结构. Yacc 在 Lex的基础上增加了栈, 可以识别嵌套结构.

	以下是Lex的正则元字符:
	<pre>	
	.		匹配任意字符,除了换行
	\n		换行符
	*		{0,inf}次重复
	+		{1,inf}次重复
	?		{0,1}此重复
	^		行首
	$		行尾
	a|b		a 或 b
	(ab)+	ab 的 {1,inf} 次重复
	"a+b"	a+b 
	[]		字符集
	</pre>	
	注意,如果两个不同的模式串都能匹配正则,那么Lex选择最长的那个,如果一样长,那么按顺序选择.

	Lex 源文件扩展名是 .l 它的内容格式如下:
	<pre>	
	... definitions ...
	%%
	... rules ...
	%%
	... subroutine ...
	</pre>
	Lex 处理 .l 之后产生一个 .c 代码, 此代码的默认输入输出是 stdin 和stdout		

	举例说明:
	<pre>
	%%
		/* 匹配任意非换行字符 */
	.	ECHO;				
		/* 匹配换行符 */
	\n	ECHO;
	%%
	int yywrap(void){
		return 1;
	}
	int main(void){
		yylex();
		return 0;	
	}	
        </pre>
	这个例子里没有 definitions 部分, 有 rules 和 subroutines 部分.

	Lex 按列处理正则和操作, 在 rules 部分中, 第一列是正则, 第二列是操作, 分隔符是[ \t\n]+ 不允许第一列前面出现分隔符

	ECHO 是一个宏, 如下定义:
	<pre>	
	#define ECHO fwrite(yytext, yyleng, 1, yyout);	

	yytext	字符指针,指向正则匹配到的串
	yyleng	整数,匹配到的串的长度
	yyout	输出文件,默认为stdin
	yywrap	函数,当输入结束的时候Lex调用这个函数,可以定义自己的操作	
	</pre>
	如果你写好了rules部分,已经完成了所有的功能,不需要再写yywrap和main函数, 那么有库可以给一个默认的yywrap和main函数, 在 gcc 中, 只需要:

	gcc filename.l -lfl

	即可省去写 yywrap 和 main 函数

	以下是 Lex 预定义的变量:
	<pre>	
	int yylex(void)		词法分析的入口函数
	char *yytext		指向匹配的串的指针
	yyleng				匹配串的长度
	yylval				记号的值
	int yywrap(void)	结束时的调用函数,成功返回0,失败返回1
	FILE *yyout			输出文件
	FILE *yyin			输入文件
	INITIAL				开始条件
	BEGIN condition		切换开始条件
	ECHO				写入匹配串
	</pre>
	有的Lex实现还定义了
	<pre>
	yylineno			当前匹配串的行号
	</pre>
	以下是一个输出行号和内容的程序,并且假定Lex实现中没有yylineno
	<pre>
	%{
		int yylineno;
	%}	
	%%
	^(.*)\n		printf("%4d\t%s", ++yylineno, yytext);
	%%
	int main(int argc,char *argv[]){
		yyin=fopen(argv[1],"r");
		yylex();
		fclose(yyin);
		return 0;
	}
        </pre>
	在本代码中有 definition 部分, 在 definition 部分, 用
        <pre>
	%{

	%}
        </pre>
	括起来的部分会被原样输出到产生的 c 源文件中.

	利用正则变量替换,可以简化编写正则, 如:
        <pre>
	digit		[0-9]
	letter		[A-Za-z]
	%{
		int count;
	%}
	%%
	{letter}({letter|digit})*		count++;
	%%
	int main(void){
		yylex();
		printf("number of identifiers = %d\n", count);
		return 0;
	}
        </pre>
	其中用 letter 替代大小写字母, digit替代阿拉伯数字, 这个程序的功能就是识别程序中标识符的数量. 正则变量需用 { } 括住.

3\. Yacc	

	Yacc 用 Backus Naur Form (BNF) 来表示, 它最早被 John Backus 和 Peter Naur 用来描述 ALGOL60 , BNF 用来描述 context-free languages (CFL 上下文无关文法). 用来表示加法和乘法的 BNF 是:
	<pre>
	E -> E + E		(r1)	
	E -> E * E		(r2)	
	E -> id			(r3)
        </pre>
	Yacc 采用 Shift-Reduce 方法来分析语法， 简单过程如下，其中 . 代表栈顶位置， 右侧是当前状态下要执行的下一步操作。
	<pre>
	1	. x + y * z		shift
	2	x . + y * z		reduce(r3)
	3	E . + y * z		shift
	4	E + . y * z		shift
	5	E + y . * z		reduce(r3)
	6	E + E . * z		shift
	7	E + E * . z		shift
	8	E + E * z .		reduce(r3)
	9	E + E * E .		reduce(r2)
	10	E + E .			reduce(r1)
	11	E .				accept 
	</pre>
	在这里的第六步，我们既可 Shift ， 也可reduce(r1),变成:
	<pre>
	E * . z 
	</pre>	
	称这为 shift-reduce 冲突. 第9步，既可以如上推导reduce(r2),也可reduce(r1),变成:
	<pre>	
	E * E .
	</pre>	
	称这为 reduce-reduce 冲突，对于这些冲突，yacc 的解决方法是：
	<pre>
	1\. 如果能够 shift , 则不去 reduce
	2\. 如果出现两条冲突 reduce 规则， 则按写在前面的规则reduce。
	</pre>
	yacc 源文件的格式如下：
	<pre>
	...definitions...
	%%
	...rules...
	%%
	...subroutines...
	</pre>	
	和 Lex 一样， 如果有头部声明， 则放在
	<pre>	
	%{

	%}
	</pre>
	之中。

	下面是个简单计算器的例子演示yacc的用法。

	首先是lex程序：
	<pre>	
	%{
		#include "y.tab.h"
	%}
	%%

	[0-9]+	{
				sscanf(yytext,"%d",&yylval);
			}

	[-+\n]	return *yytext;

	[ \t]	;

	.		yyerror("invalid character");

	%%
	int yywrap(void){
		return 1;
	}
	</pre>
	然后是yacc程序：
	<pre>	
	%token INTEGER
	%{
		#include <stdio.h>
		#include <stdlib.h>
	%}	
	%%

	program:
			program expr '\n'		{ printf("%d\n",$2);}
			|
			;

	expr:
			INTEGER				{ $$ = $1; }
			| expr '+' expr		{ $$ = $1 + $3; }		
			| expr '-' expr		{ $$ = $1 - $3; }
			;
	%%
	int yyerror(char *s){
		fprintf(stderr, "%s\n", s);
		return 0;
	}
	int main(void){
		yyparse();
		return 0;
	}
	</pre>
	在 yacc 文件头部的 %token INTEGER 定义了一个常量，此常量用来和Lex进行数据交换，在yacc生成的 y.tab.c 文件里，会出现如下几句：
	<pre>	
	#ifndef YYSTYPE
	#define YYSTYPE int
	#endif
	#define INTEGER 258
	extern YYSTYPE yylval;	
	</pre>
	Lex 识别到的一个词有 yytext 和 yylval，yylval的类型是由YYSTYPE决定的，如果没有定义YYSTYPE, 那么yacc默认定义为int类型。 
	自定义常量都要大于255, 因为小于255的值是给 ascii 字符保留的，比如 '+', 它的 yylval 就是 '+' 对应的ascii值。 yacc 为了进行一些条件判断，如文件结束，要再占去几个值，所以此处的INTEGER的值是258,如果有别的标号，会在这个基础上增长， 比如有 
	</pre>
	%token FLOAT 
	</pre>
	那么会被定义为
	<pre>
	#define FLOAT 259
	</pre>	
	yacc 会保持两个栈，一个是语法栈，一个是由 YYTYPE 的值组成的值栈，这两个栈完全同步，一边是识别的文本，一边是记号流的值. yytext的值就是语法栈里的值， 而 yylval 以及 yacc 的语法规则部分正则后的操作里出现的 $$ $1 $2 等都是值栈的值。

	分析下一个句子的处理过程，当遇到这样一个句子的时候：
	<pre>	
	expr: expr '+' expr		{ $$ = $1 + $3; }
	</pre>
	yacc 会识别到三个值，将第一个 expr 的值放在 $1, '+' 放在 $2, 第二个 expr 放在 $3， 栈上弹出这三个值以后，被后面的操作代码处理， $$ 就是栈顶值，处理之后重新被压回栈中。 这样就将三个记号变为了1个. yacc 会不断进行这个过程，直到栈中只剩下一个记号（即开始记号）时结束。			

	可以自行编写 yyerror 函数， 当发生语法错误的时候此函数被调用。

	以上代码仍有二义性，但是根据 yacc 的两条默认处理规则，1\. 能 shift 就不 reduce 2\. 两种reduce 则按先出现的规则reduce 能够产生一个非二义的结果，代码能够编译，但是会有警告产生。