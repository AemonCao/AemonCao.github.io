---
title: 榨干这台 NAS 第 005 话-外网访问之 Tailscale
tags:
  - NAS
  - unRAID
  - Docker
categories:
  - '榨干这台 NAS'
date: 2024-06-12 14:46:13
description: 轻松搞定外网访问，带你玩转Tailscale，把你的NAS变成随身携带的小宝贝！（简介是 ChatGPT 生成的~）
---

这是一系列关于 NAS 的文章，系列的名称你们也看到了：「**榨干这台 NAS**」。我将尽可能详细的介绍 NAS 相关的知识，帮助你最大限度的发挥你的手中 NAS 的威力！

<!-- more -->

许久不见，托更了快两年，今天继续来填坑。

今天我们暂时先不捣鼓 PT、影视、下载那些问题了，我们来解决一个更加基础的问题：**外网访问**。

家用 NAS 作为家庭存储的设备，绝大多数场景是在家中使用的，但是有时候我们需要在外面访问家中的 NAS，比如在外面需要访问家中的文件、下载文件到家中的 NAS 等等。那么，如何实现外网访问呢？

目前主流的方式有以下几种：

1. **端口映射**：在路由器上设置端口映射，将 NAS 的端口映射到外网，这样就可以通过外网 IP 访问到 NAS。但是这种方式有一个很大的问题，就是**安全性**，端口映射后，NAS 的端口就暴露在了外网，这样就会有安全隐患。笔者曾经在暴露了 NAS 的多个端口后，被黑客入侵，所幸没有造成太大的损失。这方式在用户没有太多安全意识以及没有太多技术能力的情况下，不建议使用。当然它也有一些优点，比如速度快、稳定等；

2. **DDNS**：动态域名解析服务，这其实是第一种方式的一个补充，通过 DDNS 服务，我们可以通过一个域名访问到家中的 NAS，而不需要记住家中的 IP 地址。因为现如今家庭宽带即使分配了公网 IP 地址，也是动态的，所以我们需要 DDNS 服务来实现动态 IP 的映射。这种方式的优点是不需要记住 IP 地址，但是安全性和第一种方式一样；

3. **frp**：frp 是一个高性能的反向代理应用，支持 TCP、UDP、HTTP、HTTPS 等协议。frp 的原理是通过一个中转服务器，将外网的请求转发到家中的 NAS，这样就不需要在路由器上设置端口映射，也不需要 DDNS 服务。frp 的优点是不需要用户拥有公网 IP，它的缺点是其速度受限于用于部署 frp 中转服务器的带宽，而且 frp 服务需要一台服务器来搭建，这样就会增加一定的成本；

4. **VPN**：VPN 是一种通过加密通道将外网请求转发到家中的 NAS 的方式，VPN 的优点是安全性高，速度快，稳定性好，但是 VPN 的缺点是需要在外网设备上安装 VPN 客户端，这样才能访问家中的 NAS，而且 VPN 服务需要一台服务器来搭建，这样就会增加一定的成本。VPN 的另一个缺点是，VPN 服务一般都是收费的，如果你自己搭建 VPN 服务，那么你需要一台服务器，这样就会增加一定的成本；

5. **Tailscale**：Tailscale 是一个基于 WireGuard 的 VPN 服务，它的优点是简单易用，安全性高，速度快，稳定性好，由于使用 Tailscale 后，优先是进行直连，影响你传输速度的只有你的家庭宽带的带宽，而不是中转服务器的带宽。而且 Tailscale 服务是免费的，不需要自己搭建服务器，也不需要在外网设备上安装 VPN 客户端，只需要在家中的 NAS 上安装 Tailscale 客户端，然后在外网设备上安装 Tailscale 客户端，就可以访问家中的 NAS 了。Tailscale 的缺点是需要在访问端和被访问端都安装 Tailscale 客户端，但是这样也增加了安全性。

今天我们就来讲讲第 5 种方式：**Tailscale**。

其实还有一种方式，就是**NAS 厂商提供的远程访问服务**，比如 Synology 的 QuickConnect、QNAP 的 myQNAPcloud 等等，这种方式的优点是简单易用，但是本质上也是一种中转服务，只是它是由 NAS 厂商提供的，而不是用户自己搭建的。并且只支持品牌的 NAS，不支持自建的 NAS。

还有人会提到说 **ZeroTier**，ZeroTier 是一个类似 Tailscale 的 VPN 服务，但是 ZeroTier 我在使用的时候经常会出现连接不上的情况，并没有 Tailscale 那么稳定，所以我没有选择 ZeroTier。当然也可能是笔者的网络环境问题，你可以尝试一下。

#### Tailscale 的注册

首先，我们需要注册一个 Tailscale 账号，打开 [Tailscale 官网](https://tailscale.com/)，点击「Get Started」注册一个账号。

在 <https://login.tailscale.com/start> 中，选择你拥有的第三方账号进行登录，比如 Google、GitHub、Apple 等等。

#### Tailscale 的安装

安装分为两部分，一部分在外网设备上安装 Tailscale 客户端，一部分在家中的 NAS 上安装 Tailscale 客户端。

首先我们先在外网设备上安装 Tailscale 客户端，打开 <https://tailscale.com/download>，点击「Download」下载对应的客户端，然后安装。安装方式就不详细介绍了，很简单。

安装完成后，打开 Tailscale 客户端，登录你刚刚注册的 Tailscale 账号即可。你可以将其设置为开机启动。

然后我们在家中的 NAS 上安装 Tailscale 客户端，这里以 unRAID 为例，打开 unRAID 的「Apps」，搜索「Tailscale」，点击「Install」安装 Tailscale 客户端。

![unRAID-App-Tailscale.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@master/source/_posts/榨干这台NAS第005话-外网访问之Tailscale/unRAID-App-Tailscale.png)

docker 配置使用默认配置即可，然后点击「Apply」。

![Tailscale-docker-config.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@master/source/_posts/榨干这台NAS第005话-外网访问之Tailscale/Tailscale-docker-config.png)

安装完成后，我们需要打开 unRAID 的「Docker」，找到刚刚安装的 Tailscale 客户端，点击「Logs」，找到「logon URL」，然后在电脑上打开浏览器，输入这个 URL，然后登录你的 Tailscale 账号，点击「Auth」授权。

如此一来，你的家中的 NAS 就和你的外网设备连接在了一起，你可以在外网设备上访问家中的 NAS 了。

你可以将其当作是 TailScale 为你的外网设备和家中的 NAS 搭建局域网，这样你的外网设备就可以访问家中的 NAS 了。相反的，NAS 也可以访问外网设备。

#### TailScale 的使用

Tailscale 的使用非常简单，你可以在 [Tailscale 控制台](https://login.tailscale.com/admin/machines) 中看到你的设备和它的 IP 地址，你可以通过这个 IP 地址访问到你的设备。

![Tailscale-console.png](https://cdn.jsdelivr.net/gh/AemonCao/AemonCao.github.io@master/source/_posts/榨干这台NAS第005话-外网访问之Tailscale/Tailscale-console.png)

例如，你可以在外网设备上通过 `http://TailScale-IP:Port` 访问到家中的 NAS 上的服务，比如你可以通过 `http://TailScale-IP` 直接访问到 unRAID 的 WebUI。其他服务也是一样，并且无需设置端口映射。

#### 高阶使用 subnets

Tailscale 还有一个很强大的功能，就是 subnets，你可以在 Tailscale 控制台中设置 subnets，这样你就让你的外网设备访问到家中的局域网中的其他设备了。

比如你家中有一个打印机，你可以设置 subnets，让外网设备访问到这个打印机，这样你就可以在外网设备上通过这台打印机打印文件了。

具体设置方法可以参考 [Tailscale 官方文档](https://tailscale.com/kb/1019/subnets)。

#### 总结

通过上述的设置，你应该已经可以从外网访问到家中的 NAS 了，而且速度快、安全性高、稳定性好。如果你使用 TailScale 的时候发现速度并不能达到预期，或者连接速度慢，那么再进阶一步，可以考虑自建 Headscale 服务，这样你就可以自己控制中转服务器的带宽了。对于笔者来说，Tailscale 已经能够满足我的需求了，所以我没有自建 Headscale 服务。

希望这篇文章对你有所帮助，如果有任何问题，欢迎在评论区留言，我会尽力解答。

#### 参考

- [Tailscale 官网](https://tailscale.com/)

- [Tailscale 官方文档](https://tailscale.com/kb/)
