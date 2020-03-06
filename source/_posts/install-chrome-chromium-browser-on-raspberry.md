---
title: 在树莓派上安装 Chrome（Chromium） 浏览器
tags:
  - Linux
  - 树莓派
url: 245.html
id: 245
categories:
  - Linux
  - 树莓派
date: 2017-09-23 10:58:54
---

用习惯了谷歌浏览器，树莓派上面自带的浏览器有些不习惯，所以就想着弄一个 _Chrome_ 在上边。其实是 _Chromium_，_Chromium_ 是 _Google_ 的 _Chrome_ 浏览器背后的引擎。不过除了一些细小功能上的差别以及图标的样子，_Chromium_ 和 _Chrome_ 在绝大部分功能上还是一致的。

<!-- more -->

要安装的话，首先你需要一台树莓派。为了输入方便，你可能还需要一台 _PC_ 通过 _SSH_ 连接到这台树莓派上边。

搜索了很多资料，最后是安装这个[视频](http://video.tudou.com/v/XMTc4OTczNjEyMA==.html)的方式来安装的。

接下来的安装方法都是视频中所讲的，如果懒得看我写的，也可以直接去看视频。

1.  准备工作

    先要去这个网站去下载三个文件

    [http://ports.ubuntu.com/pool/universe/c/chromium-browser/](http://ports.ubuntu.com/pool/universe/c/chromium-browser/)

    下载地址分别是：

    0.  [http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-codecs-ffmpeg-extra\_48.0.2564.82-0ubuntu0.15.04.1.1193\_armhf.deb](http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-codecs-ffmpeg-extra_48.0.2564.82-0ubuntu0.15.04.1.1193_armhf.deb)

    1.  [http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-browser\_48.0.2564.82-0ubuntu0.15.04.1.1193\_armhf.deb](http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-browser_48.0.2564.82-0ubuntu0.15.04.1.1193_armhf.deb)

    2.  [http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-browser-l10n\_48.0.2564.82-0ubuntu0.15.04.1.1193\_all.deb](http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-browser-l10n_48.0.2564.82-0ubuntu0.15.04.1.1193_all.deb)

    可以使用如下命令来进行下载：

    ```shell
    wget http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-codecs-ffmpeg-extra_48.0.2564.82-0ubuntu0.15.04.1.1193_armhf.deb
    ```

    ```shell
    wget http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-browser_48.0.2564.82-0ubuntu0.15.04.1.1193_armhf.deb
    ```

    ```shell
    wget http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-browser-l10n_48.0.2564.82-0ubuntu0.15.04.1.1193_all.deb
    ```

    顺序可以调换，没有关系。

    速度可能并不稳定，下载的时候请耐心等待。

2.  开始安装

    下载完成后使用命令：

    ```shell
    ls
    ```

    来查看是否下载成功。

    检查三个文件名是否有误，确认无误后进行接下来的操作。

    输入：

    ```shell
    sudo dpkg -i chromium-codecs-ffmpeg-extra_48.0.2564.82-0ubuntu0.15.04.1.1193_armhf.deb
    ```

    等待安装完成后，再输入：

    ```shell
    sudo dpkg -i  chromium-browser_48.0.2564.82-0ubuntu0.15.04.1.1193_armhf.deb chromium-browser-l10n_48.0.2564.82-0ubuntu0.15.04.1.1193_all.deb
    ```

    耐心等待安装完成。

    当提示完成后，你就可以去树莓派使用 _Chromium_ 浏览器啦。
