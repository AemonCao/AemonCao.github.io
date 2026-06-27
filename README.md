# Blog

基于 Hexo 的博客。

## 注意事项

项目使用 Node.js 24.18.0 和 pnpm 11.8.0。安装依赖使用：

```bash
pnpm install
```

MathJax 由 `hexo-math` 在构建时渲染，不需要手动修改 `node_modules`。需要数学公式时使用 Hexo 标签：

```md
{% mathjax %}
\frac{1}{x^2-1}
{% endmathjax %}
```
