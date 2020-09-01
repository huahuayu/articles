[//title]: (linux安装oracle-jdk-1.8)
[//englishtitle]: (install-oracle-jdk1.8-in-linux)
[//category]: (java,linux)
[//tags]: (java,linux)
[//createtime]: (20200901)
[//updatetime]: (20200901)

## 概述

本文介绍 Oracle JDK 1.8 (java 1.8) 在 linux 下的安装方法。在 CentOS/Ubuntu 中默认安装或者通过 yum/apt 安装的 JDK 均为 openJDK, 如需安装 Oracle JDK 需要去 Oracle 官网下载，手动安装。

假设安装目录为`/usr/local/`，shell 为 `bash`。

## 下载

[[JAVA 1.8 下载连接]](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
选择**jdk-8u261-linux-x64.tar.gz**下载。

## 上传解压

将下载的 jdk-8u261-linux-x64.tar.gz 上传至服务器, 放入`/usr/local/`目录, 然后解压。

```bash
$ tar -zxvf jdk-8u261-linux-x64.tar.gz
$ cd jdk1.8.0_261 && pwd
/usr/local/jdk1.8.0_261
```

## 配置环境变量

### 全局安装, 所有用户均生效

将下面的内容添加入 `/etc/profile` 文件的末尾。

```bash
vim /etc/profile
```

```bash
export JAVA_HOME=/usr/local/jdk1.8.0_261
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

添加完毕之后执行：

```bash
source /etc/profile
```

### 当前用户生效

将下面的内容添加到 ~/.bash_profile 文件的末尾。 若无法修改 ~/.bash_profile 文件，则将下面的内容添加到 ~/.bashrc 文件的末尾。

```text
vim ~/.bash_profile
或  vim ~/.bashrc
```

```bash
export JAVA_HOME=/usr/local/jdk1.8.0_261
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

添加完毕之后执行：

```bash
$ source ~/.bash_profile
若添加的内容到~/.bashrc文件，则执行 $ source ~/.bashrc
```

## 检查 java 版本

```bash
$ java -version
$ java version "1.8.0_261"
$ Java(TM) SE Runtime Environment (build 1.8.0_261-b13)
$ Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
```

安装成功！
