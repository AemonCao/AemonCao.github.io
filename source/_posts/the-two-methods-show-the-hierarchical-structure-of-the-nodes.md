---
title: 两种方法显示节点的层次结构
tags:
  - WEB前端
  - CSS
  - JavaScript
url: 35.html
id: 35
categories:
  - WEB 前端
date: 2017-06-15 15:09:19
---

有时为了更好的看清网页的布局，仅仅通过看代码或者 _F12_ 的调试是不够的。这时候我们就需要一些「黑科技」。这里介绍两种方法，一种是通过 _JavaScript_ 来显示，另一种是通过 _CSS_ 样式来显示。

<!-- more -->

##  JavaScript 方法

```javascript
[].forEach.call($$("*"), function(a) {
    a.style.outline = "1px solid #" + (~~(Math.random() * (1 & lt; & lt; 24))).toString(16)
})
```

在 _F12_ 中输入此段 _JavaScript_ 代码，就可以显示各个节点的外部框架。

效果如下：

![js](https://ooo.0o0.ooo/2017/06/15/594230a99e355.png)

原网站：

![原网站](https://ooo.0o0.ooo/2017/06/15/594230a9285a6.png)

##  CSS 方法

```css
* {background-color: rgba(255, 0, 0, .2);}
* * {background-color: rgba(0, 255, 0, .2);}
* * * {background-color: rgba(0, 0, 255, .2);}
* * * * {background-color: rgba(255, 0, 255, .2);}
* * * * * {background-color: rgba(0, 255, 255, .2);}
* * * * * * {background-color: rgba(255, 255, 0, .2);}
```

只要在网站中使用这一段 _CSS_ 样式，就能看到如下效果：

![css](https://ooo.0o0.ooo/2017/06/15/594230a9a0f03.png)

##  总结

两种方法都有自己的优缺点，_JavaScript_ 的使用方便，但是只能适用于单个网站；而 _CSS_ 样式适用于数量多的网站（只需要在公共 _CSS_ 文件中加入这些代码就可以了）。

##  来源

> [代码来源-知乎](https://www.zhihu.com/question/27432017/answer/40621923)

> [代码来源-Quora](https://www.quora.com/What-are-the-most-interesting-HTML-JS-DOM-CSS-hacks-that-most-web-developers-dont-know-about/answer/Gajus-Kuizinas)
