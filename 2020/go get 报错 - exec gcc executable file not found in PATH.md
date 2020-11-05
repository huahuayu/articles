[//title]: (go-get报错exec-gcc-executable-file-not-found-in-PATH)
[//englishtitle]: (go-get-build-error-gcc-not-found)
[//category]: (go)
[//tags]: (go,gcc,windows,gitbash,ubuntu)
[//createtime]: (2020-11-05)
[//updatetime]: (2020-11-05)

## 概述

GNU 编译器套装（英语：GNU Compiler Collection，缩写为 GCC），指一套编程语言编译器，以 GPL 及 LGPL 许可证所发行的自由软件，也是 GNU 计划的关键部分，也是 GNU 工具链的主要组成部分之一。GCC（特别是其中的 C 语言编译器）也常被认为是跨平台编译器的事实标准。

以下 `go get` 命令需要用到 gcc，需要 gcc 在 `PATH` 环境变量中可以找到，如果没有在 PATH 中则报错：exec: "gcc": executable file not found in %PATH%

```bash
 go get -u github.com/ethereum/go-ethereum
```

具体命令行：

```bash
$  go get -u github.com/ethereum/go-ethereum
go: github.com/btcsuite/btcd upgrade => v0.21.0-beta
go: golang.org/x/crypto upgrade => v0.0.0-20201016220609-9e8e0b390897
go: golang.org/x/sys upgrade => v0.0.0-20201101102859-da207088b7d1
# github.com/ethereum/go-ethereum/crypto/secp256k1
exec: "gcc": executable file not found in %PATH%
```

本文介绍解决方案。

## windows 解决方案

在这里下载 gcc 并重启 shell 命令行界面：

https://jmeubank.github.io/tdm-gcc/

检查一下 gcc 命令是否在 PATH 中，如果不在需要手动加到 PATH 中：

git bash 下可以用 `which gcc` 检查

windows cmd 下可以用 `where gcc` 检查

比如我的 tdm-gcc 安装路径是 `C:\TDM-GCC-64\bin`，用的是 git bash，在家目录下（C:\Users\shiming.liu）新建`.bashrc`文件（git bash 默认会加载这个文件的），加上一行：

```bash
PATH=$PATH:/c/TDM-GCC-64/bin
```

在命令行中再执行：

```bash
source ~/.bashrc
```

执行完后用`gcc -v`检查 gcc 命令是否正常工作。

安装完 gcc，再 `go get` 即可，没有错误了

```bash
$  go get -u github.com/ethereum/go-ethereum
go: github.com/btcsuite/btcd upgrade => v0.21.0-beta
go: golang.org/x/sys upgrade => v0.0.0-20201101102859-da207088b7d1
go: golang.org/x/crypto upgrade => v0.0.0-20201016220609-9e8e0b390897
```

## ubuntu 解决方案

`apt-get` 安装 `build-essential` 即可

```bash
apt-get update
apt-get install build-essential
```

## 参考资料

[exec: “gcc”: executable file not found in %PATH% when trying go build](https://stackoverflow.com/questions/43580131/exec-gcc-executable-file-not-found-in-path-when-trying-go-build)
