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

本文不定期更新，可以点击目录来跳转观看。

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
$ tac abc.txt
```

输出到文件：

```shell
$ tac abc.txt > cba.txt
```

目前想到的用法可能是用在日志文件上，可以将日志文件 reverse，方便查看最新的日志。

### w

>   用于显示目前登入系统的用户信息。

这个命令也不算是新学的，但是对输出的信息还只是一知半解，所以今天去查了一下，这边记录一下。

```shell
$ w
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
    $ man w
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
*   `:qa`：退出所有（`:quitall` 的缩写）；
*   `:cq`：退出且不保存（即便有错误）。

你也可以直接在「正常模式」下输入 `ZZ` 来保存并退出 Vim（和 `:x` 相同），或者 `ZQ` 不保存并退出（和 `:q!` 相同），注意此处 `ZZ` 大写和小写是完全不同的。

### 查找

在命令模式按下 <kbd>/</kbd>，之后跟上你需要搜索的字符串然后回车就可以进行搜索，如果要查找下一个只需要按下 <kbd>n</kbd>，上一个的话则是 <kbd>N</kbd>。

### 上下移动一行代码

在 Visual Studio Code 中，上下移动代码比较简单，只要使用 <kbd>alt</kbd> + <kbd>↑</kbd> 或者 <kbd>alt</kbd> + <kbd>↓</kbd> 就可以移动了。

而在 vim 中，首先你需要将光标移动到你想要操作的行，按下两次 <kbd>d</kbd>（剪切当前行），然后移动你的光标到你想要的位置，按下 <kbd>p</kbd>（在光标之后粘贴） 或 <kbd>P</kbd>（在光标之前粘贴），即可将一行代码移动过来了。

参考：

*   [请问vim如何移动当前行向上或向下？不用选中](https://www.v2ex.com/t/49043)

*   [最全的vim快捷键](https://blog.csdn.net/donahue_ldz/article/details/17139361)

##  Git

### 保存 GitHub 的用户名和密码

通过 ssh 远程服务器进行开发后，如果需要 `git push` 到 GitHub 的话，每次都需要重新输入用户名和密码，比较繁琐。

可以使用以下命令来设置：

```shell
$ git config --global credential.helper cache
```

或者：

```shell
$ git config --global credential.helper store
```

使用 `cache` 选项的话，会将用户名密码保存在内存中，平且在15分钟后从内存中消失，使用 `store` 选项的话，则会将用户名密码保存在磁盘中，永远都不会消失，除非你更改了 GitHub 的密码，否则是永远都不用输入密码的。但是密码会以明文的方式存储在当前用户的根目录下的 *.git-credentials* 文件中，比较不安全。

如果你是 Mac 系统，则可以使用第三个选项 `osxkeychain`，可以将凭证存储都[钥匙串](https://support.apple.com/zh-cn/HT204085)中。这样你的凭证虽然还是存储在磁盘中，但是它是被加密的，并且也是可以永久使用。

参考：

*   [Git 工具 - 凭证存储](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%87%AD%E8%AF%81%E5%AD%98%E5%82%A8)

### Submodules

在搭建 hexo 博客的时候，当使用了 next 的 themes 时，修改配置后，发现 commit 总是报错。总觉得哪里不对劲，在 GitHub 上查看项目的时候，next 文件夹只有一个名称，无法点击进入。

如果一个项目中包含了另一个项目，我们就要使用 Git 的 Submodules 来进行管理。

首先要在当前主项目上使用 `git submodule` 命令来添加一个子项目：

```shell
$ git submodule add https://github.com/AemonCao/hexo-theme-next.git themes/next
```

默认情况下，子项目会放在一个与仓库名同名的目录中，如果想放到其他地方，可以在命令最后添加一个路径。

这时候，主项目的目录下会有一个新的 *.gitmodules* 的文件。

这是用来保存项子项目的 URL 以及已经拉取的本地目录之间的映射：

```git
[submodule "themes/next"]
        path = themes/next
        url = https://github.com/AemonCao/hexo-theme-next.git
```

当前只有一条记录，如果添加了多个子项目，则会有多条记录。

之后在 Visual Studio Code 的「源代码管理页面」的「源代码管理提供程序」中就会出现两个项目，一个是主项目，一个就是刚刚添加的子项目。

{% asset_img 源代码管理提供程序.png 源代码管理提供程序 %}

现在你就可以更好得管理这两个项目了。

此时在 GitHub 上，你的主项目中相应的[子项目文件夹](https://github.com/AemonCao/AemonCao.github.io/tree/source/themes)也会有一个指向原仓库的某一次提交的一个链接。

参考：

*   [Git 工具 - 子模块](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)