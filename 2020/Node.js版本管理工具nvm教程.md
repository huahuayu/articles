[//title]: (Node.js版本管理工具nvm教程)
[//englishtitle]: (nodejs-version-manager-nvm-tutorial)
[//category]: (node,nvm)
[//tags]: (nodejs,nvm)
[//createtime]: (20201123)
[//updatetime]: (20201123)

## 概述

`nvm` 是 nodejs 的版本管理工具，可以在一个环境中同时安装多个 nodejs 版本（和配套的 npm 版本），并随时切换。

好处是，开发调试不同 nodejs 应用变得更省心，随时切换所需要的版本。

官方 github：https://github.com/nvm-sh/nvm

## 安装 nvm

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.0/install.sh | bash
```

或者

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.0/install.sh | bash
```

安装后需要添加环境变量到`.bashrc` 、`.zshrc` 或 `.profile` 文件：

```bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

但是以上加载`nvm`的动作或严重拖慢`shell`的启动速度，可以参考我这篇文章做优化[zsh 启动速度优化 – 5 倍提升](https://liushiming.cn/article/speed-up-zsh-load-nvm-tuning.html)

所有关于安装/更新 nvm 的细节可以参考[官方 github](https://github.com/nvm-sh/nvm#install--update-script)

## 查看可安装的 nodejs 版本

```bash
nvm ls-remote
```

会输出一个很长的列表

```bash
       ...
       v12.18.3   (LTS: Erbium)
       v12.18.4   (LTS: Erbium)
       v12.19.0   (LTS: Erbium)
       v12.19.1   (Latest LTS: Erbium)
        v13.0.0
        v13.0.1
        v13.1.0
        v13.2.0
        v13.3.0
        v13.4.0
        v13.5.0
        v13.6.0
        v13.7.0
        v13.8.0
        v13.9.0
       v13.10.0
       v13.10.1
       v13.11.0
       v13.12.0
       ...
```

## 安装 node

从上一步的列表中可以选择一个版本安装

```bash
nvm install v12.19.1
```

## 查看已安装的版本

使用 `nvm ls` 可以查看已安装的版本

```bash
nvm ls
```

## 指定使用某个版本

```bash
nvm use v12.19.1
```

## 设置默认版本

如果安装了多个版本，可以指定一个版本作为默认版本(给该版本一个 default 的别名)

```bash
nvm alias default v14.15.1
```

## 用特定的 nodejs 版本运行应用

```bash
nvm run v14.15.1 app.js
```

## 卸载某个特定 nodejs 版本

```bash
nvm uninstall v14.15.1
```

## 参考资料

[How to Install and Manage Node.js via NVM](https://tecadmin.net/install-nodejs-with-nvm/)
