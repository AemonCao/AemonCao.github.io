---
title: 在 Visual Studio Code 中远程开发
date: 2020-03-08 16:26:56
tags:
  - Visual Studio Code
categories:
  - Visual Studio Code
---

由于近期的疫情影响，导致复工时候只能在家办公，所以捣鼓了一下远程办公，特此记录一下。

<!-- more -->

##  准备

0.  一台可以通过 ssh 来进行连接的服务器（云服务器或者公司的服务器）；

1.  一台用于可以连接 `0` 中的服务器的 PC。

##  安装 Visual Studio Code

点击这里下载「[Visual Studio Code](https://code.visualstudio.com/)」，然后安装。

##  安装远程插件

新安装的 Visual Studio Code 并不能直接进行远程工作，需要下载相应的插件。

0.  在左侧边栏点击 Extensions（或者使用快捷键 Ctrl+Shift+X）调出插件管理页面；

1.  在顶部搜索栏搜索「Remote」,在搜索结果中选择 「Remote - SSH」 进行安装；

    在安装的时候会自动为你安装 「Remote - SSH: Editing Configuration Files」。

2.  安装完成后，你的左侧边栏就会多出一个「远程资源管理器（Remote Explorer）」的图标，如下图所示：

    ![Remote Explorer](https://i.loli.net/2020/03/08/YXuTfDQxovHUkNm.png)

##  开始使用

0.  进行连接

    点击远程资源管理器图标，在 SSH TARGETS 栏中点击加号，来新建一个 ssh 连接：

    ![SSH TARGETS](https://i.loli.net/2020/03/08/mlqFf1N9nuxRaEp.png)

    输入：

    ```shell
    ssh user@host
    ```

    `user` 是用来登录的用户名，`host` 则是你需要远程的服务器的地址。

    ssh 的默认端口是 22，如果你修改过该端口，则需要使用 p 参数：

    ```shell
    ssh -p 9588 user@host
    ```

    9588 则是新的 ssh 端口。

    回车，然后选择一个配置文件进行保存。

    这时候在左侧 SSH TARGETS 下将会出现以你的 host 为名称的项目，右键选择在连接当前窗口或者在新窗口，进行连接。

    ![password.png](https://i.loli.net/2020/03/08/jXhLQ2UgY5Gwl3f.png)

    输入当前用户名所对应的密码。

    这时你的窗口左下角将会将会显示正在连接：

    ![Opening](https://i.loli.net/2020/03/08/Fa6xRzThrIsEYUN.png)

    等待一段时间后。如果变成以下样式，则为连接成功：

    ![success.png](https://i.loli.net/2020/03/08/v6jDnFKbcJPWLRl.png)

1.  选择文件夹

    连接成功后，你要选择需要进行工作的文件夹，基本上是一个项目的根目录。

    0.  点击左侧边栏的 Expoler（或者使用快捷键 Ctrl+Shift+E）打开资源管理器：

        ![expoler.png](https://i.loli.net/2020/03/08/Tltvb9aLrmq67CI.png)

    1.  点击 Open Folder 按钮来选择文件夹：

        ![open-folder.png](https://i.loli.net/2020/03/08/2JU7CYA5i6T1Fzc.png)

    2.  点击 OK，这时可能需要你再次输入密码，之后你的资源管理器中就会有选择文件夹下的文件了。

2.  权限修改

    这时候你已经可以点击任意一个文件来进行预览了，为什么说是预览呢，因为当你尝试编辑并保存时，系统会有如下警告：

    ![error.png](https://i.loli.net/2020/03/08/BemOgUkIxCsjPrh.png)

    提示权限不够。

    最简单的方法是执行以下命令：

    ```shell
    sudo chmod 777 /path
    ```

    之后再保存的话就可以顺利保存成功了。