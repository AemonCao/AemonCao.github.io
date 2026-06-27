---
title: 给 Hexo 博客换上 Giscus 评论系统
date: 2026-06-28 02:30:00
tags:
  - 博客
  - Hexo
  - Giscus
categories:
  - GitHub
description: 旧评论系统快到站了，是时候换一辆车。
---

之前博客用的是 Valine，后端数据放在 LeanCloud 上。这个组合用了好多年，基本上属于「配好之后就忘了它存在」的东西。

但是最近 LeanCloud 发了停止对外服务的公告，2027 年 1 月 12 日之后，数据读写、API、控制台这些都会停。也就是说，如果什么都不做，评论系统早晚会变成摆设。

所以今天来捣鼓一下：把旧的 Valine 评论换成 Giscus，新评论走 GitHub Discussions，旧评论则静态归档到页面里。

<!-- more -->

## 为什么选 Giscus

一开始也看了 Waline、Twikoo 之类的方案。它们都挺好，体验上也更接近 Valine，匿名评论、后台管理、邮件通知这些都能做。

但我这次想优先解决一个问题：**别再依赖一个单独的在线数据库服务了**。

Giscus 的思路很简单：每篇文章对应一个 GitHub Discussion，评论数据就存在 GitHub 里。我的博客本来就部署在 GitHub Pages 上，源码也在 GitHub，评论也放过去，心理负担比较小。

当然它也有缺点：读者必须登录 GitHub 才能评论。这个门槛不算低，但对我这个博客来说可以接受。

## 准备 GitHub 仓库

Giscus 配置之前，需要先确认三件事：

1. 仓库是公开的。
2. 仓库开启了 Discussions。
3. 安装了 giscus GitHub App。

Discussions 在仓库的 `Settings` 里打开。giscus App 则去 GitHub Marketplace 或 giscus 官网按提示安装，仓库权限选择当前博客仓库即可。

准备好之后，打开 giscus 的配置页，填入仓库：

```text
AemonCao/AemonCao.github.io
```

页面和 discussion 的映射关系我选的是：

```text
Discussion 的标题包含页面的 pathname
```

原因是 Hexo 的永久链接本来就长这样：

```text
/2020/12/25/whatisthisthing-5/
/2022/08/08/榨干这台NAS第000话-目录结构/
/about/
```

之前 Valine 导出的评论里，`url` 字段也是这个格式。用 `pathname` 作为绑定关系，比完整 URL 更稳。以后即使用了自定义域名，也不会因为域名变了导致评论分裂。

另外我勾选了严格匹配。博客里有很多类似 `whatisthisthing-9`、`whatisthisthing-10` 这样的标题，严格匹配可以减少 GitHub 搜索匹配错 discussion 的概率。

最后得到的关键配置大概是这样：

```html
<script src="https://giscus.app/client.js"
        data-repo="AemonCao/AemonCao.github.io"
        data-repo-id="MDEwOlJlcG9zaXRvcnkyNDQ1NDY0NjE="
        data-category="Announcements"
        data-category-id="DIC_kwDODpN7nc4DAA13"
        data-mapping="pathname"
        data-strict="1"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="preferred_color_scheme"
        data-lang="zh-CN"
        data-loading="lazy"
        crossorigin="anonymous"
        async>
</script>
```

## 接入 NexT 主题

我这个博客用的是 Hexo + NexT。当前主题本身内置了 Valine、Gitalk、Disqus 等评论系统，但是没有内置 Giscus。

所以做法是按 NexT 原来的评论注入方式补一个 Giscus。

先在主题配置里关掉 Valine：

```yml
valine:
  enable: false
```

然后新增 Giscus 配置：

```yml
giscus:
  enable: true
  repo: AemonCao/AemonCao.github.io
  repo_id: MDEwOlJlcG9zaXRvcnkyNDQ1NDY0NjE=
  category: Announcements
  category_id: DIC_kwDODpN7nc4DAA13
  mapping: pathname
  strict: 1
  reactions_enabled: 1
  emit_metadata: 0
  input_position: bottom
  theme: preferred_color_scheme
  lang: zh-CN
  loading: lazy
```

主题里再加两个东西：

1. 一个 filter，用来告诉 NexT：页面上要放一个 `#giscus-comments` 容器，页面底部还要加载 Giscus 脚本。
2. 一个 swig 模板，用来真正生成 Giscus 的 `<script>`。

这样每个开启评论的页面底部都会出现 Giscus 评论区。

## 旧评论怎么办

旧评论不建议直接导入 Giscus。

不是完全不能做，而是导入之后会有几个问题：

1. 所有旧评论都会变成同一个 GitHub 账号发布。
2. 原始评论时间不能真正变成 GitHub 评论时间。
3. 匿名昵称、邮箱、IP、UA 这些 Valine 元数据不适合搬进 GitHub。
4. 嵌套回复关系也很难做到完全一致。

所以最后我选了一个比较朴素的办法：**旧评论静态归档，新评论交给 Giscus**。

LeanCloud 导出的评论是 JSON streaming 格式，每一行是一条评论。先按 `url` 分组，然后只保留这些字段：

```json
{
  "id": "5ff983415d6b37098950665c",
  "nick": "nathanZheng",
  "link": "",
  "createdAt": "2021-01-09T10:19:45.434Z",
  "replyTo": "",
  "content": "我还以为昨天更新了， 原来是修改日期"
}
```

这些字段不会放进页面：

```text
ip
mail
ua
ACL
QQAvatar
```

评论内容也不再原样输出 HTML，而是转成纯文本，再交给模板转义。这样可以避免把旧评论里的用户 HTML 直接塞回页面。

最后把净化后的数据放到：

```text
source/_data/old_comments.json
```

再写一个 helper，通过当前页面的 `page.path` 找对应的旧评论。如果有旧评论，就在 Giscus 上方渲染一个「历史评论」区域；没有旧评论，就什么也不显示。

最终效果就是：

```text
文章正文
历史评论
Giscus 新评论
```

## 验证

改完之后跑一遍：

```bash
pnpm clean
pnpm build
```

我检查了几个页面：

1. 有旧评论的文章会显示「历史评论」。
2. `/about/` 这种独立页面也能显示旧评论。
3. 新文章没有旧评论区，只显示 Giscus。
4. 生成后的 HTML 里没有旧评论导出里的邮箱和 IP。

这样就算 LeanCloud 到期停服，旧评论也不会丢；新的评论则继续放在 GitHub Discussions 里。

## 参考

- LeanCloud 停止对外服务公告：<https://docs.leancloud.cn/sdk/announcements/sunset-announcement>
- Giscus 官网：<https://giscus.app/>
- Giscus GitHub 仓库：<https://github.com/giscus/giscus>
- GitHub Discussions 文档：<https://docs.github.com/discussions/quickstart>
- 启用 GitHub Discussions：<https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/enabling-or-disabling-github-discussions-for-a-repository>
