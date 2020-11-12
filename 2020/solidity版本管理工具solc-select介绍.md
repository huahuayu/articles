[//title]: (solidity版本管理工具solc-select介绍)
[//englishtitle]: (solidity-version-manager-solc-select-introduction)
[//category]: (ethereum,solidity)
[//tags]: (ethereum,solidity,solc,nvm,jenv)
[//createtime]: (20201112)
[//updatetime]: (20201112)

## 概述

开发以太坊智能合约，本地编译时，如果本地的 `solidity` 编译器 `solc` 的版本与合约代码使用的版本不一致时，编译会失败。

比如说合约使用 0.5.16 版本，而编译器使用 0.7.4 版本。

编译的时候会报以下错误：

```text
Error: Source file requires different compiler version (current compiler is 0.7.4+commit.3f05b770.Linux.g++) - note that nightly builds are considered to be strictly less than the released version
 --> StakingRewards.sol:5:1:
  |
5 | pragma solidity ^0.5.16;
  | ^^^^^^^^^^^^^^^^^^^^^^^^
```

但是如果你把 0.7.4 版本卸载，安装 0.5.16 版本，下次调试其他版本合约怎么办？难道又要卸载重装？显然不是一个解决办法。

所以我们需要一个 `solc` 的版本管理工具，可用安装多个 `solc` 版本，然后选择一个使用，随时可以切换，原理类似于 `Java` 的版本管理工具 `jenv` 和 `nodejs` 的版本管理工具 `nvm`。

经过一番对比，我觉得 [solc-select](https://github.com/crytic/solc-select) 好用，下面做简单介绍。

## 安装

安装的时候会下载所有稳定版本，`solc` 应该也不大, 安装很快。

### Linux

```bash
git clone https://github.com/crytic/solc-select.git
./solc-select/scripts/install.sh
```

脚本运行完后将 `.solc-select` 加入 PATH 中, 需要将以下语句加入 .bashrc 或者 .zshrc 文件末尾。

```bash
export PATH=$HOME/.solc-select:$PATH
```

然后 `source` rc 文件。

```bash
source ~/.zshrc
```

### OS X (测试失败)

osx 我根据官网的安装指引，安装失败了（20201111 号测试），已提了[issue](https://github.com/crytic/solc-select/issues/33) 。

目前 commit id: 002b9a8be26533c2240e1265870449f629477e32

有兴趣自己试下。

```bash
docker pull trailofbits/solc-select
docker run --read-only -i --rm --entrypoint='/bin/sh' trailofbits/solc-select:latest -c 'cat /usr/bin/install.sh' | bash -e
```

## 验证安装

首先检查是否在 PATH 中

```bash
$ which solc
/home/shiming/.solc-select/solc
```

运行 `solc --versions`，查看可用的版本，出现版本列表就安装成功了

```bash
$ solc --versions
0.7.4
0.7.3
0.7.2
0.7.1
0.7.0
......
```

## 查看目前所用的版本

```bash
$ solc --version
solc, the solidity compiler commandline interface
Version: 0.4.0+commit.acd334c9.Linux.g++
```

## 切换版本

```bash
$ solc use 0.5.16
Now using version 0.5.16
```

切换到正确的版本就可以愉快的编译了！

## 升级

```bash
$ solc --upgrade
```

## 参考资料

[solc-select github repo](https://github.com/crytic/solc-select)
