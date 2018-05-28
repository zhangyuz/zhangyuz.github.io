---
title: Pelican 入门
date: 2016-12-18 11:58
categories: 
    - Pelican
tags: 
    - Pelican

---

# Pelican 入门
[http://docs.getpelican.com/en/stable/index.html](http://docs.getpelican.com/en/stable/index.html) 

## Pelican 安装
`pip install pelican markdown typogrify beautifulsoup4`
安装 `typogrify` 和 `beautifulsoup4`是为了后面使用 `render_math` 插件生成数学公式使用。
> 后来发现 `render_math` 在最新版本的 Pelican 不工作。需要使用 `python-markdown-math` 来帮助实现。

`pip install python-markdown-math`
[参考](https://github.com/mitya57/python-markdown-math)

`pip install ghp-import` 用于发布网页到github。

## 使用pelican-quickstart快速生成网站框架
运行pelican-quickstart命令快速交互式创建网站框架。
~~~bash
$ pelican-quickstart 
Welcome to pelican-quickstart v3.7.0.

This script will help you create a new Pelican-based website.

Please answer the following questions so this script can generate the files
needed by Pelican.

    
> Where do you want to create your new web site? [.] 
> What will be the title of this web site? Running Out of Memory   
> Who will be the author of this web site? Zhang Yuz
> What will be the default language of this web site? [en] zh
> Do you want to specify a URL prefix? e.g., http://example.com   (Y/n) 
> What is your URL prefix? (see above example; no trailing slash) https://zhangyuz.github.io 
> Do you want to enable article pagination? (Y/n) 
> How many articles per page do you want? [10] 8
> What is your time zone? [Europe/Paris] Asia/Shanghai
> Do you want to generate a Fabfile/Makefile to automate generation and publishing? (Y/n) 
> Do you want an auto-reload & simpleHTTP script to assist with theme and site development? (Y/n) 
> Do you want to upload your website using FTP? (y/N) 
> Do you want to upload your website using SSH? (y/N) 
> Do you want to upload your website using Dropbox? (y/N) 
> Do you want to upload your website using S3? (y/N) 
> Do you want to upload your website using Rackspace Cloud Files? (y/N) 
> Do you want to upload your website using GitHub Pages? (y/N) y
> Is this your personal page (username.github.io)? (y/N) 
Done. Your new project is available at /home/yuz/YuZ/zhangyuz.github.io/running-out-of-memory
~~~

## 编写内容
### 文章与页面

Pelican把文章（articles）看做时间连续的内容，比如发布一篇blog，因此它和某个日期是相关的。
页面(pages)背后的意义是这样的：他们通常不是短期存在的，目的是保存那些不经常改变的内容（比如：About页面、Contact页面）。
### 文件元信息（File metadata）

Pelican想尽可能只能的从你的文件系统（译注：此处文件系统是指文件，比如目录的名字）获取它需要的信息（比如，你文章的类别），但是你还是需要在你文件的内部以元信息的形式提供文章信息。
>我本人使用 markdown 写作，所以下面只涉及 markdown 和 HTML 语法的元信息写法

使用 Markdown 写作的元信息语法：
~~~markdown
Title: My super title
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: Python
Tags: pelican, publishing
Slug: my-super-post
Authors: Alexis Metaireau, Conan Doyle
Summary: Short version for index and feeds

This is the content of my super blog post.
~~~

使用HTML编写的元信息语法：
~~~html
<html>
    <head>
        <title>My super title</title>
        <meta name="tags" content="thats, awesome" />
        <meta name="date" content="2012-07-09 22:28" />
        <meta name="modified" content="2012-07-10 20:14" />
        <meta name="category" content="yeah" />
        <meta name="authors" content="Alexis Métaireau, Conan Doyle" />
        <meta name="summary" content="Short version for index and feeds" />
    </head>
    <body>
        This is the content of my super blog post.
    </body>
</html>
~~~

除了文章标题（title）以外的元信息都是可选的：如果没有指定日期（Date）并且 `DEFAULT_DATE `设置为 `fs` ，Pelican 就使用这个文件的 mtime 属性作为日期。文章的分类（Category）可以使用包含文章的目录来确定。如果你使用其他的方式组织你的文章目录结构，目录的名字不适合作为分类的名字，你可以设置 `USE_FOLDER_AS_CATEGORY` 为 `False` 。在解析元信息中的日期（Date）时，Pelican 支持的是 W3C 的 [ISO8601](https://www.w3.org/TR/NOTE-datetime) 。

> 在试验不同的设置（尤其涉及到元信息）时，缓存可能影响是修改使修改无法生效。在这种情况下，可以设置 `LOAD_CONTENT_CACHE = False` 来禁用缓存或者使用 `--ignore-cache` 命令行开关。

modified 应该是你最后更新文章的时间，如果不指定，默认设为 date。除了在模板中显示 modified　时间以外，如果把 modified时间设置为当前时间，rss/atom订阅中文章的时间也会自动更新（译注：订阅者也会收到更新提醒）。

authors 是逗号分割的文章作者列表。如果只有一位作者，你可以使用 author 代替 authors。

如果不指定 summary 信息，`SUMMARY_MAX_LENGTH` 设置用来指定默认使用文章起始多少字作为文章的 summary。

你可以使用 `FILENAME_METADATA` 设置正则表达式，从文件名字中抽取出任何文章的元信息。任何符合正则表达式的命名字段都会被设置到元信息对象中。`FILENAME_METADATA` 的默认值仅仅从文件名字中获取`date`信息。例如，如果你想从文件名字中抽取 `date` 和 `slug`， 你可能需要把 `FILENAME_METADATA` 设置成 `'(?P<date>\d{4}-\d{2}-\d{2})_(?P<slug>.*)'`

注意，你文中设置的元信息比从文件名字中抽取的元信息优先级更高，会被优先使用。

### 页面（Pages）

如果在 `content`　目录下创建名为 `pages`　的目录，其中的所有文件都会被用来创建静态页面，比如 `About`、`Contact`页面。

使用 `DISPLAY_PAGES_ON_MENU` 设置来控制是否所有静态页面都显示在导航菜单中（默认为 `True`）。

如果希望设置某个文件既无法被链接到也不在导航菜单中显示，可以在文中元信息增加 `status: hidden` 属性。这个属性在制作错误页的时候比较有用。

### 内部资源链接

使用 `{filename}path/to/file` 来链接内部内容（==**译注：指的是 `content` 目录中的内容**==）。注意前面的`/` 是 `{filename}` 指令的所必须的分隔符。（译注：这里的内容指的你写作的文章，rst或md文件等）

例如，你的Pelican项目结构如下：
```
website/
├── content
│   ├── category/
│   │   └── article1.rst
│   ├── article2.md
│   └── pages
│       └── about.md
└── pelican.conf.py
```

`article1.rst` 中的内容可能如下：
```rst
The first article
#################

:date: 2012-12-01 10:02

See below intra-site link examples in reStructuredText format.

`a link relative to the current file <{filename}../article2.md>`_
`a link relative to the content root <{filename}/article2.md>`_
```

`article2.md` 中的内容：
```
Title: The second article
Date: 2012-12-01 10:02

See below intra-site link examples in Markdown format.

[a link relative to the current file]({filename}category/article1.rst)
[a link relative to the content root]({filename}/category/article1.rst)
```
==**（译注：注意上面路径中相对于当前文件和相对于 `content` 目录文件路径的不同写法。）**==

#### 静态文件链接

链接到非文章、非页面内容液使用前文提到的 `{filename}` 语法。重点是除非包含这些文件的目录被包含在项目配置文件 `pelicanconf.py` 的 `STATIC_PATHS`设置中，否则这些文件是不会自动被复制到 `output` 目录的。Pelican的默认设置包含 `images` 目录，但是其他目录必须手动添加。忘记添加设置将会导致链接无效。

例如，你项目的 content 目录结构如下：
```
content
├── images
│   └── han.jpg
├── pdfs
│   └── menu.pdf
└── pages
    └── test.md
```

`test.md` 可能写作如下：
```
![Alt Text]({filename}/images/han.jpg)
[Our Menu]({filename}/pdfs/menu.pdf)
```

`pelicanconf.py` 应该包含如下内容：
`STATIC_PATHS = ['images', 'pdfs']`

网站生成过程中，会把`han.jpg` 复制到 `output/images/han.jpg`， `menu.pdf` 复制到 `out/pdfs/mnu.pdf`, 并根据test.md生成合适的链接。

#### 在同一个目录中混合内容（Mixed content in the same directory）

从 Pelican3.5 开始，静态文件和页面源码文件可以放在相同的目录中，无需担心会在生成的站点中暴漏源码。对于这种混合的目录，必须同时添加到 `STATIC_PATHS`和 `PAGE_PATHS`（或者 `STATIC_PATHS` 和 `ARTICLE_PATHS`），这样 Pelican 就可以正常处理其中的页面源码并且复制其他文件（就像他们在独立的目录中一样）。

注意：把静态文件和源码内容放在相同的目录并不能保证它们在生成的站点中仍然位于同一个目录。为了达到这样的效果，最简单的方法是使用 `attach`语法。作为一种选择， `STATIC_SAVE_AS`、`PAGE_SAVE_AS` 和 `ARTICLE_SAVE_AS` （连同相应的 `*_URL` 设置）这些设置实现把不同类型的内容放在相同的目录中的目的，这个与 Pelican 之前的版本是一致的。

#### 附件静态文件（Attaching static files）

从 Pelican3.5开始，使用 `{attach}path/to/file` 语法可以（attached）静态文件到静态页面或文章中。这和 `{fileneme}` 语法效果类似，但是同时会把静态文件重定位到链接文件的输出目录。如果被链接的文件位于使用链接的文件的目录的下级目录中，那么也会在输出目录中保持相同的目录结构；否则，静态文件会被放到与链接它的文件相同的目录。

本语法仅适用于静态文件，并且他们位于 `STATIC_PATHS` 所设置的目录中。

例如，某个项目的 content 目录结构如下：
```
content
├── blog
│   ├── icons
│   │   └── icon.png
│   ├── photo.jpg
│   └── testpost.md
└── downloads
    └── archive.zip

```

`pelicanconf.py‘ 应该包含如下内容：
```
PATH = 'content'
STATIC_PATHS = ['blog', 'downloads']
ARTICLE_PATHS = ['blog']
ARTICLE_SAVE_AS = '{date:%Y}/{slug}.html'
ARTICLE_URL = '{date:%Y}/{slug}.html'

```

`testpost.md` 写作：
```
Title: Test Post
Category: test
Date: 2014-10-31

![Icon]({attach}icons/icon.png)
![Photo]({attach}photo.jpg)
[Downloadable File]({attach}/downloads/archive.zip)
```

那么生成的输出目录结构如下：
```
output
└── 2014
    ├── archive.zip
    ├── icons
    │   └── icon.png
    ├── photo.jpg
    └── test-post.html
```

对于比链接多次的静态文件，`{attach}` 只在第一次处理到它时生效一次，之后把它与 `{filename}` 做相同的处理。这样可以避免破坏已经存在的链接。

多个链接到同一个文件要格外注意：因为 Pelican在第一次处理 `{attach}` 标签的时候确定这个被链接文件重定位的为止，但是 Pelican 不保证每次处理文件的顺序都是相同的，所以多个链接到相同的静态文件可能在不同的编译结果中的位置也不一样（这个问题的出现和你的操作系统、文件系统、Pelican的版本、文件创建、修改和删除都有关系）。任何外部站点链接到旧的文件位置都可能因此链接无效。因此，建议只在所有使用相同链接的文档位于同一个目录使用 `{attach}`。这样，同一个文件的输出位置就不会改变了。对于这种前提条件不存在情况，建议使用 `{filename}` 代替 `{attach}`，让 `STATIC_SAVE_AS` 和 `STATIC_URL` 来确定被链接文件的生成位置（单个文件的 `save_as` 和 `url` 可以在 `EXTRA_PATH_METADATA` 中设置）。

#### 链接作者、类别、索引和标签

使用 `{author}name` `{category}foobar` `{index}` `{tag}tagname` 链接到作者、类别、索引和标签。

### 导入现有站点

参考 [Importing an existing site](http://docs.getpelican.com/en/stable/importer.html#import) ，支持 WordPress,Tumblr, Dotclear, RSS.

### 多语言（Translations）

Pelican 支持多语言的文章。为了达到目的，你血药添加 `lang` 属性到文章(article)、页面(pages)的元信息，设置 `DEFAULT_LANG`（默认为en）。设置好后，默认语言的文章会被显示在列表中，其他翻译的版本会连同原文一起显示。
> 注意：Pelican 不支持生成用于翻译子站点（比如：`example.com/de`）。参考[ i18n_subsites plugin](http://github.com/getpelican/pelican-plugins/tree/master/i18n_subsites)  来使用这种高级功能。

Pelican 使用 `slug` 来确定是否多篇文章之间具有翻译关系。用户可以在文章中手动设置`slug` 属性，否则 Pelican 根据文章标题自动生成`slug`。

例如下面英语和法语的两篇文章：
英语版：
```
Foobar is not dead
##################

:slug: foobar-is-not-dead
:lang: en

That's true, foobar is still alive!
```
法语版：
```
Foobar n'est pas mort !
#######################

:slug: foobar-is-not-dead
:lang: fr

Oui oui, foobar est toujours vivant !
```

尽管文章内容不同，但是它们两个仅有的共同点是 `slug`，功能是作为一个标识。如果你不想设置 `slug`，你就要保证它们的标题是完全一样，因为默认 `slug`是使用标题自动生成的。

如果不希望使用自动检测来确定哪一个是原始版本哪一个是翻译版，你可以用 `translation` 属性在元信息中指定。
```
Foobar is not dead
##################

:slug: foobar-is-not-dead
:lang: en
:translation: true

That's true, foobar is still alive!
```
### 语法高亮

Pelican 提供代码块的彩色高亮显示。使用下面的转换就可以实现此功能。
（译注：下面的翻译省略掉RST的部分，仅保留MD部分。）

Markdown中，Pelican 使用 [CodeHilite extension](http://pythonhosted.org/Markdown/extensions/code_hilite.html#syntax)  提供语法高亮功能，你需要在代码块上面使用语言标识，缩进语言标识和代码：
```
There are two ways to specify the identifier:

    :::python
    print("The triple-colon syntax will *not* show line numbers.")

To display line numbers, use a path-less shebang instead of colons:

    #!python
    print("The path-less shebang syntax *will* show line numbers.")
```

在[这里](http://pygments.org/docs/lexers/) 查看支持的语言标识。

### 发布草稿
如果希望把文章作为草稿发布（在正式发布前请朋友审阅），你可以在元信息中添加 `Status: draft` 属性。这样这篇文章会被输出到 `draft` 目录，不想是在索引和分类、标签页中。

如果你的文章应该以草稿的方式自动发布出去（不是意外的发布未完成的文章），就在 `DEFAULT_METADATA` 设置中包含这个状态：
```
DEFAULT_METADATA = {
    'status': 'draft',
}
```

如果想发布默认状态是 `draft` 的文章，设置文章的元信息 `Status: published` 即可。






## 发布网站

## 设置

## 创建主题

## 插件

##　pelican-themesd
