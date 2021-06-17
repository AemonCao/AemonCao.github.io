---
title: nvm use 命令不生效问题以及解决方法
date: 2021-06-17 11:47:14
tags: 
  - homebrew
  - node
  - nvm
  - yarn
categories:
  - WEB 前端
description: 解决问题是令人开心的。
---

今天启动项目的时候发现报错了，提示说 **node-sass** 的版本不适用当前版本的 **node**。**node-sass** 官网有一张表格，记录着 **node-sass** 和 **node** 的对应版本：

NodeJS  | Supported node-sass version | Node Module
--------|-----------------------------|------------
Node 16 | 6.0+                        | 93
Node 15 | 5.0+                        | 88
Node 14 | 4.14+                       | 83
Node 13 | 4.13+, <5.0                 | 79
Node 12 | 4.12+                       | 72
Node 11 | 4.10+, <5.0                 | 67
Node 10 | 4.9+, <6.0                  | 64
Node 8  | 4.5.3+, <5.0                | 57
Node <8 | <5.0                        | <57

我使用的 **node-sass** 版本为：

```json
"node-sass": "^4.12.0"
```

我记得我的 **node** 版本应该是 `v14.16.0`，应该不会有问题啊。

但是试了几次 `npm run serve`，都是报错，于是我只好查看了一下我的 **node** 版本：

```bash
> node --version
v16.3.0
```

`v16.3.0`？？？

我明明是 `v14.16.0` 啊，怎么变成 `v16.3.0` 了？我想起前几天好像是用 `nvm` 安装了一个当前最新的版本，可能那时候安装好没切换回来吧，于是我：

```bash
> nvm use v14.16.0
Now using node v14.16.0 (npm v7.15.1)
```

ok！easy，于是我愉快的 `npm run serve`，靠！怎么还是报错？

我再次查看了一下我的 **node** 版本：

```bash
> node --version
v16.3.0
```

怎么没切过去？出了什么问题？

上网查了好多资料，大多数都让我卸载 **nvm** 和 **node** 重新安装的。但是我不想把我的环境搞得一团糟，所以我一直在搜索有没有其他的的解决方案。

终于我看到一个方案是：看看有没有安装除了 **nvm** 安装的其他 **node**。

于是：

```bash
which -a node
```

果然，除了 **nvm** 下的 **node**，还有 **homebrew** 也有个 **node**。之后我又确认了一下：

```bash
brew list
```

确实，列表中有 node。之后我看了我的 `history`，因为我记得我没有用 **homebrew** 安装过 **node**，翻了一遍，确实没有，虽然很好奇，但是我还是准备动手删了它！

```bash
brew uninstall node
```

但是却提示：

> Error: Refusing to uninstall /opt/homebrew/Cellar/node/16.3.0
> because it is required by yarn, which is currently installed.

**yarn**？原来是你！我终于想到昨天下午为了搭建一个新项目的环境，我安装了 **yarn**，由于它是依赖于 **node** 的，所以 **homebrew** 顺便帮我安装了 **node**。知道了原因，接下来就好办了。

首先卸载 **yarn**：

```bash
brew uninstall yarn
```

然后卸载 **node**：

```bash
brew uninstall node
```

之后再重新安装 **yarn** 并加上忽略依赖项参数：

```bash
brew install yarn --ignore-dependencies
```

至此，**nvm** 和 **yarn** 终于能和谐共处啦🎉！
