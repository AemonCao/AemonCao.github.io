---
title: 使用 raspberry 配合 frp 来进行远程开机
tags:
  - 树莓派
url: 285.html
id: 285
categories:
  - Linux
  - 树莓派
date: 2018-07-19 22:56:39
---

0.  起因

    一直有在外操作家里电脑的需求，远程控制这一步好解决，一般是通过 _TeamViewer_ 来进行。但是前提之一是需要家中电脑时刻处于开机状态，但是由于电脑是台式即使待机也比平常笔记本的功率要高不少（可以看一下半个月的电费），而且如果 24 小时开机的话，散热器风扇一直处于运行状态，会使机箱的灰尘增多，这样的话清灰频率又要大大增加了。 ![5.png](https://i.loli.net/2018/07/19/5b507b12be317.png) 所以需要一种可以远程开机的办法。

1.  思路

    之前的方法是使用即时通讯软件让家中的室友帮忙开机，但是也只限于家中有室友的情况，如果室友去上班了，那就没办法了。 也想过训练猫来帮助我开机，但是奈何这猫实在太蠢，朽木不可雕也。而且就算是训练成功了，那当我需要开机时，我该怎么通知到猫呢？这也是一个问题。 在试验过各种方法后，最后我使用了树莓派（raspberry）结合 frp 的方式来完成我的需求。

2.  事先准备

    需要用到的设备有：

    0.  用来进行远程开机以及远程控制的设备一台；
        
    1.  具有 **IP/MAC绑定** 功能的路由器一台；
        
    2.  树莓派一台；
        
    3.  支持 WOL 的 PC 一台；
        
    4.  带有公网 IP 的服务器一台。
    

3.  具体操作

    0.  局域网设备配置

        首先是家中局域网的配置，PC 和 树莓派要位于同一局域网，然后在路由器中把两者的 MAC 和 IP 进行绑定。这里需要注意的是 MAC 不是代表一台设备而是一个网卡，所以在设置树莓派的 MAC 地址的时候需要根据当前树莓派连接路由器的方式来设置。我使用的路由器可以直接查看设备的 MAC 地址。如下： ![Routerlist.png](https://i.loli.net/2018/07/19/5b507b1a3f9aa.png) 如果路由器无法查看 MAC 地址或者设备太多无法区分，那么在 **Windows** 系统下可以使用

        ```bat
        ipconfig -all
        ```

        来查看 
        
        ![MACWindows.png](https://i.loli.net/2018/07/19/5b507b1a4128f.png) 
        
        在 **raspberry** 下可以使用

        ```shell
        ifconfig
        ```

        来查看
        
        ![MACraspberry.png](https://i.loli.net/2018/07/19/5b507b1a4292a.png)
        
        可以看到树莓派有两个 MAC 地址，由于我是使用无线连接所以我选择的是第二个 _wlan0_。 然后使用 **IP/MAC绑定** 功能将两台设备与 IP 进行绑定，绑定的时候建议就选择当前使用的 IP 以免用了其他设备正在使用的 IP，造成 IP 冲突。 如果绑定了其他的 IP，请在绑定成功后重启设备。

    1.  PC WOL 配置

        然后是要配置 PC 使其可以支持 WOL（wake-on-LAN）开机。WOL 是一种电源管理功能，中文译为 **网络唤醒**，以下是 Wiki 对其作出的解释：

        > Wake-on-LAN简称WOL或WoL，中文多译为“网络唤醒”、“远程唤醒”技术。WOL是一种技术，同时也是该技术的规范标准，它的功效在于让已经进入休眠状态或关机状态的电脑，透过局域网（多半为以太网）的另一端对其发令，使其从休眠状态唤醒、恢复成运作状态，或从关机状态转成引导状态。此外，与WOL相关的技术也包括远程下令关机、远程下令重启等相关的遥控机制。

        它的具体方法就是向要启动的设备发送一个魔法数据包（Magic Packet）：

        > 魔法数据包（Magic Packet）是一个广播性的帧（frame），透过端口7或端口9进行发送，且可以用无连接（Connectionless protocol）的通信协议（如UDP、IPX）来传递，不过一般而言多是用UDP，原因是Novell公司的Netware网络操作系统的IPX协议已经愈来愈少机会被使用。 在魔法数据包内，每次都会先有连续6个"FF"（十六进制，换算成二进制即：11111111）的数据，即：FF FF FF FF FF FF，在连续6个"FF"后则开始带出MAC地址信息，有时还会带出4字节或6字节的密码，一旦经由网卡侦测、解读、研判（广播）魔法数据包的内容，内容中的MAC地址、密码若与电脑自身的地址、密码吻合，就会启动唤醒、引导的程序。

        所以我们要先设置 BIOS 打开「**网卡唤醒**」这一功能，由于各个品牌主板的 BIOS 各不相同，所以设置的方法也各式各样，大家可以自行搜索「**wake on lan 设置**」，来寻找正确的方式。不过大多是在 **电源管理**（Power Management Setup）中。 然后是系统上的设置，这里我以 _Windows 10 17134.165_ 版本为例。 首先右键「**网络**」-「**属性**」来打开「**网络和共享中心**」面板： 
        
        ![4.png](https://i.loli.net/2018/07/19/5b507b129fdee.png)
        
        在左侧单击「**更改适配器设置**」-右键你现在正在使用的网卡-「**属性**」来打开「**属性**」面板：
        
        ![1.png](https://i.loli.net/2018/07/19/5b507b12983ac.png)
        
        单击上方的「**配置**」-选择「**高级**」选项卡-在属性类别中将「**关机 网络唤醒**」和「**魔术封包唤醒**」的值设置为「**开启**」：
        
        ![3.png](https://i.loli.net/2018/07/19/5b507b129f1e7.png)
        
        选择「**电源管理**」选项卡-勾选「**允许计算机关闭此设备以节约电源**」和「**允许此设备唤醒计算机**」选项：
        
        ![2.png](https://i.loli.net/2018/07/19/5b507b1298186.png)
        
        就此，PC 端的设置已经完成了。

    2.  带公网 IP 的服务器配置

        带公网 IP 的服务器，大家可以去阿里云或者腾讯云买一台最低配的就可以了，我的这台是之前在腾讯云薅羊毛薅的。 这个服务器的作用主要是运行 _frp_ 的服务端来使局域网内的树莓派可以内网穿透。 _frp_ 是一个免费的开源的内网穿透软件，而且部署简单方便。具体方式如下：

        我们可以在 [https://github.com/fatedier/frp/releases](https://github.com/fatedier/frp/releases) 下载指定架构下的版本，我在腾讯云服务器上使用的 _Ubuntu_ 系统，所以选择的是 `frp_0.20.0_linux_amd64.tar.gz` 这个版本。可以下载下来使用 _FTP_ 来放到服务器上，也可以在服务器上使用：
    
        ```shell
        wget https://github.com/fatedier/frp/releases/download/v0.20.0/frp_0.20.0_linux_amd64.tar.gz
        ```
        
        来直接下载到服务器上。
    
    3.  下载完成后使用：
    
        ```shell
        tar -zxvf frp_0.20.0_linux_amd64.tar.gz
        ```
        解压文件。
    
    4.  进入目录：
    
        ```shell
        cd frp_2.20.0_linux_amd64
        ```

    5.  通过 `rm` 命令来删除 `frpc` 和 `frpc.ini` 两个文件
        
        ```shell
        rm frpc frpc.ini
        ```
        
    6.  打开配置文件 `frps.ini`
        
        ```shell
        vim frps.ini
        ```    
        
    7.  更改配置如下：
        
        ```ini
        [common]
        bind_port = 7000           #与客户端绑定的进行通信的端口
        vhost_http_port = 6081     #访问客户端web服务自定义的端口号
        ```
        
        注：

        1.  「#」 后面的是注释，可以不写；

        2.  这边 _Vim_ 的用法可以上搜索引擎查一下，这里不多赘述。

    8.  然后启动服务：
        
        ```shell
        ./frps -c ./frps.ini
        ```
        
        这个是前台启动服务，会输出日志信息，是用来调试的用的，到时调试成功了就可以使用后台服务启动：
        
        ```shell
        nohup ./frps -c ./frps.ini &
        ```

至此，服务器也设置完成。

4.  树莓派配置

    我现在使用的是 _Raspberry 3B_，当时是在淘宝 _￥195_ 的价格买的，如果配上电源以及 _SD_ 卡的等配件一共是 _￥278.9_。清单如下： 
    
    ![list.png](https://i.loli.net/2018/07/19/5b507b12b3fed.png)
    
    之后的系统安装我就不在这详细说明了，网上有很多详细的教程。 树莓派的配置和服务器配置其实是差不多的，不同的是服务器上的部署的是 _frp_ 的服务端，而树莓派上的部署的是客户端。 从下载到解压的步骤和服务器端是一模一样的，只要照着之前的步骤做就可以了。 从第三步删除文件开始有所不同：

    0.  在服务器上我们删除的是 `frpc` 和 `frpc.ini`，这两个是 _frp_ 的客户端程序和客户端配置文件，同理我们在树莓派也就是服务器端上就要删除 `frps` 和 `frps.ini` 这两个文件：
    
        ```shell
        rm frps frps.ini
        ```
        
    1.  打开配置文件 `frpc.ini`
    
        ```shell
        vim frpc.ini
        ```
    
    2.  更改配置如下：
    
        ```ini
        [common]
        server_addr = 118.126.***.***
        server_port = 7000
        
        [ssh]
        type = tcp
        local_ip = 192.168.1.100
        local_port = 22
        remote_port = 6022
        
        [mysql]
        type = tcp
        local_port = 3306
        remote_port = 3306
        ```

        `server_addr` 即为服务器的公网 IP 的地址，`server_port` 为之前在服务端配置时的 `bind_port`，这里我用的是 `7000`。 然后是需要内网穿透的服务的配置，我这里写了两个，一个是 _SSH_，一个是 _MySQL_。如果只要能进行远程连接的话我们只需要 _SSH_ 的配置就好了，这里要注意的就是 `remote_port`，自定义的端口号，不要填 `22`，因为在服务器上已经被占用了（被用于服务器的 _SSH_），所以你要选一个没被占用的端口来使用，这里我用的是 `6022`。
    
    3.  然后启动服务：
    
        ```shell
        ./frpc -c ./frpc.ini
        ```
    
        同样的，后台服务启动是：
    
        ```shell
        nohup ./frpc -c ./frpc.ini &
        ```
    

5.  测试 _frp_

    现在我们可以在自己的 PC 上使用以下命令来访问树莓派了：

    ```powershell
    ssh -p 6022 118.126.***.***
    ```

    然后键入树莓派的密码就可以了：
    
    ![SSHraspberryPC.png](https://i.loli.net/2018/07/19/5b507b1a44208.png)
    
    当然也可以用手机的移动网络来访问：
    
    ![SSHPhone.png](https://i.loli.net/2018/07/19/5b507b1a5450c.png)
    
    至此我们已经成功的内网内网穿透了，即可以从外网访问内网设备了，接下来我们就要通过树莓派来使家中的 PC 开机了。

6.  在树莓派上使用 _WOL_ 控制 PC 开机

    这里我找了好多软件，测试了好久，不是没有 _Linux_ 平台的，就是不能用。所以最后还是用 _Python_ 了，具体代码如下：

    ```python
    #!/usr/bin/env python
    # -*- encoding: utf-8 -*-
    from __future__ import absolute_import
    from __future__ import unicode_literals
    
    import argparse
    import socket
    import struct
    
    BROADCAST_IP = '255.255.255.255'
    DEFAULT_PORT = 9
    
    def create_magic_packet(macaddress):
        if len(macaddress) == 12:
            pass
        elif len(macaddress) == 17:
            sep = macaddress[2]
            macaddress = macaddress.replace(sep, '')
        else:
            raise ValueError('Incorrect MAC address format')
    
        # Pad the synchronization stream
        data = b'FFFFFFFFFFFF' + (macaddress * 16).encode()
        send_data = b''
    
        # Split up the hex values in pack
        for i in range(0, len(data), 2):
            send_data += struct.pack(b'B', int(data[i: i + 2], 16))
        return send_data
    
    def send_magic_packet(*macs, **kwargs):
        packets = []
        ip = kwargs.pop('ip_address', BROADCAST_IP)
        port = kwargs.pop('port', DEFAULT_PORT)
        for k in kwargs:
            raise TypeError('send_magic_packet() got an unexpected keyword '
                            'argument {!r}'.format(k))
    
        for mac in macs:
            packet = create_magic_packet(mac)
            packets.append(packet)
    
        sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
        sock.setsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST, 1)
        sock.connect((ip, port))
        for packet in packets:
            sock.send(packet)
        sock.close()
    
    def main(argv=None):
        parser = argparse.ArgumentParser(
            description='Wake one or more computers using the wake on lan'
                        ' protocol.')
        parser.add_argument(
            'macs',
            metavar='mac address',
            nargs='+',
            help='The mac addresses or of the computers you are trying to wake.')
        parser.add_argument(
            '-i',
            metavar='ip',
            default=BROADCAST_IP,
            help='The ip address of the host to send the magic packet to.'
                 ' (default {})'.format(BROADCAST_IP))
        parser.add_argument(
            '-p',
            metavar='port',
            type=int,
            default=DEFAULT_PORT,
            help='The port of the host to send the magic packet to (default 9)')
        args = parser.parse_args(argv)
        send_magic_packet(*args.macs, ip_address=args.i, port=args.p)
    
    if __name__ == '__main__':  # pragma: nocover
        main()
    ```

    我们把上面的代码保存为 `*.py` 格式，例如 `wol.py` 然后通过 _ftp_ 传输到树莓派上去。 然后在存有这个 `wol.py` 的文件夹下使用：

    ```shell
    python wol.py E0:D5:5E:88:88:88
    ```

    `E0:D5:5E:88:88:88` 就是你需要启动的 PC 的 MAC 地址了。 回车后发现没有任何提示，这是正常了，因为在 _Linux_ 中，_没有消息就是好消息_。 如果你只需要启动一台 PC，而且你不想记录这么长的 MAC 地址（通常也不需要你记录，因为你可以在终端通过上下键来显示历史使用过的命令），你可以将你的 MAC 地址写入到代码中去，这样就可以一劳永逸了。 至此，你已经可以通过树莓派来启动你的 PC 了，快关闭你的电脑试一试吧。

7.  其他

    1.  事后，我用 _Wireshark_ 抓了包，找到了这个 _Magic Packet_：
    
        ![6.png](https://i.loli.net/2018/07/19/5b507b12b69cb.png)
    
        发现和 _Wiki_ 上说的一样：以 `6` 个 `FF` 开始，并且重复 `16` 遍 MAC 地址。
    
    2.  可以看到，我在树莓派上的 _frp_ 配置文件中有一个 _MySQL_ 的条目：
    
        ```ini
        [mysql]
        type = tcp
        local_port = 3306
        remote_port = 3306
        ```
        
        当如此设置后，并且在树莓派上安装 _MySQL_，就可以在外网用类似的方法来访问内网的数据库了。 同样的，我们知道，网站（HTTP）是通过 `80` 端口来传输的，由此如果我们在树莓派上有部署网站的话，那么就可以通过 _frp_ 进行类似的配置（这部分可能会有一些不同），我们就可以在外网访问该网站了。
    

8.  总结

    没有总结。
