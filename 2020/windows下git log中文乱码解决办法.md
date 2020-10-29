[//title]: (windows下git-log中文乱码解决办法)
[//englishtitle]: (windows-git-log-messy-code-fix)
[//category]: (windows,git)
[//tags]: (windows,git,git-log)
[//createtime]: (2020-10-29)
[//updatetime]: (2020-10-29)

## 问题描述

windows 下安装了 git bash，执行`git log`命令，中文的 git commit msg 出现乱码。

不管是在 windows cmd 窗口还是 git bash 窗口，都是乱码。

```bash
$ git status
commit 5d1281197d8879b22bcc217ccaaf0daba4f6b4ab
Author: Shiming <codingmylife@gmail.com>
Date:   Wed Oct 28 21:10:51 2020 +0800

    <D0>޸<C4><CE>ļ<FE><C3><FB>
```

## 解决办法

**step1:** 打开 git bash

**step2:** git bash 界面上点击右键 -> options -> text

**step3:** local 选择为 zh_CN, charector set 选择为 UTF-8

**step4:**git bash 上面输入以下命令

```bash
git config --global i18n.commitencoding utf-8
git config --global i18n.logoutputencoding utf-8
```

## 相关文章

[windows 下 git status 乱码解决办法](https://liushiming.cn/article/windows-git-messy-code.html)
