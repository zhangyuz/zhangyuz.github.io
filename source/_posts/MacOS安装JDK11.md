---
title: MacOS 安装 Jdk11,并在多 Jdk 间切换
date: 2020-02-12 12:03
categories: 
    - DailyNotes
tags:
    - MacOS
    - Jdk
    - 安装
    - Jdk切换

---

# MacOS 安装 Jdk11
1. 在 [此处](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) 下载Jdk 11。这是此时最新的 LTS 版本。

2. 解压缩安装
```shell
sudo tar -xvf jdk-11.0.6_osx-x64_bin.tar.gz -C /Library/Java/JavaVirtualMachines/
```

3. 查看是否安装成功
```shell
# 执行此命令
java -version
# 可以看见系统默认的 java 已经切换到了新版本
java version "11.0.6" 2020-01-14 LTS
Java(TM) SE Runtime Environment 18.9 (build 11.0.6+8-LTS)
Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.6+8-LTS, mixed mode)
```

4. 查看系统所有已安装的 Jdk
```shell
# 执行此命令
/usr/libexec/java_home -V
# 输出
Matching Java Virtual Machines (2):
    11.0.6, x86_64:	"Java SE 11.0.6"	/Library/Java/JavaVirtualMachines/jdk-11.0.6.jdk/Contents/Home
    1.8.0_192, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_192.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/jdk-11.0.6.jdk/Contents/Home
```

5. 在不同的 Jdk 版本之间切换：
```shell
# 切换到 Jdk1.8
export JAVA_HOME=$(/usr/libexec/java_home -v 1.8)
# 验证切换结果
java -version
# 已切换到 Jdk1.8
java version "1.8.0_192"
Java(TM) SE Runtime Environment (build 1.8.0_192-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.192-b12, mixed mode)
# 切换到 Jdk11
export JAVA_HOME=$(/usr/libexec/java_home -v 11)
# 验证切换结果
java -version
# 已切换到 Jdk11
java version "11.0.6" 2020-01-14 LTS
Java(TM) SE Runtime Environment 18.9 (build 11.0.6+8-LTS)
Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.6+8-LTS, mixed mode)
```

> 在第一步下载 Jdk 时网站会需要你登录。