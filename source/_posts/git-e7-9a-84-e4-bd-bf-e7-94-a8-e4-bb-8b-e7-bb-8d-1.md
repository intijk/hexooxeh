---
title: Git 的使用介绍 (1)
tags:
  - git
id: 307
categories:
  - 其它
date: 2011-02-06 09:51:51
---

本篇文章内容多来自 [这里 ](http://www.linuxgem.org/user_files/linuxgem/Image/git-tutor.pdf) 感谢 LiYanrui 的这份优秀文档.&nbsp;

# <span style="color:#0ff;">

	版本控制工具，git。</span>

## <span style="color:#afeeee;">

	1.为什么这个世界需要版本控制？</span>

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 世界是变化的。

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 如果有一个既定的目标，比如一份文档，一个工程，那么随着时间的演进，它总是在变化的，我们会在其上修改，更新，创造出不同于以前的新版本。保留修改的历史，方便的供人查阅、变更、合并所做的修改，就是版本控制的目的。

	&nbsp; &nbsp; &nbsp; 版本控制不仅仅用来控制软件的版本，也可以用来管理文档，书籍等文本信息。它是一个需要和人进行灵活交互的数据库。

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 有许多版本控制的软件和工具，比如 CVS，SVN，我们今天的主角是 git.

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; git 是Linux 之父 Linus Torvalds开发的项目，旨在替换掉曾经用来管理Linux Kernel的存在协议争议的版本控制工具BitKeeper.

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [早期Linux的開發人員是使用 BitKeeper 来管理版本控制和维护程式碼。2005年的时候，开发 BitKeeper 的公司同 Linux 内核开源社区結束合作关系，並收回使用BitKeeper的權利。Torvalds 开始着手开发Git 来替代BitKeeper。from wikipedia]

## <span style="color:#afeeee;">

	2.git的基本思想，基本的设计特性与使用方法。</span>

&nbsp;

&nbsp; 

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 设计思想，设计特性待定。

	&nbsp;

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 使用方法:

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 需要维护的文件放在一个总的文件夹下，这个文件夹称作工作树。工作树下面建立一个 .git 的隐藏目录，此目录中是所有git的管理历史记录和历史文件，这个目录叫做仓库。

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 我们需要做的就是

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1\. 更新工作树下的文件，要git管理这些变更，自动加入到仓库中。

	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2\. 当有需要时，从仓库中还原出工作树，取出历史版本。

	假设某用户名为 alice，主目录为/home/alice, 在主目录下，有一个 work 目录，这个目录下是alice正在写的一篇 tex 文档， 它的名字是 paperwork.tex . 我们的任务就是帮助alice 管理这个文档，在产生新版本的同时，保存历史版本。

	建立一个工作树：

	$ cd ~/work

	$ git init

	会出现如下字样

	Initialized empty Git repository in /home/alice/work/.git/

	进行这一步之后，会在 /home/alice/work/ 下建立隐藏目录 .git/ 即前文提到的&rdquo;仓库&ldquo;，而此时的 /home/alice/work 目录，就是此项目的&ldquo;工作树&rdquo;。

	当git建立以后，不会自动加入任何文件，当需要对一个文件或者目录进行管理的时候，我们需要做的第一件事就是将需要管理的目录和文件利用git-add命令加入到仓库的管理列表中。

	$ cd /home/alice/work

	$ git add .

	这样，work目录下的所有文件，包括隐藏文件和隐藏文件夹(不包含.git/)都被加入到git的监视列表中，他们的变更，包括修改删除，都会被git记录，注意：在目录树下新建一个文件不会被git自动注意到，必须显示的使用git add filename加入到监视列表中。

	alice这时可以专注于她的文档写作了。我们假定她目前所做的工作只修改了 paperwork.tex .

	当 alice 完成更新以后， 她需要把这次的修改进行保存，只需用git-commit命令进行提交。

	$ git commit

	git会调用系统默认的编辑器，比如 Vim 打开本次修改历史，要求你输入一些备注信息，然后返回，我们在底部输入一行字：

	The first change of paperwork.tex

	然后保存退出，可以看到如下字样：

	[master (root-commit) 0d6f859] The first change of paperwork.tex

	&nbsp;Committer: alice &lt;alice@solo.(none)&gt;

	Your name and email address were configured automatically based

	on your username and hostname. Please check that they are accurate.

	You can suppress this message by setting them explicitly:

	&nbsp;&nbsp;&nbsp; git config --global user.name &quot;Your Name&quot;

	&nbsp;&nbsp;&nbsp; git config --global user.email you@example.com

	If the identity used for this commit is wrong, you can fix it with:

	&nbsp;&nbsp;&nbsp; git commit --amend --author=&#39;Your Name &lt;you@example.com&gt;&#39;

	&nbsp;0 files changed, 0 insertions(+), 0 deletions(-)

	&nbsp;create mode 100644 paperwork.tex

	最主要的就是最后两行，它告诉我们本次的版本控制修改了0个文件，创建了一个文件属性为100644的名为 paperwork.tex 的受管理文件的记录。

	git-commit命令也可以用简短的方式进行提交，这样就无须打开一个编辑器。

	$ git commit -m &quot;The first change of paperwork.tex&quot;

	与上面的提交是等价的。如果同时修改了多个文件，要一一

	上面出现的 git config 等是什么意思？

	git 希望每个提交者都能提供完整的姓名和邮箱来确认提交者的身份，这在多人项目中会带来便利，我们这里设置一下

	$ git config --global user.name &quot;alice&quot;

	$ git config --global user.email alice@gmail.com

	如果你在工作树目录下有若干文件，其中有些你不希望让 git 插足管理，那么将这些文件或文件夹的名字加入 .gitignore 文件中即可。

	比如 alice 在编辑这份文档时，会产生多个中间文件，包括paperwork.aux paperwork.log paperwork.tex.log paperwork.pdf 但是只有 paperwork.tex 是需要关注的文件，所以让 git 忽略这些文件， 可以向 .gitignore 里写入：

	paperwork.aux 

	paperwork.log 

	paperwork.pdf

	paperwork.tex 

	paperwork.tex.log 

	保存之后，使用 git-add 命令添加 .gitignore 进入监视列表并用 git-commit 更新：

	$ git add .gitignore

	$ git commit

	alice 可以在评注里加入&quot;unwatch some intermidiate file&quot;.

	查看所做的历史修改

	利用 git-log 命令可以查看修改的版本历史

	$ git log

	其中准确列出了各个版本的修改历史，我们查看其中一项：

	commit eda0f0bbf22d63c1bcc7b11375f32dcd57b1ffb2

	Author: alice@gmail.com &lt;alice@solo.(none)&gt;

	Date:&nbsp;&nbsp; Tue Feb 1 19:16:25 2011 +0800

	&nbsp;&nbsp;&nbsp; unwatch some intermidiate file

	其中 commit 字段是 git 生成的唯一版本号，Author 是作者信息，Date 是此次提交的时间，下方有提交者做的评注。

	如果要详细查看某次的修改内容，可以使用 git-show 命令：

	$ git show eda0f0bbf22d63c1bcc7b11375f32dcd57b1ffb2

	show 后面跟的是你想要查看的某次提交的 commit 号码， 可以只取其前几位：

	$ git show eda0

	可以看到比 git-log 更多的具体修改信息。

	commit eda0f0bbf22d63c1bcc7b11375f32dcd57b1ffb2

	Author: alice@gmail.com &lt;alice@solo.(none)&gt;

	Date:&nbsp;&nbsp; Tue Feb 1 19:16:25 2011 +0800

	&nbsp;&nbsp;&nbsp; unwatch some intermidiate file

	diff --git a/.gitignore b/.gitignore

	new file mode 100644

	index 0000000..1b535d5

	--- /dev/null

	+++ b/.gitignore

	@@ -0,0 +1 @@

	+paperwork.aux paperwork.tex paperwork.log paperwork.tex.log paperwork.pdf

	对于最新的一个版本，可以使用HEAD这个版本号来进行查看

	$ git show HEAD

	版本号+^ 表示比版本的较早的版本，称作父版本，^标签可以叠加。

	$ git show HEAD^

	$ git show HEAD^^ #查看父版本

	$ git show HEAD^4 #指定有4个^的前辈版本

	也可以创建一个 tag 给版本起个别名：

	$ git tag v0.1 eda0

	$ git show v0.1&nbsp; #等价于 git show eda0

	tips: 为什么使用 git-command 来作为git的命令名，而在使用的时候是 git command 的形式？首先，当熟悉这种表示之后，我们不会产生歧义。而且，在查阅手册的时候，可以采用 man git-command 的格式。比如需要查阅 git add 的用法，只需

	man git-add

	&nbsp;