---
title: ASF（ArchiSteamFarm） 挂卡指南-入门篇
tags:
  - ArchiSteamFarm
  - ASF
  - Linux
  - Steam
  - 喜加一
  - 挂卡
url: 300.html
id: 300
categories:
  - Linux
date: 2018-09-20 17:26:59
---

之前有写过 _Windows_ 版本的 _ArchiSteamFarm_ 在**树莓派**上的挂卡教程，那次过后到现在已经一年多了，_Steam_ 库又新进了批货，所以又要挂一次啦。这次呢，发现 _ArchiSteamFarm_ 已经更新到了 **3.3.6.0** 版本，所以就准备用最新的版本。还有就是由于众所周知的原因，国内很难正常的登录 _Steam_ ，所以这次挂机的平台从自家的树莓派转移到了 _Google Cloud_ 上。

<!-- more -->

## 准备

0. 最新版本的 _ArchiSteamFarm_ ，具体可以去[这里](https://github.com/JustArchi/ArchiSteamFarm/releases)下载，我用的是 **3.3.6.0** 版本（这个作者更新得很勤快）；

1. _Google Cloud_ 的云服务器一台，这个的话只要你有国际信用卡，就能免费领取一台一年的云服务器，从开始到现在只装了一个梯子，其他什么都没用，现在终于可以发挥一下余热了。

## 下载以及配置

0. 在最近的更新中，_ArchiSteamFarm_ 开始支持多平台，所以我们不用像以前那样麻烦得再安装 _Mono_ 了。首先先去下载对应的版本，可以去上面提到的[地址](https://github.com/JustArchi/ArchiSteamFarm/releases)，然后通过 _FTP_ 等方式放入云主机，也可以直接通过如下命令下载：

    ```shell
    wget https://github.com/JustArchi/ArchiSteamFarm/releases/download/3.3.0.6/ASF-linux-x64.zip
    ```

1. 然后解压文件：

    ```shell
    unzip ASF-linux-x64.zip
    ```

2. 之后需要开始调整配置文件，总共需要调整的配置文件一共有三个，第一个是位于根目录下的 `ArchiSteamFarm.runtimeconfig.json`，默认值是这样的：

    ```json
    {
        "runtimeOptions": {
            "configProperties": {
                "System.GC.Concurrent": true,
                "System.GC.Server": false
            }
        }
    }
    ```

    如果使用此配置来启动的话，在我的云服务器上会报如下错误

    > FailFast: Couldn't find a valid ICU package installed on the system. Set the configuration flag System.Globalization.Invariant to true if you want to run with no globalization support.

    为此我们要加上这么一句：

    ```json
    {
        "runtimeOptions": {
            "configProperties": {
                "System.GC.Concurrent": true,
                "System.GC.Server": false,
                // 防止运行报错
                "System.Globalization.Invariant": true
            }
        }
    }
    ```

    这个错误只在我这台谷歌云的云服务器上出现过，在其他的上面都是使用默认配置也能成功启动。所以可以暂时先不加这一句，如果启动时报错，可以加上这一句试一试。 第二处需要更改的配置文件是位于 `config` 文件夹下的 `ASF.json`，这部分的配置也不是必须的，主要是为了修改运行时的语言，默认的话都是系统设置的语言，而在 _Linux_ 系统上，基本上都是英语，所以要将其更改成中文的话，需要将其修改成如下配置：

    ```json
    {
        "AutoRestart": true,
        "Blacklist": [],
        "CommandPrefix": "!",
        "ConfirmationsLimiterDelay": 10,
        "ConnectionTimeout": 60,
        "CurrentCulture": "zh-CN",
        "Debug": false,
        "FarmingDelay": 15,
        "GiftsLimiterDelay": 1,
        "Headless": false,
        "IdleFarmingPeriod": 8,
        "InventoryLimiterDelay": 3,
        "IPC": false,
        "IPCPassword": null,
        "IPCPrefixes": [
            "http://127.0.0.1:1242/"
        ],
        "LoginLimiterDelay": 10,
        "MaxFarmingTime": 10,
        "MaxTradeHoldDuration": 15,
        "OptimizationMode": 0,
        "Statistics": true,
        "SteamMessagePrefix": "/me ",
        "SteamOwnerID": 0,
        "SteamProtocols": 5,
        "UpdateChannel": 1,
        "UpdatePeriod": 24,
        "WebLimiterDelay": 200,
        "WebProxy": null,
        "WebProxyPassword": null,
        "WebProxyUsername": null
    }
    ```

    在第 _7_ 行，将 `CurrentCulture` 的值改为 `zh-CN`，记得要加双引号。 第三处的话就是我们自己的账号配置文件了，如果你想以默认配置挂卡，你只需要复制一份 `config` 目录下的 `minimal.json`，使用最简配置就可以了：

    ```json
    {
        "Enabled": false,
        "SteamLogin": null,
        "SteamPassword": null
    }
    ```

    填入你的用户名和密码，并将 `Enabled` 的值改为 `true`：

    ```json
    {
        "Enabled": true,
        "SteamLogin": "Aemon",
        "SteamPassword": "QdXR@Fj%YEb#bA@du#$p7nYS6E1XemFY"
    }
    ```

    就可以了。 如果你嫌这样麻烦，并且怕自己粗心的话，可以去这个官方提供的[配置文件生成网站](https://justarchi.github.io/ArchiSteamFarm/#/)，来生成自己的配置文件。 如果想配置更多的功能，但又看不懂属性名称的话，你可以对着这份[配置说明](https://www.jianshu.com/p/a1459d1ca639)，来进行配置。 当然，如果你英语足够好，也可以参阅[官方的文档](https://github.com/JustArchiNET/ArchiSteamFarm/wiki/Configuration)。

## 运行

0. 在运行之前呢还需要一步，就是为 _ArchiSteamFarm_ 增加可执行权限，只要执行一下命令即可：

    ```shell
    chmod +x ArchiSteamFarm
    ```

1. 由于我们需要长时间挂机，需要 _ArchiSteamFarm_ 能在后台运行，所以我就开一个名为 _asf_ 的 _screen_ ：

    ```shell
    screen -S asf
    ```

    进入这个 _screen_ ：

    ```shell
    screen -r asf
    ```

2. 然后我们运行 _ArchiSteamFarm_ 即可：

    ```shell
    sudo ./ArchiSteamFarm
    ```

    根据你的设置不同，可能会提示你输入 _Steam 令牌_ 的五位代码，检查一下你绑定的邮箱或者是手机 _APP_ ，然后输入即可（不区分大小写）。 好了，现在就已经开始挂卡了，你可以按 `Ctrl` \+ `A` 和 `Ctrl` \+ `D` 来退出这个 _screen_ ，并退出终端，程序会一直运行的。

## 最后

最后祝大家挂卡顺利，早日回本。
