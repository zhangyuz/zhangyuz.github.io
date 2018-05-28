---
title: Git Submodules 学习笔记
date: 2016-12-24 13:40
tag: 
    - git
    - submodule
categories: 
    - git

---
# Git Submodules
译自[原文](https://git-scm.com/book/en/v2/Git-Tools-Submodules) ，有部分修改。

## 子模块 Submodules
在一个项目中使用其他的项目是一种常见的现象。或许是第三方开发的库或你自己为了在多个项目中使用而开发的单独的库。这些情况综合能够最常见的问题就是：你既想把他们作为两个独立的库对待又想在一个项目中使用例外一个。

假设你在开发一个网站，它包含Atom订阅功能。所以你决定使用第三方库而不是自己编写生成Atom订阅的代码。你的做法可能是使用CPAN、Ruby gem的共享库或是复制相应的代码到你的代码树中。直接使用共享库的问题你很难对库进行任何形式的定制，并且部署难度大，因为你要保证每个客户端都包含同样的库。把代码复制到自己的代码树的问题是你对它的任何修改定制都可能在上游代码修改后变得很难合并。

子模块就是为了解决这个问题。==子模块就是你可以在一个Git仓库的子目录中包含另一个Git仓库==。

## 开始使用子模块 Starting with Submodules

我们简单分析下这个分割为主项目和若干子项目的简单项目。

首先我们把一个现有的Git仓库作为子模块添加到我们正在工作的项目中。使用 'git submodule add' 命令，把你想要跟踪的仓库的绝对或相对路径作为参数，来添加子模块。本例中，我们添加一个成为 “DbConnector” 的库。
```
$ git submodule add  git@github.com:somename/somerepository.git
Cloning into 'somerepository'...
Warning: Permanently added the RSA host key for IP address '192.30.253.113' to the list of known hosts.
remote: Counting objects: 9, done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 9 (delta 0), reused 3 (delta 0), pack-reused 0
Receiving objects: 100% (9/9), done.
Checking connectivity... done.

```
默认，子模块被添加大与子项目同名的目录中，本例中名字为 “somerepository”。你也可以在名字末尾增加你想保存的子目录的路径。

这时运行 ‘git status’，你会发现入戏结果：
```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   .gitmodules
	new file:   somerepository
```
你首先会注意到新增的文件 '.gitmodules'。这是保存项目地址与本地子目录间映射关系的配置文件：
```
[submodule "somerepository"]
    path = somerepository
    url = git@github.com:somename/somerepository.git
```
如果你有多个子模块，配置文件中会包含多个这样的配置项。值得注意的是这个文件与其他文件比如 '.gitignore' 一样都是进行版本控制的。与项目中其他文件一样都会被 'push' 和 'pull'。这样别人使用这个仓库的时候就也知道去哪儿获得子模块了。

> 由于 '.gitmodules' 中仓库的地址是其他人首先尝试clone/fetch的地址，尽量保证这个地址可以被其他人访问到。比如，如果你push的地址与其他人pull的地址不同，使用那个其他人也可以访问到的地址。你可以使用 `git config submodule.somerepository.url PRIVATE_URL` 来重写这个地址来供你自己使用。有些时候，使用相对地址非常有用。

`git status` 命令的另外一个输出是项目的目录项。如果对这个目录运行 `git diff`，你会发现一些有趣的结果：
```
$ git diff --cached somerepository/
diff --git a/somerepository b/somerepository
new file mode 160000
index 0000000..e4c24a3
--- /dev/null
+++ b/somerepository
@@ -0,0 +1 @@
+Subproject commit e4c24a3fb6a10c451730553402acaa27caf5f682

```
虽然 somerepository 是一个目录，Git把它作为子模块处理，并且不跟踪它内部的内容。相反Git把它作为一个特殊的 commit 。
如果你想要更好看的 diff 的输出，可以给 `git diff` 传递 `--submodule` 参数。
```
$ git diff --cached --submodule
diff --git a/.gitmodules b/.gitmodules
new file mode 100644
index 0000000..8291bcf
--- /dev/null
+++ b/.gitmodules
@@ -0,0 +1,3 @@
+[submodule "somerepository"]
+       path = somerepository
+       url = git@github.com:somename/somerepository.git
Submodule zhangyuz.github.io 0000000...e4c24a3 (new submodule)

```
提交commit的时候可以看见：
```
$ git commit -am 'Add submodule somerepository'
[master 435c587] Add submodule somerepository
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 somerepository
```
==注意 somerepository 的 mode 是 `160000`。这时Git中一种特殊的 mode ，基本意思就是以目录的形式记录commit，而不是记录为子目录或文件。==

## Clone包含子模块的项目

1. 方式1：
	1. 克隆主项目：`git clone path/to/your/main/project` 克隆回本地的项目中会包含空的子模块，子模块中是空的。
	2. `git submodule init` 初始化本地配置。
	3. `git submodule update` fetch 子模块的所有文件，并且 checkout 合适的 commit。
2. 方式2：
	1. `git clone --recursive path/to/your/main/project`，它会自动初始化和更新其中的每个子模块。

## 使用包含子模块的项目

现在我们已经有了一个包含子模块的项目，我们在与团队成员合作开发主项目和子项目。

### 拉取上游修改
如果你只是一个子模块的消费者（既不对子模块贡献代码），并希望及时获得子模块的最新修改，你可以直接进入子模块的目录运行 `git fetch` 和 `git merge` 把上游代码合并到本地即可。
这时回到主项目，运行 `git diff`，你会发现主项目跟踪了子项目HEAD的commit的变化（有个名字叫submodule的子模块）：
```
$ git diff
diff --git a/submodule b/submodule
index a1f2285..6910783 160000
--- a/submodule
+++ b/submodule
@@ -1 +1 @@
-Subproject commit a1f228568aafe016a3e06e69f14abf211c124d49
+Subproject commit 6910783df605ced7860d8260dc87622c4904bba7

```
运行 `git diff --submodule`， 你可以看见具体子模块变更的commit。
```
$ git diff --submodule
Submodule submodule a1f2285..6910783:
  > Update readme
```
你可以设置 `git diff` 默认显示子模块的变化 `git config --global diff.submodule log`。
如果这时 commit 主项目的修改，那么别人更新代码后也会获得这个更新。

`git submodule update --remote path/to/submodule` ：自动更新子模块。

`git config -f .gitmodules submodule.asubmodule.branch stable` 设置子模块跟踪的分支。本例中设置 asubmodule 这个子模块默认跟踪 stable 这个分支。
`git submodule update --remote` 自动更新子模块。

`git config status.submodulesummary 1` 设置 `git status` 显示子模块修改的摘要信息。


















