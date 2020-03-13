---
title: Linux 学习记录
date: 2020-03-11 19:52:38
tags:
  - Linux
  - Vim
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

### w

>   用于显示目前登入系统的用户信息。

这个命令也不算是新学的，但是对输出的信息还只是一知半解，所以今天去查了一下，这边记录一下。

```shell
w
```

```
 22:33:11 up 4 days,  9:44,  1 user,  load average: 0.05, 0.03, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/1    192.168.1.88     22:22    7.00s  0.03s  0.00s w
```

第一行显示以下信息：当前时间（`22:33:11`），系统运行时间（`up 4 days,  9:44`），当前一共有多少用户登录（`1 user`），以及过去 1 分钟，5 分钟，15 分钟的系统平均负载（`load average: 0.05, 0.03, 0.05`）。

第二行开始是一张表，标头依次为登录名（`USER`），登录后系统分配的终端号（`TTY`），远程主机名（`FROM`），何时登录（`LOGIN@`），空闲时间（`IDLE`），与该 TTY 终端连接的所由进程占用的时间，不包括过去的后台作业时间（`JCPU`），当前进程所占用的时间（`PCPU`），当前进程（`WHAT`）。

参考：

*  
    ```shell
    man w
    ```

*  [w command in Linux with Examples](https://www.geeksforgeeks.org/w-command-in-linux-with-examples/)

*  [图解Linux命令之--w命令](https://blog.csdn.net/Jerry_1126/article/details/52088987)

*  [Linux的JCPU与PCPU](https://blog.csdn.net/zzxian/article/details/8070144)

##  Vim

这里写一下 Vim 的学习历程，之前都是用 nano，但是不是所有的系统都预装的 nano。而且最近在使用 Vim 的时候，发现并没用想象中那么难用。更加下定了要学好 Vim 的决心。

### 退出 Vim

在学习 Vim 前，首先要先学会如何退出 Vim，毕竟很多人直到老死也没有退出 Vim。

>   How to exit the Vim editor?

这个问题在 [Stack Overflow](https://stackoverflow.com/questions/11828270/how-do-i-exit-the-vim-editor) 得到了 200多万次的查看（截至 2020-3-12 13:55:28）。

其实排名第一的答案已经讲得很清楚了，这边就把答案拿过来放在这里了：

点击 <kbd>ESC</kbd> 进入「正常模式」，然后输入 <kbd>:</kbd>，进入「命令模式」。此时屏幕的下方会出现一个冒号，你可以输入以下命令，并按 <kbd>ENTER</kbd> 执行：

*   `:q`：退出（`:quit` 的缩写）；
*   `:q!`：退出且不保存（`:quit!` 的缩写）；
*   `:wq`：保存并退出；
*   `:wq!`：保存并退出即使文件没有写入权限（强制保存退出）；
*   `:x`：保存并退出（类似 `:wq`，但是只有在有更改的情况下才保存）；
*   `:exit`：保存并退出（和 `:x` 相同）；
*   `:qa`：退出所有(`:quitall` 的缩写)；
*   `:cq`：退出且不保存（即便有错误）。

你也可以直接在「正常模式」下输入 `ZZ` 来保存并退出 Vim（和 `:x` 相同），或者 `ZQ` 不保存并退出（和 `:q!` 相同），注意此处 `ZZ` 大写和小写是完全不同的。

### 查找

在命令模式按下 <kbd>/</kbd>，之后跟上你需要搜索的字符串然后回车就可以进行搜索，如果要查找下一个只需要按下 <kbd>n</kbd>，上一个的话则是 <kbd>N</kbd>。
