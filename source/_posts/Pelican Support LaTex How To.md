---
Title: Pelican Support LaTex How To
Date: 2017-01-02 10:35
Slug: Pelican Support LaTex How To

---

最近在自学 Andrew Ng 的机器学习课程，使用MD记笔记的时候发现，默认 `Pelican` 并不支持 `LaTex` 的数学公式。原来 `Pelican` 中默认数学公式是使用 `render_math` 这个插件来实现的，遂安装插件，然并卵。看了下，应该是 `Pelican`更新升级后 `render_math` 没有同步更新造成的。于是google之，对比几个方案后决定使用 Python Markdown的 [`mdx_math`](https://github.com/mitya57/python-markdown-math) 扩展 + 修改页面模板来实现。这应该是最简单的方案。

 “Shut the fuck up, show me the code”。

## 安装mdx_math
```
pip install python-markdown-math
```
选择 `mdx_math` 的原因无外乎就是社区活跃。看了其他几个扩展，最近的更新都已经块2年前了。

## Pelican 设置
增加 [`mdx_math`](https://github.com/mitya57/python-markdown-math) 扩展，由于默认不支持行内两个 $ 符号之间的公式，根据官方文档启用即可。修改如下：
```
diff --git a/pelicanconf.py b/pelicanconf.py
index 649aa9f..aef29da 100644
--- a/pelicanconf.py
+++ b/pelicanconf.py
@@ -37,6 +37,7 @@ MARKDOWN = {
         'markdown.extensions.headerid':{},
         'markdown.extensions.meta': {},
         'markdown.extensions.toc': {'title': 'ToC'},
+        'mdx_math': {'enable_dollar_delimiter': True},
     },
     'output_format': 'html5',
 }
```
## 修改页面模板增加MathJax支持
[MathJax](https://www.mathjax.org/)是支持浏览器页面显示数学公式的JavaScript引擎。

我使用 [`pure`](https://github.com/PurePelicanTheme/pure) 主题，其他主题应该也都包含 `base.html`， 修改也是大同小异。你也可以来[这里](https://github.com/zhangyuz/pure)获取我修改好的版本。
修改如下：
```
diff --git a/templates/base.html b/templates/base.html
index e407f94..fa80727 100644
--- a/templates/base.html
+++ b/templates/base.html
@@ -33,6 +33,16 @@
 
     <script src="//cdnjs.cloudflare.com/ajax/libs/jquery/2.0.3/jquery.min.js"></script>
     {% block head_js %}{% endblock %}
+
+    <script type="text/x-mathjax-config">
+        MathJax.Hub.Config({
+          config: ["MMLorHTML.js"],
+          jax: ["input/TeX", "output/HTML-CSS", "output/NativeMML"],
+          extensions: ["MathMenu.js", "MathZoom.js"]
+        });
+      </script>
+    <script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js"></script>
+
 </head>
 
 <body>
```

所有的修改就是这些。Let's check it out !!!

$E=MC^2$

$$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$$

> 部分官方文档：
>For inline math, use `\(...\)`.
>For standalone math, use `$$...$$`, `\[...\]` or `\begin...\end`.
>The single-dollar delimiter `($...$)` for inline math is disabled by default, but can be enabled by passing `enable_dollar_delimiter=True` in the extension configuration.
>If you want to render to span elements with inline math rather than script elements, so as to improve fallback when JavaScript is disabled or unavailable, use `render_to_span=True`.

