---
title: Linux学习记录
date: 2020-03-11 19:52:38
tags:
  - Linux
categories:
  - Linux
---

这是一篇偏向流水账的文章，主要记一下平时使用 Linux 过程中的一些小技巧，或者某些命令的使用方法。

<!-- more -->

##  命令

### tac

>   用于将文件已行为单位的反序输出，即第一行最后显示，最后一行先显示。

今天在写博客的[关于页](/about)时，把原本记在微信收藏中的一些「骚话」贴过来了，原本记录的时候是按时间从旧到新，如果有新的，就往底部添加。

但是觉得这样不太好，我希望比较新的「骚话」可以放在前面。

第一时间是想用 _python_ 或者 _JavaScript_ 写个小脚本跑一下，但是有点懒，就去网上找了一下资料，被我找到了[这个](https://www.itranslater.com/qa/details/2106725530790790144)，排在第一的就是 [tac](https://man.linuxde.net/tac) 命令。

tac 其实就是 cat 的 reverse，包含于 [coreutils](https://zh.wikipedia.org/zh-hans/GNU%E6%A0%B8%E5%BF%83%E5%B7%A5%E5%85%B7%E7%BB%84)。

用法和 cat 类似，预览：

```shell
tac abc.txt
```

输出到文件：

```shell
tac abc.txt > cba.txt
```

目前想到的用法可能是用在日志文件上，可以将日志文件 reverse，方便查看最新的日志。
