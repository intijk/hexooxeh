---
title: Chromium里使用pdf viewer
id: 1220
categories:
  - Linux
date: 2013-05-25 21:10:23
tags:
---

<!--:zh-->把一个pdf拖动到google-chrome里，可以直接打开，而拖动到chromium里，则会出现下载提醒，说明chromium无法直接打开pdf文件。

    about:plugins
    `</pre>
    结果显示chromium缺少chrome pdf viewer这个插件。

    ## 解决方法

    从google的安装目录里找到libpdf.so这个文件（/opt/google/chrome/libpdf.so), 拖动到chromium的目录下(/usr/lib/chromium or /usr/lib64/chromium), 重启打开chromium，再次
    <pre>`about:plugins
    `</pre>
    确定chrome pdf viewer处于enabled状态，拖动pdf到chomium里，woking～

    _注:如果不想安装google-chrome，直接从包里拖出libpdf.so即可。_

    [![chromium](http://intijk.com/wp-content/uploads/2013/05/chromium.png)](http://intijk.com/wp-content/uploads/2013/05/chromium.png)<!--:--><!--:en-->Drag a pdf file to google-chrome, you can open it directly, but when drag into chromium, it shows a download notice, that means chromium can not open and view pdf file.

    Run this in address bar:
    <pre>`about:plugins
    `</pre>
    Compare to google-chrome, chromium lack the plugin called 'chrome pdf viewer'.

    ## Solution

    Copy 'libpdf.so' from google-chrome installation path(/opt/google/chrome/libpdf.so) to chromium installation path(/usr/lib/chromium for 32bit, /usr/lib64/chromium for 64bit), run this in address bar again:
    <pre>`about:plugins

Confirm plugin 'chrome pdf viewer' is in 'enabled' status. Now drag the pdf file you want to chromium, it should working ~

_Notice: if you don't want to install goolge-chrome, just unpack the package and get the libpdf.so_

[![chromium](http://intijk.com/wp-content/uploads/2013/05/chromium.png)](http://intijk.com/wp-content/uploads/2013/05/chromium.png)<!--:-->