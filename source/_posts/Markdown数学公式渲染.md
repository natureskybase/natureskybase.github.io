---
title: Markdown数学公式渲染：MathJax与KaTeX
date: 2026-03-08 12:00:00
tags: [Markdown, Hexo, KaTeX, MathJax]
categorie: 工具
description: |
  介绍为什么Markdown需要数学公式渲染器，对比MathJax与KaTeX的特点，并说明如何在Hexo中配置KaTeX渲染器。
---

# Markdown 数学公式渲染

## 为什么需要渲染器

Markdown 本身是一种轻量级标记语言，其设计目标是文本格式化，**并不原生支持数学公式**。

标准 Markdown 语法能处理的是文字、列表、代码块、链接等结构，但对于数学符号，它无能为力。比如下面这些内容：

- 希腊字母：$\alpha$、$\beta$、$\Sigma$
- 上下标：$x^2$、$a_n$
- 分数：$\dfrac{a}{b}$
- 积分：$\int_0^\infty f(x)\,dx$
- 矩阵：$\begin{pmatrix} a & b \\ c & d \end{pmatrix}$

这些符号如果直接写在 Markdown 里，渲染引擎会把 `$...$` 当成普通文本输出，或者把下划线 `_` 解析成斜体，导致公式完全乱掉。

### 根本原因

Markdown 解析器只处理文本结构，不理解 LaTeX 语法。数学公式本质上是 **LaTeX 语法**，需要专门的数学渲染引擎来：

1. 识别 `$...$`（行内公式）和 `$$...$$`（块级公式）定界符
2. 将 LaTeX 语法解析成对应的数学符号树
3. 将符号树渲染成 HTML + CSS 或 SVG 输出到页面

---

## MathJax 与 KaTeX 对比

目前主流的两个数学渲染引擎是 **MathJax** 和 **KaTeX**。

### MathJax

MathJax 是老牌渲染引擎，支持范围极广。

**优点：**
- 支持几乎所有 LaTeX 数学命令
- 支持 AsciiMath 和 MathML 输入格式
- 渲染结果非常精准，接近 LaTeX 原生排版

**缺点：**
- 体积大，加载慢（完整包约 1MB+）
- 渲染是 JavaScript 运行时完成，页面加载后才渲染，会有短暂的公式闪烁

### KaTeX

KaTeX 是 Khan Academy 开发的渲染引擎，专注于速度。

**优点：**
- 渲染速度极快，比 MathJax 快 10~100 倍
- 支持服务端渲染（SSR），可以在构建时生成 HTML，无需客户端 JS
- 体积更小，页面无闪烁

**缺点：**
- 支持的 LaTeX 命令集比 MathJax 少（但覆盖了绝大多数常用命令）
- 部分复杂宏或环境不支持

### 如何选择

| 场景 | 推荐 |
|------|------|
| 需要最广泛的 LaTeX 支持 | MathJax |
| 静态博客、追求加载速度 | **KaTeX** |
| 服务端渲染 | **KaTeX** |

对于 Hexo 这类静态博客，**KaTeX 是更好的选择**。

---

## 在 Hexo 中使用 KaTeX

### 1. 卸载默认渲染器

Hexo 默认使用 `hexo-renderer-marked`，它会干扰数学公式中的特殊字符（如 `_`、`\`），需要先替换掉。

``` bash
npm uninstall hexo-renderer-marked
npm install hexo-renderer-markdown-it-plus
# 或者 hexo-renderer-markdown-it
```

### 2. 配置 `_config.yml`

在 Hexo 根目录的 `_config.yml` 中添加：

``` yaml
math:
  ...
  katex:
    enable: true
```

如果使用的是主题（如 ParticleX 等），还需要在主题配置中开启 math 支持：

``` yaml
# _config.particlex.yml 或主题配置
math:
  enable: true
```

### 3. 写公式

行内公式用单个 `$` 包裹：

```
质能方程 $E = mc^2$ 是物理学的基础。
```

块级公式用 `$$` 包裹：

```
$$
\int_{-\infty}^{+\infty} e^{-x^2} dx = \sqrt{\pi}
$$
```

渲染效果：

$$
\int_{-\infty}^{+\infty} e^{-x^2} dx = \sqrt{\pi}
$$

### 常见问题

**下划线冲突**：Markdown 把 `_` 解析为斜体，导致 `a_b_c` 变成 `a<em>b</em>c`。解决方法是换用支持数学模式的渲染器（如 pandoc），或在公式中用 `\_` 转义。

**反斜杠被吃掉**：部分渲染器会把 `\\` 解析成 `\`，导致换行符丢失。pandoc 渲染器对此处理更好。

**构建时渲染 vs 运行时渲染**：KaTeX 支持在 `hexo generate` 时直接把公式渲染成 HTML，这样部署后的页面不需要加载任何 JS 就能显示公式，速度最快。
