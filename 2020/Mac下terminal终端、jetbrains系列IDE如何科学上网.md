[//title]: (Mac下terminal终端、jetbrains系列IDE如何科学上网)
[//englishtitle]: (mac-terminal-and-jetbrains-proxy-though-shadowsocks-pass-gfw)
[//category]: (mac,shadowsocks,gfw)
[//tags]: (mac,shadowsocks,gfw,terminal,jetbrains,proxy)
[//createtime]: (2020-04-28)
[//updatetime]: (2020-04-28)

## 概述

Mac 下的`ShadowsocksX-NG`只代理浏览器上网，即便选择了全局代理，其他应用也不会走代理的

但有时候我们需要某些应用也通过代理访问互联网（比如 github 访问慢甚至不能访问），那怎么实现呢？

本文通过 `terminal` 和 `jetbrains`系列 ide 两款软件来举例说明，因为技术人员经常有这个需要

## 环境介绍

macOS: 10.15.4

ShadowsocksX-NG: 1.9.4

## 原理介绍

`ShadowsocksX-NG`启动后会默认监听本地的 1087 端口，如果有请求发到 1087 则会通过`ShadowsocksX-NG`去访问互联网

## terminal 科学上网

### step1: 复制代理环境变量

首先配置好`ShadowsocksX-NG`，保证浏览器能科学上网

点击 Mac 顶部状态栏的`ShadowsocksX-NG`图标，可一键复制环境变量

![](https://cdn.liushiming.cn/img/20200428185604.png)

点了这个按钮剪贴板中就有了如下的命令

```bash
export http_proxy=http://127.0.0.1:1087;export https_proxy=http://127.0.0.1:1087;
```

### step2：将命令贴到 terminal 中执行

将`step1`得到的命令贴到终端运行，当前的 terminal session 即可以科学上网了

这只是临时操作，再新开一个 session 就无效了

如果想 terminal 默认走代理，需要将`step1`的 `export` 命令贴到`.bashrc`、`.zshrc`或`.profile`文件中去

## jetbrains 系列 ide 代理设置

jetbrains 代理设置和 terminal 的代理设置其实一样，不过一个是命令行，一个是界面操作罢了

**具体流程：**  
`preferences -> 搜索 proxy -> 选择手动设置代理 -> 将 http 代理参数填进去`

![](https://cdn.liushiming.cn/img/20200428191312.png)
