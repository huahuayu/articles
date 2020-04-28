[//title]: (ubuntu-apt使用国内源，享受飞一般的下载速度)
[//englishtitle]: (ubuntu-use-aliyun-apt-source)
[//category]: (linux,ubuntu,apt)
[//tags]: (linux,ubuntu,apt,aliyun)
[//createtime]: (20190428)
[//updatetime]: (20200428)

## 概述

ubuntu `apt`包管理器默认的软件源为`archive.ubuntu.com/ubuntu/` 或 `cn.archive.ubuntu.com/ubuntu/`

即下载软件都是要通过这两个网址下载。

使用官方的软件源安装软件无疑是最安全的，但是问题是下载速度慢，因为服务器有可能在国外，也有可能带宽不行。

在国内将其替换为阿里云的源速度会快得多，阿里云有大把资源可以支持，也算是对开源软件的贡献了。

本文就来简单介绍如何使用阿里云做 ubuntu `apt` 的软件源。

## 步骤

### step1: 备份

储存软件源列表的文件为`/etc/apt/sources.list`，修改前先将其备份，万一有问题还可以恢复回来

```bash
cp /etc/apt/sources.list /etc/apt/sources.list.bup
```

### step2: 修改配置文件

将`archive.ubuntu.com` 或者 `cn.archive.ubuntu.com`批量替换为`mirrors.aliyun.com`

```bash
vi /etc/apt/sources.list
```

在 vi 命令模式下使用`:%s/cn.archive.ubuntu.com/mirrors.aliyun.com/g`进行整体替换，`:wq` 保存退出

### step3: 更新软件包列表

使用`apt update`更新软件包列表。

以后再次使用`apt install`速度就会很快了。
