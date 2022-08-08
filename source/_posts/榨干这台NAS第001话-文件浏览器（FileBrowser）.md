---
title: 榨干这台 NAS 第 001 话-文件浏览器（File Browser）
tags:
  - NAS
  - unRAID
  - Docker
  - 'File Browser'
categories:
  - '榨干这台 NAS'
date: 2022-08-08 16:08:44
description:
---

这是一系列关于 NAS 的文章，系列的名称你们也看到了：「**榨干这台 NAS**」。我将尽可能详细的介绍 NAS 相关的知识，帮助你最大限度的发挥你的手中 NAS 的威力！

<!-- more -->

在上一话中我们介绍了目录结构，那么应该如何编辑这些目录和文件呢？最最原始的方式就是使用命令来执行这些操作。但是这对于一部分不熟悉 Linux 系统的人来说，会显得有些困难。所以，这一话，我将介绍一个软件，可以用于 NAS 的文件浏览。如果你是群晖或威联通用户，那么其系统自带的软件就应该能满足大部分需求了，所以可以跳过这一话。

PS：接下来的操作将在 unRAID（6.10.2） 系统上进行。

PSS: 当然对于非 unRAID 系统的用户，我也会演示一下在 Linux 系统下应该如何进行安装。

### unRAID 下的安装和使用

#### 搜索

前往「应用『Apps』」页面搜索「FileBrowser」，选择下图所示的应用：

![FileBrowser.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/榨干这台NAS第001话-文件浏览器（FileBrowser）/FileBrowser.png)

点击「安装『Install』」，即图中「Actions」按钮所在位置（因为我已经安装过这个软件，所以不会显示「Install」按钮了）。

#### 配置

![FileBrowserConfig.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/榨干这台NAS第001话-文件浏览器（FileBrowser）/FileBrowserConfig.png)

然后依照上图所示填写。

主要是配置了两个路径和一个端口：

1. 将宿主机的 `/` 路径映射至容器的 `/srv` 路径，此路径为你需要进行文件管理的路径；

2. 将宿主机的 `/mnt/user/appdata/filebrowser/` 路径映射至容器的 `/db/` 路径，此路径为此容器的配置数据库路径，你之后所设置的用户名密码等个性配置信息都将存储在这里；

3. 将宿主机的 `8222` 端口映射到容器的 `80` 端口，此端口为该容器的 WebUI 端口，之后可以通过此端口来访问 WebUI 界面（注意，请确定宿主机的 `8222` 端口无其他程序占用，不然将无法启动容器，如果被占用，可以自行更换端口）。

点击应用，即可启动容器。如果你的配置无误，那么在等待片刻后，容器将会自动启动，接下来就可以使用了。

附上 unRAID 下的启动命令：

```shell
/usr/local/emhttp/plugins/dynamix.docker.manager/scripts/docker run -d --name='FileBrowser' --net='bridge' -e TZ="Asia/Shanghai" -e HOST_OS="Unraid" -e HOST_HOSTNAME="Tower" -e HOST_CONTAINERNAME="FileBrowser" -l net.unraid.docker.managed=dockerman -l net.unraid.docker.webui='http://[IP]:[PORT:8222]/files/' -l net.unraid.docker.icon='https://github.com/maschhoff/docker/raw/master/filebrowser/35781395.png' -p '8222:80/tcp' -v '/':'/srv':'rw' -v '/mnt/user/appdata/filebrowser/':'/db/':'rw' 'filebrowser/filebrowser' -d /db/database.db
```

#### 使用

打开浏览器，访问 <http://192.168.1.223:8222>（注意，如果你在上诉配置中修改过端口，请访问相应的端口，而 IP 地址则是你的 NAS 的 IP 地址），即可前往 File Browser 的操作页面。

默认的用户名为：`admin`，默认为密码为：`admin`。

##### 修改密码

登录后的第一件事，请前往「设置」-「个人设置」页面修改你的密码。

##### 一些使其更好使用的配置

0. 在「设置」-「个人设置」中将语言修改为中文；

1. 在「设置」-「个人设置」中**取消勾选**「不显示隐藏文件」，这样你就能看到隐藏的文件和文件夹了；

2. 在「设置」-「个人设置」中**取消勾选**「使用单击来打开文件和目录」，这样可以更加符合 Windows 资源管理器的操作逻辑，同时也可以使用 *ctrl* 和 *shift* 来进行多选操作了；

3. 由于在安装容器的时候设置了管理目录为宿主机的 `/` 路径，所以每次访问 WebUI 默认路径都是此路径，要想更换路径的话，软件并没有相应的设置，但是我们可以曲线救国，首先去到我们想默认显示的路径下。例如：`/mnt/user/appdata/`，然后我们只要将当前页面（<http://192.168.1.223:8222/files/mnt/user/appdata/>）添加到浏览器的书签，这样以后就可以通过这个书签来访问此路径了。同样的，我们可以为不同的目录设置不同的书签，从而达到快捷访问的目的。

### Linux 下的安装

以下操作均建立在系统已经安装 Docker 的基础之上。

1. 拉取 Docker 镜像

    ```shell
    docker pull filebrowser/filebrowser:latest
    ```

2. 启动容器

    ```shell
    docker run \
        -v '/':'/srv' \
        -v '/mnt/user/appdata/filebrowser/':'/db/' \
        -p '8222:80/tcp' \
        filebrowser/filebrowser:latest \
        -d /db/database.db
    ```

### 参考

* <https://filebrowser.org/>

* <https://github.com/filebrowser/filebrowser>

* <https://hub.docker.com/r/filebrowser/filebrowser>
