---
title: Git 删除 Submodule
date: 2017-01-12 23:03
categories: 
    - git

---

## 起因
自己搞 Github pages 的时候用了 Git 的子模块 （submodule），后来删除的时候发现 git 的 submodule 命令根本就没有提供删除功能，于是 Google 、百度，发现只能手动删除，总结如下。
# 实践
- 从 `.gitmodules` 中删除你想要删除的子模块相关的配置，我这里想删掉的就是这两个子模块，如下：
```
[submodule "output"]
       path = output
       url = git@github.com:zhangyuz/zhangyuz.github.io.git
[submodule "zhangyuz.github.io"]
       path = zhangyuz.github.io
       url = git@github.com:zhangyuz/zhangyuz.github.io.git
```
- 暂存对 `.gitmodules` 的修改：
```
git add .gitmodules
```
- 从 `.git/config` 中删除上面两个子模块相关配置：
```
[submodule "output"]
	url = git@github.com:zhangyuz/zhangyuz.github.io.git
[submodule "zhangyuz.github.io"]
	url = git@github.com:zhangyuz/zhangyuz.github.io.git
``` 
- 从仓库中删除相关记录:
```
git rm --cached output
git rm --cached zhangyuz.github.io
```
> 不要包含路径最后的斜杠 /。

- 删除仓库文件：
```
rm -rfv .git/modules/output/
rm -rfv .git/modules/zhangyuz.github.io/
```

- 现在这两个仓库变成了 untracked file， 删除即可：
```
rm -rf output/ zhangyuz.github.io/
```
- 提交刚刚的修改：
```
git commit --signoff
```

- 最后查看下刚刚的修改：
```
git show
......
diff --git a/.gitmodules b/.gitmodules
index 8fabe85..297ff0b 100644
--- a/.gitmodules
+++ b/.gitmodules
@@ -4,9 +4,3 @@
 [submodule "themes/pure"]
        path = themes/pure
        url = https://github.com/PurePelicanTheme/pure.git
-[submodule "output"]
-       path = output
-       url = git@github.com:zhangyuz/zhangyuz.github.io.git
-[submodule "zhangyuz.github.io"]
-       path = zhangyuz.github.io
-       url = git@github.com:zhangyuz/zhangyuz.github.io.git
Submodule output e4c24a3...0000000 (submodule deleted)
Submodule zhangyuz.github.io 7a72305...0000000 (submodule deleted)
```

收工睡觉～～～
