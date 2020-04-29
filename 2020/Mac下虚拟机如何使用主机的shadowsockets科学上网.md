[//title]: (Mac下虚拟机如何使用主机的shadowsockets科学上网)
[//englishtitle]: (mac-virtual-machine-proxy-through-host-machine-shadowsocks)
[//category]: (mac,shadowsocks,gfw)
[//tags]: (mac,shadowsocks,gfw,terminal,proxy,virtual-machine,科学上网,翻墙)
[//createtime]: (2020-04-29)
[//updatetime]: (2020-04-29)

## 概述

主机配置好了 `shadowsockets` 科学上网

主机通过 `parallels desktop` 或 `vmware` 安装了一台虚拟机，比如说 ubuntu

那么问题来了：**在虚拟机内如何科学上网？**

**有两种解决方案：**  
1）在虚拟机里面也配置一个 shadowsockets 客户端  
2）虚拟机通过主机的 shadowsockets 上网

显然，第一种是典型的 `repeat yourself` 的做法，最好别这么做

本文介绍第二种方案：虚拟机通过主机的`shadowsockets` 来进行 http 代理上网

## 背景 - 为啥虚拟机要科学上网

既然主机能科学上网了，用主机不就得了，为什么要折腾虚拟机科学上网？

**实属无奈！**

比如我虚拟机要运行一个脚本，脚本里面有个任务，要从 github 下载一个大约 50M 的二进制安装包，虽然说 github 没有被墙，但是下载速度极慢，大概十几 kb/s，50M 的东西预估要下 3 小时，这能忍吗？

所以虚拟机也要科学上网

应该很多开发同学都会遇到这种问题

已经没脾气了

## 环境介绍

macOS: 10.15.4

ShadowsocksX-NG: 1.9.4

parallels desktop: 15.1.3

虚拟机: ubuntu 16.04

## 主机 terminal 如何科学上网

参考我写的这篇文章[Mac 下 terminal 终端、jetbrains 系列 IDE 如何科学上网](https://liushiming.cn/2020/04/28/mac-terminal-proxy-though-shadowsocks-pass-gfw/)

## 虚拟机 terminal 如何科学上网

如果你参考了我上面的文章，其实虚拟机 terminal 科学上网原理一样，只不过需要`ShadowsocksX-NG`的 http 代理端口设置对`0.0.0.0`服务，而不是使用`127.0.0.1`。因为虚拟机要能通过局域网访问到这个端口。

### step1: 修改 ShadowsocksX-NG http 代理端口

`点击 ShadowsocksX-NG 图标 -> Preferences -> http -> http proxy listen address 改为 0.0.0.0`

![](https://cdn.liushiming.cn/img/20200429085159.png)

### step2: 测试通过局域网是否能访问（可选）

首先通过`lsof`查看是否在`0.0.0.0:1087`端口工作

```bash
$ lsof -iTCP -sTCP:LISTEN -n -P | grep 1087
privoxy   25340 shiming    3u  IPv4 0xa88c238d0b494445      0t0  TCP *:1087 (LISTEN)
```

如果显示的是`*:1087`则说明服务正常

为了 double check，使用`ifconfig`命令找出主机的局域网地址，如`172.30.102.185`

然后使用`telnet`看端口是否能通，要保证出现 Connected 的字样

```bash
$ telnet 172.30.102.185 1087
Trying 172.30.102.185...
Connected to 172.30.102.185.
Escape character is '^]'.
```

### step3: 查看虚拟机局域网 ip

进入虚拟机，使用`ifconfig`命令查看局域网 ip（192、172、10 打头）将其记录下来

其中`10.211.55.21`就是了

```bash
$ ifconfig | grep inet
          inet addr:10.211.55.21  Bcast:10.211.55.255  Mask:255.255.255.0
          inet6 addr: fe80::ac25:f687:f1bb:32d2/64 Scope:Link
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
```

### step4: 找到主机的局域网 ip

这是关键的一步，需要在主机上找到和虚拟机同网段的 ip 地址

主机的 ip 地址可能有多个，一个网卡就有一个 ip

主机和虚拟机之间一般会再虚拟出一块网卡，这个 ip 地址和虚拟机才是同网段的, ip 地址的前三段都会相同

因为`step3`已经找出虚拟机的局域网 ip 为`10.211.55.21`

那么在主机中运行`ifconfig`，以下`10.211.55.2`就是主机与虚拟机同网段的 ip 了

```bash
$ ifconfig | grep inet
  inet address  172.30.102.185
  inet address  127.0.0.1
  inet address  192.168.77.6
  inet address  10.211.55.2
  inet address  10.37.129.2
```

### step5: 虚拟机 export 代理配置

最后一步，打开虚拟机的终端，指定代理地址`export http_proxy=http://主机局域网ip:1087;export https_proxy=http://主机局域网ip:1087;`

在虚拟机使用`curl`测试是否能连接`google`，如果能连接就说明完全配置好了

```bash
$ curl https://google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
```

**注意：**  
使用 `export` 命令只对当前的 terminal session 有效，如果想全部流量都走 Shadowsocks 出去，需要将 `export` 命令写到`.bashrc`、`.zshrc`或`.profile`文件中
