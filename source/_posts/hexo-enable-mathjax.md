---
title: 启用math支持
date: 2018-06-06 11:01:52
tags: Hexo
categories: Hexo

---

1. Next 主题中启用Math支持,默认使用 Mathjax 渲染：
```yaml
# theme/next/_config.yml
math:
  enable: true
  ...
  engine: mathjax
```
2. 使用 hexo-renderer-pandoc 渲染页面:
```shell
npm un hexo-renderer-marked --save
npm i hexo-renderer-pandoc --save
```
3. 重新生成网站、部署：
```shell
hexo clean
hexo d -g
```
4. 使用：
在 Next 主题中，必须把 LaTeX 公式放在 `equation` 环境中，而不是 Markdown 标准的两个 `$` 符号中间，比如：
```
$$\begin{equation}
e=mc^2
\end{equation}\label{eq1}$$
```
显示为：
$$\begin{equation}
e=mc^2
\end{equation}\label{eq1}$$

在文章的其他地方，可以用 label 来引用公式，比如：
```
著名的质能方程 $\eqref{eq1}$ 由爱因斯坦提出 ...

```
显示为：
著名的质能方程 $\eqref{eq1}$ 由爱因斯坦提出 ...


> 参考官方文档 [Math](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/MATH.md) 。
