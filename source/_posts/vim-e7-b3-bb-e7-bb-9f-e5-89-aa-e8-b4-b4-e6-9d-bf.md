---
title: vim 系统剪贴板
tags:
  - gvim
  - vim
  - 系统剪贴板
id: 524
categories:
  - 其它
date: 2011-07-19 14:32:52
---

:reg命令可以看vim当前的剪贴板
在arch中，如果pacman -S vim ，那么这个vim是没有+这个系统剪贴板的，需要pacman -S gvim，这时才有+这个剪贴板，装了这个以后，命令行的vim也是有的。
选中一段内容，"+y （注意要输入双引号）即可复制到系统剪贴板
而 "+p 可以把系统剪贴板内容粘贴到vim中。