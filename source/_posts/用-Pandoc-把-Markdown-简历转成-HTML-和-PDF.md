---
title: 用 Pandoc 把 Markdown 简历转成 HTML 和 PDF
tags:
  - Pandoc
  - Markdown
  - 简历
categories:
  - WEB 前端
date: 2026-06-28 01:30:00
---

由于最近在找工作，需要重新整理一下简历。之前简历一般都是用 Word 或者在线平台写的，改起来总有一点不太顺手。既然简历本质上也是一份结构化文档，那不如直接用 Markdown 来写内容，再用 CSS 来控制样式，最后导出成 HTML 和 PDF。

<!-- more -->

这样做的好处是内容和样式可以分开。简历内容放在 Markdown 里，之后要改经历、技能、项目，只需要改文本；样式放在 CSS 里，想换字体、间距、颜色，也不会影响内容本身。

这篇就简单记录一下这次折腾的过程。

## 目录结构

我单独建了一个 `pandoc-workspace`，大致目录如下：

```text
pandoc-workspace
├── markdown
│   └── frontend_resume.md
├── styles
│   └── resume-calm-professional.css
├── outputs
│   ├── frontend_resume.html
│   └── frontend_resume.pdf
└── manifest.md
```

其中：

1. `markdown` 放简历源文件；

2. `styles` 放 CSS；

3. `outputs` 放最后生成的 HTML 和 PDF；

4. `manifest.md` 简单记录一下转换清单，免得过几天忘了哪个文件对应哪个样式。

## 写 Markdown

简历本身不用写得太复杂，正常使用标题、段落、列表和表格就够了。比如：

```markdown
# 张三
### Web Frontend Engineer

浙江杭州 · 1990.01
13800000000 · example@example.com

## 01 EXPERIENCE / 工作经历

### 某科技公司
**Frontend Engineer / 前端核心开发**
2021.04 — 2025.05

- 基于 Vue 3 + TypeScript + Pinia 搭建 AI 对话主链路
- 设计 Prompt Template + Zod Schema 的结构化输出方案
```

这里我没有追求很复杂的 Markdown 语法，主要是为了之后维护方便。简历这种东西，最重要的还是内容本身，样式只要清楚、稳定、专业就好。

## 写 CSS

这次简历内容偏前端工程、医疗 AI、IoT 管理后台这些方向，所以样式没有做得太花，而是用了比较冷静的文档风格。

大概思路是：

1. 页面居中，限制最大宽度；

2. 使用浅色背景和白色纸张区域；

3. 用一个偏冷的蓝绿色作为强调色；

4. 标题、公司、项目、关键价值之间拉开层级；

5. 加上 `@media print`，让打印成 PDF 时少一点意外。

类似这种：

```css
:root {
  --page-bg: #f3f5f7;
  --paper: #ffffff;
  --ink: #1f2933;
  --muted: #5f6f7f;
  --accent: #246b73;
}

body {
  max-width: 920px;
  margin: 36px auto;
  padding: 48px 64px;
  background: var(--paper);
  color: var(--ink);
  font-family: "Inter", "Segoe UI", "PingFang SC", "Microsoft YaHei", Arial, sans-serif;
}

h2 {
  margin: 2.35rem 0 1rem;
  padding: 0.72rem 0 0.45rem;
  border-top: 2px solid var(--accent);
  border-bottom: 1px solid #e5ebf0;
}
```

当然，完整 CSS 还是要根据自己的简历内容来调。比如项目经历很多，就要注意段落间距不要太大；奖项是表格，就要单独处理表格样式；如果要打印，就还要考虑分页。

## 转成 HTML

安装好 Pandoc 后，在项目根目录执行：

```powershell
pandoc `
  --from markdown+yaml_metadata_block+pipe_tables+hard_line_breaks+autolink_bare_uris `
  --to html5 `
  --standalone `
  --variable lang=zh-CN `
  --metadata pagetitle="张三" `
  --metadata title="" `
  --css ../styles/resume-calm-professional.css `
  --output outputs/frontend_resume.html `
  markdown/frontend_resume.md
```

这里有几个参数需要注意：

1. `--standalone` 会生成一份完整 HTML，而不是只有 body 片段；

2. `hard_line_breaks` 会保留 Markdown 里的换行，适合简历顶部的联系方式；

3. `autolink_bare_uris` 会把裸 URL 自动转成链接；

4. `--metadata title=""` 是为了避免 YAML 里的 `title` 被 Pandoc 再渲染一次，导致页面顶部出现两个姓名；

5. `--css ../styles/resume-calm-professional.css` 是因为 HTML 输出在 `outputs` 目录里，所以 CSS 路径要从 `outputs` 往上找。

如果本机没有安装 Pandoc，可以先去官网下载安装，或者临时用便携版也可以。

## 转成 PDF

一开始我想直接用 Pandoc 转 PDF，但 Pandoc 默认走 LaTeX 路线。对于这种已经写好 HTML + CSS 的简历来说，直接用浏览器打印成 PDF 会更接近预览效果。

Windows 下可以用 Chrome 的 headless 模式：

```powershell
& "C:\Program Files\Google\Chrome\Application\chrome.exe" `
  --headless=new `
  --disable-gpu `
  --no-first-run `
  --print-to-pdf="D:\Code\pandoc-workspace\outputs\frontend_resume.pdf" `
  "file:///D:/Code/pandoc-workspace/outputs/frontend_resume.html"
```

如果你装的是 Edge，也可以把路径换成：

```powershell
C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe
```

生成 PDF 前，最好先在浏览器里打开 HTML 看一遍。因为 PDF 本质上就是把这个页面打印出来，如果 HTML 本身有问题，PDF 也会跟着有问题。

## 遇到的两个小问题

### Pandoc 没有安装

一开始直接执行：

```powershell
pandoc --version
```

发现系统里没有这个命令。解决方法也简单，要么安装 Pandoc，要么临时下载一个 Windows 版的可执行文件，然后在命令里使用完整路径。

### 页面顶部出现重复标题

Markdown 文件开头有 YAML：

```yaml
---
title: "张三"
subtitle: "Web Frontend Engineer"
---
```

正文里又写了一次：

```markdown
# 张三
### Web Frontend Engineer
```

Pandoc 默认模板会把 YAML 里的 `title` 渲染到正文里，所以就重复了。我的处理方式是保留 YAML 给浏览器标题用，但在转换命令里加上：

```powershell
--metadata pagetitle="张三" `
--metadata title="" `
```

这样浏览器标题还是示例姓名，正文里也不会多出一份标题。

## 最后

这套方案不复杂，但是比较适合像我这样既想自己控制样式，又不想每次打开 Word 慢慢拖格式的人。

Markdown 负责内容，CSS 负责样式，Pandoc 负责转换，Chrome 负责打印 PDF。每一步都很明确，之后修改简历的时候，只要改 Markdown，再重新跑一遍命令就行了。

## 参考

1. <https://pandoc.org/MANUAL.html>

2. <https://developer.chrome.com/docs/chromium/headless>
