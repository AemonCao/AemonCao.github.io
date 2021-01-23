# Blog

基于 Hexo 的博客。

## 注意事项

每次 `npm i` 之后需要进行的操作，为了使 hexo 支持 Mathjax：

### 更改 `formatText`

去 `node_modules/hexo-renderer-kramed/lib/renderer.js` 将：

```JavaScript
// Change inline math rule
function formatText(text) {
    // Fit kramed's rule: $$ + \1 + $$
    return text.replace(/`\$(.*?)\$`/g, '$$$$$1$$$$');
}
```

修改为：

```JavaScript
// Change inline math rule
function formatText(text) {
    return text;
}
```

### 更改默认转义规则

去 `node_modules\kramed\lib\rules\inline.js` 中将第 `11` 行 `escape` 变量的值改为：

```JavaScript
escape: /^\\([`*\[\]()#$+\-.!_>])/,
```

同时要将第 `20` 行 `em` 变量的值改为：

```JavaScript
em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

### 更新 Mathjax 的 CDN 链接

去 `node_modules/hexo-renderer-mathjax/mathjax.html` 将 `<script>` 更改为：

```HTML
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML"></script>
```
