[//title]: (使用acme.sh生成let's-encrypt泛域名证书)
[//englishtitle]: (generate-ssl-cert-by-acme)
[//category]: (web,ssl,security)
[//tags]: (acme,ssl,let'sencrypt,证书)
[//createtime]: (20200402)
[//updatetime]: (20200402)

## 概述

本文介绍如何使用`acme.sh`生成 let's encrypt 泛域名 ssl 证书

## 安装 acme.sh

安装很简单，一个命令:

```bash
curl  https://get.acme.sh | sh
```

普通用户和 root 用户都可以安装使用.
安装过程进行了以下几步:

1. 把 acme.sh 安装到你的 **home** 目录下:

```bash
~/.acme.sh/
```

并创建 一个 bash 的 alias， 方便你的使用: `alias acme.sh=~/.acme.sh/acme.sh`

2). 自动为你创建 cronjob， 每天 0:00 点自动检测所有的证书， 如果快过期了， 需要更新， 则会自动更新证书。

更高级的安装选项请参考: https://github.com/Neilpang/acme.sh/wiki/How-to-install

**安装过程不会污染已有的系统任何功能和文件**， 所有的修改都限制在安装目录中: `~/.acme.sh/`

## 生成证书

**acme.sh** 实现了 **acme** 协议支持的所有验证协议。
一般有两种方式验证: http 和 dns 验证。dns 验证又分手动和自动。

我建议使用 dns 自动验证的方式，方便快捷。

使用方法参考[官方文档](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)

支持主流的 dns 服务商，去 dns 服务商申请一个 apikey 即可使用。

我使用的是 dnspod，export apikey 之后，一键生成泛域名证书的命令为

```bash
acme.sh --issue --dns dns_dp -d 'liushiming.cn' -d '*.liushiming.cn'
```

**域名注意要加单引号** ，否则因为有`*`通配符，可能会报错（我使用的是 zsh，报的错为`zsh: no matches found: *.liushiming.com`）

## 查看证书

证书生成后在`/.acme.sh/liushiming.cn/`目录中，在 nginx/apache 中配置使用即可。

## 证书自动更新

每天 0:00 点自动检测所有的证书，如果快过期了，需要更新， 则会自动更新证书。

## 参考资料

[acme wiki](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)  
[dnsapi 使用说明](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)  
[Issue Wildcard certificate with zsh failed](https://github.com/acmesh-official/acme.sh/issues/1334)
