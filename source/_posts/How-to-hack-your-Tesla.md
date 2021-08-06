---
title: 如何黑入你的特斯拉
tags:
  - Tesla
  - Docker
  - 树莓派
categories:
  - Tesla
date: 2021-08-03 19:33:42
description: Life, the Universe and Everything
---

正题之前，先说一些半题外话：

提车到现在已经三个多月了，我对 Model 3 的感受就是好开。不过我是基本上没开过油车的。所以我不能说比油车好开多少，毕竟我驾照也是五年前学的，我已经忘了教练车开起来是什么感觉了。

买车的时候，正是刹车问题最沸沸扬扬的时候，不被影响是不可能的，不过还是抱着一颗支持人类太空计划的心选择了特斯拉（SpaceX 天下第一）。那时候也要很多朋友半调侃的提起这个问题，我都是笑着说：「是的啊，刹车根本没用，每次都是开门直接脚刹」。你要是问我有没有遇到刹车失灵的情况，说实话，到目前为止我都没踩过几脚刹车，单踏板模式太爽了。

### Tesla App

特斯拉有个官方的 [App](https://apps.apple.com/cn/app/tesla/id582007913)，下载后登录 Tesla 账号后就可以对车子进行控制，例如：空调、车窗、前后备箱、闪灯、鸣笛等等，甚至可以控制车子前进后退（极其缓慢的），方便在超挤的车位中进出。

虽然这个 APP 控制功能强大，但是可以查看的信息却是少得可怜，只能查看车辆的当前位置、当前电量、当前温度、总里程。基本上再没有其他信息了。

但其实这些信息，车辆都会进行记录，只是 APP 没有展示出来。特斯拉方面已经设计了一整套完善的车机接口，APP 只是调取了部分接口而已。你问我为什么特斯拉不开放？我也不知道，毕竟它做得到车内安装了 14 个喇叭却只启用 8 个；后排座椅硬件上支持座椅加热，却不开放后排座椅加热的开关。

### TeslaMate

国外已经有大神通过反向特斯拉的 App，取得了很多尚未开放的接口，我要介绍的就是其中一个叫做 [TeslaMate](https://github.com/adriankumpf/teslamate) 的项目。

它做到了通过调取 Tesla 的接口来获取更多的数据，并将其整合成图表，以实现例如：终身驾驶地图、驾驶效率报告、充电报告、电池退化统计等等等等。

其中最吸引我的就是终身驾驶地图这一功能，苹果手机的相册拥有地图功能，可以通过照片的拍摄地点来记录你所到过的地方，所以我每到一个新地方就会拍些照片，就好像游戏中打卡一样。我是那种喜欢记录过去的人，这功能简直直击我的心坎。不多说，就但这一个功能，我也要把它搭起来。

### 搭建

#### 搭在哪

TeslaMate 的文档是这么说的：

> A Machine that's always on, so TeslaMate can continually fetch data.

一台永远在线的机器，这样 TeslaMate 才能不断得获取数据。

那么现在我就有几个选择：

1. 自己的台式电脑；
2. 云服务器；
3. 树莓派。

开始是想安装在台式机上的，但是自从我上次由于 24 小时开机挖 ETH 导致电脑无故蓝屏后，我就不太敢长时间让它开机了。

其次是云服务器，这其实是最好的选择，永远在线（前提是💰足够），并且支持外网访问，这样你在世界各地都能看到自己的车辆数据，那没选择它的原因呢？就是因为💰不够。

最后就是树莓派了，选它的原因就是不想再看它吃灰了。而且之后配合公网 IP，以及 DDNS，也能实现和云服务器一样的效果。比起云服务器有个优点是，数据都是存在我自己的设备上，相对来说安全一些。所以就决定部署在树莓派上了。

#### 怎么搭

首先需要在树莓派上安装 [Docker](https://www.docker.com/)，当然如果部署在台式电脑或者云服务器上，也都是要安装 Docker 的。

Docker 在树莓派上的安装步骤如下：

1. 安装 Docker

    ```shell
    curl -sSL https://get.docker.com | sh
    ```

2. 为 `pi` 用户添加权限以运行 Docker 命令

    ```shell
    sudo usermod -aG docker pi
    ```

    之后重启一下，或者使用 sudo 来运行下一步的命令。

3. 测试 Docker 是否安装成功

    ```shell
    docker run hello-world
    ```

4. 十分重要！安装正确的依赖项

    ```shell
    sudo apt-get install -y libffi-dev libssl-dev
    ```

    ```shell
    sudo apt-get install -y python3 python3-pip
    ```

    ```shell
    sudo apt-get remove python-configparser
    ```

5. 安装 [Docker Compose](https://docs.docker.com/compose/)

    ```shell
    sudo pip3 -v install docker-compose
    ```

这样，准备工作就完成了。接下来开始安装 TeslaMate：

1. 新建一个名为 `docker-compose.yml` 的文件，内容如下：

    ```yml
    version: "3"

    services:
      teslamate:
        image: teslamate/teslamate:latest
        restart: always
        environment:
          - DATABASE_USER=teslamate
          - DATABASE_PASS=secret
          - DATABASE_NAME=teslamate
          - DATABASE_HOST=database
          - MQTT_HOST=mosquitto
        ports:
          - 4000:4000
        volumes:
          - ./import:/opt/app/import
        cap_drop:
          - all

      database:
        image: postgres:13
        restart: always
        environment:
          - POSTGRES_USER=teslamate
          - POSTGRES_PASSWORD=secret
          - POSTGRES_DB=teslamate
        volumes:
          - teslamate-db:/var/lib/postgresql/data

      grafana:
        image: teslamate/grafana:latest
        restart: always
        environment:
          - DATABASE_USER=teslamate
          - DATABASE_PASS=secret
          - DATABASE_NAME=teslamate
          - DATABASE_HOST=database
        ports:
          - 3000:3000
        volumes:
          - teslamate-grafana-data:/var/lib/grafana

      mosquitto:
        image: eclipse-mosquitto:2
        restart: always
        command: mosquitto -c /mosquitto-no-auth.conf
        ports:
          - 1883:1883
        volumes:
          - mosquitto-conf:/mosquitto/config
          - mosquitto-data:/mosquitto/data

    volumes:
      teslamate-db:
      teslamate-grafana-data:
      mosquitto-conf:
      mosquitto-data:
    ```

2. 使用 `docker-compose up` 命令启动 docker 容器，如果要在后台运行，可以添加 `-d` 参数：

    ```shell
    docker-compose up -d
    ```

好了，至此，属于你自己的 TeslaMate 已经搭建完成了。没错，由于 docker 的存在，搭建的过程还是很简单的。

### 使用

在部署完成后，我们可以访问：<http://192.168.2.121:4000> 这个网址。

这边的 IP 地址是我的树莓派的局域网 IP 地址，如果你使用的是云服务器，那就替换成云服务器的公网 IP 地址，并且要去控制台的安全策略组开放 4000 端口以及下面的 3000 端口。如果你是在台式电脑上部署的，那和树莓派一样，替换成你的台式机局域网 IP 地址就行了，或者可以使用 `127.0.0.1` 这个 IP 来访问。

进入后，使用你的特斯拉账号来进行登录，不出意外的话，你应该可以看到如下页面：

![web_interface.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/web_interface.png)

之后，你就可以访问 <http://192.168.2.121:3000> 来访问 [Grafana](https://grafana.com/) 仪表盘，Grafana 是一个跨平台、开源的数据可视化网络应用程序平台。它可以将 TeslaMate 中数据库的数据通过可视化图表的方式展示出来。

#### 驾驶细节（Drive Details）

例如你可以看到你每次行程的驾驶细节（Drive Details）

![drive.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/drive.png)

![race_track.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/race_track.png)

#### 驾驶统计（Drive Stats）

你的总历程数，你的总驾驶次数，你的总耗电量，你的预计每月里程，预计每年里程

![drive-stats.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/drive-stats.png)

#### 驾驶记录（Drives）

你的每一段行程的记录：起始位置，耗时，里程，开始电量，结束电量，温度，平均速度，耗电量：

![drives.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/drives.png)

#### 充电统计（Charging Stats）

可以查看你充电的统计（Charging Stats），例如每次充电的时间，是交流还是直流，你在各个地点充电的统计信息

![charging-stats.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/charging-stats.png)

#### 充电记录（Charges）

你的每一次充电的记录：时间，充电位置，充电时长，话费，增加的里程

![charging-history.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/charging-history.png)

#### 亏电记录（Vampire Drain）

每次驻车时的亏电记录

![vampire_drain.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/vampire_drain.png)

#### 更新记录（Updates）

每次系统更新的记录：更新开始时间，更新结束时间，系统版本

![updates.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/updates.png)

#### 预计里程（Projected Range）

你的车辆电池预计里程和总里程数以及时间的关系：

![projected-range.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@source/source/_posts/How-to-hack-your-Tesla/projected-range.png)

### 最后

需要注意几点：

1. 每一步操作前，务必知道自己在做什么；

2. 上面提供的安装方法，仅仅当在自己的家庭网络中部署时才推荐，如果想要将其暴露在互联网上（例如我提到的安装在云服务器上），请查看官方的[高级指南](https://docs.teslamate.org/docs/guides/traefik)；

3. 如果希望长久的使用，还是需要定时备份，备份的方法，请查看[这里](https://docs.teslamate.org/docs/maintenance/backup_restore)；

4. 此项目只能展示你部署之后车辆的信息，你部署之前的信息是无法获取到的；

5. <del>所有因为此文章，进行操作并导致车辆变砖的情况，本人概不负责；</del>

6. 请理性看待这一篇文章，这并不是一篇教授你如何入侵的教程，学会了这些你也**不能**启停其他特斯拉车主的车，也**不能**使他们的**刹车失灵**。

### 参考

* <https://github.com/adriankumpf/teslamate>

* <https://docs.teslamate.org/docs/installation/docker>

* <https://dev.to/rohansawant/installing-docker-and-docker-compose-on-the-raspberry-pi-in-5-simple-steps-3mgl>

* <https://www.bilibili.com/video/BV1u54y167BE>
