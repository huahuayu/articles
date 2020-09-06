[//title]: (mac升级最新版bash)
[//englishtitle]: (Upgrading-Bash-on-macOS)
[//category]: (mac,bash)
[//tags]: (mac,bash)
[//createtime]: (2020-09-06)
[//updatetime]: (2020-09-06)

## 概述

Mac 自带的 bash shell 版本为 3.2，2007 年的版本了，原因是 bash 3.2 的授权为 GPLv2，3.2 的下一版本为 4.0，使用 GNU General Public License v3 (GPLv3)授权，苹果不想使用这个授权的 bash，所以一直没升级。

```bash
bash --version
GNU bash, version 3.2.57(1)-release (x86_64-apple-darwin19)
Copyright (C) 2007 Free Software Foundation, Inc.
```

目前 GNU Bash 的最新版为 5.x 参考(http://ftp.gnu.org/gnu/bash/)。

为什么我会想升级 bash 呢？因为我需要 4.x 以上的版本才可以使用 vscode 的[bash debug 插件](https://marketplace.visualstudio.com/items?itemName=rogalmic.bash-debug)。对于一些复杂的 bash 脚本，我有 debug 的需求（如何 debug bash 脚本，可以查看我这篇文章[使用 vscode 调试 bash 脚本](https://liushiming.cn/article/debug-bash-on-macos.html)）。

下面介绍如何升级 Mac 的 bash 版本。

## 安装新版 bash

使用`brew`安装

```bash
brew install bash
```

查看安装的 bash

```bash
$ which -a bash
/bin/bash
/usr/local/bin/bash
```

安装的新版 bash 在`/usr/local/bin/`下面，而系统自带的 bash 在`/bin`下面，所以如果想使用新版的 bash，必须保证在`PATH`中`/usr/local/bin` 排在 `/bin`的前面。

调整完成后再次使用`which`查看,`/usr/local/bin`排在前面即可。

```bash
which -a bash
/usr/local/bin/bash
/bin/bash
```

## 设置为默认 shell (可选)

首先需要把新版 bash 路径添加到`/etc/shells`中，作为系统信任的 shell，即`login shell`（登录到系统时所使用的 shell）。

在`/etc/shells`最后加上`/usr/local/bin/bash`

```bash
/bin/bash
/bin/csh
/bin/dash
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
/usr/local/bin/bash
```

然后执行`chsh`设置默认 shell

```bash
sudo chsh -s /usr/local/bin/bash
```

## 注意事项

写 bash 脚本的时候，如以下`test.sh`, 第一行默认写法是`#!/bin/bash`，显现设置了使用`/bin/bash`执行脚本。

```bash
#!/bin/bash
echo $BASH_VERSION
```

使用不同的执行方式会有不同的结果

```bash
$ bash test.sh
5.0.18(1)-release
$ ./test.sh
3.2.57(1)-release
```

如果想确保使用新版 bash 执行可以将脚本第一行改为`#!/usr/local/bin/bash`。

## 参考资料

[Upgrading Bash on macOS](https://itnext.io/upgrading-bash-on-macos-7138bd1066ba)
