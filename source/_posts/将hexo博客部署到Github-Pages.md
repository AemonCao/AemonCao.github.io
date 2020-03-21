---
title: 将hexo博客部署到Github Pages
date: 2020-03-16 21:50:55
tags:
---

本来博客是部署在自己的服务器上的，但是华为云的备案实在是太慢了，所以想着先部署到 [Github Pages](https://pages.github.com/) 上，这样至少不用每天顶着个服务器地址来访问了。

<!-- more -->

昨天（2020-3-17）下午备案终于通过了。但是这个 Github Pages 的部署过程还是要写一下的，毕竟学到了很多东西，我准备分两块来写，一部分是使用 hexo 搭建博客，一部分是当前你看到的这篇，如何将 hexo 博客部署到 Github Pages。

##  什么是 GitHub Pages？

往常，当你需要部署一个静态网站时，你需要一台有公网 IP 的服务器或者是虚拟主机，然后在上面通过配置 Nginx、Tomcat 或者 IIS 等网页服务器来部署你的网站。之后再通过域名的 DNS 设置，将你的域名指向到你的服务器地址。这样，当访问这个域名的时候，就能见到你的网站了。

如果你在中国，你还要经过较长且繁琐的备案程序，最终才能使你的网站可以访问。

而 GitHub Pages 则帮你省去了很多步骤，你甚至不需要域名，你就可以拥有属于自己的网站。

##  步骤

### 首先你需要有一个网站的源文件

我部署的网站是我的博客，就是你当前在浏览的这个网站，他是由 hexo 来生成的。接下来过程都是基于 hexo 的博客来写的。

当然，如果你对样式或者内容不满意，你也可以自己修改或者完全自己手搓一份。

### 然后你需要一个 GitHub 账号

你需要在 [GitHub 官网](https://github.com) 注册一个属于你自己的账号。你的用户名（Name），将决定你的 GitHub Pages 域名（ *你的GitHub用户名.github.io* ）。虽然之后也可以更改，但是更改用户名之后会有很多意想不到的问题出现，所以干嘛不一开始就做到最好，给自己取个好听又好记的名字吧。

### 新建 repository

在[这里](https://github.com/new)新建一个 repository，Repository name 就是上面说的 *你的GitHub用户名.github.io* ，我的就是 *AemonCao.github.io*。

点击 *Creating repository* 即可创建。

这样，你就得到了一个远程的 git 仓库，记下你的仓库地址：*https://github.com/AemonCao/AemonCao.git* ，这个地址可以在仓库页面，点击 *Clone or download* 来进行复制。

### 推送 Hexo 站点文件夹

文件夹中不应该包含 *node_modules* 和 *public* 文件夹，所以你需要将这两个文件夹写进你的 *.gitignore* 。

使用：

```shell
$ git init
```

来初始化你的 git 仓库；

然后进行第一次 commit：

```shell
$ git add .
```

```shell
$ git commit -m 'first commit'
```

添加远程仓库：

```shell
$ git remote add origin https://github.com/AemonCao/AemonCao.git
```

之后将本地仓库推送的 GitHub 上你刚刚建立的远程仓库中：

```shell
$ git push -u origin master
```

### 分支的操作

由于 GitHub 现在不允许将非主分支发布到 GitHub Pages，推送部署的目标只能是 *master* 分支。而我们上传到 *master* 分支的并不是最后需要部署的静态文件，所以我们需要新建一个名为 *source* 的分支：

```shell
$ git checkout -b source
```

它是下面两条命令的简写：

```shell
$ git branch source
$ git checkout source
```

意思是新建并切换到一个名为 *source* 的分支。



##  参考

*   [将 Hexo 部署到 GitHub Pages](https://hexo.io/zh-cn/docs/github-pages#Project-page)

*   [Getting started with GitHub Pages](https://help.github.com/en/github/working-with-github-pages/getting-started-with-github-pages)