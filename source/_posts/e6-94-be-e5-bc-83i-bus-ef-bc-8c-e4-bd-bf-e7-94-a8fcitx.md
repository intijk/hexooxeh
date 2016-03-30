---
title: 放弃I-bus，使用fcitx
tags:
  - fcitx
  - ibus
id: 221
categories:
  - 其它
date: 2011-01-27 04:29:47
---

ibus的慢速已经让人失望了， 换用fcitx！
小企鹅3.*.*版已经停止开发，而fcitx4采用了新的架构，支持换肤，俨然已经是一个专业级的输入法。
到[项目主页](http://code.google.com/p/fcitx/downloads/list)上下载最新的  fcitx-4.*.*_all.tar.gz ,当前是 fcitx-4.0.1_all.tar.gz 。

然后就是老三样了
./configure
make
sudo make install

如果出现
/usr/local/bin/fcitx-config-gtk: error while loading shared libraries: libfcitx-config.so.4: cannot open shared object file: No such file or directory

只需
sudo ldconfig
再不行就加一个符号链接
sudo ln -s /usr/local/lib/libfcitx-config.so /usr/lib/libfcitx-config.so.4

然后在 ~/.profile 里面加入
fcitx &

这样就能够登陆时启动小企鹅输入法了。

上图， 有图有真相：
[![](http://intijk.com/wp-content/uploads/2011/01/fcitx4.png "fcitx2")](http://intijk.com/?attachment_id=247)