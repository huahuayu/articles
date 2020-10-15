[//title]: (vim插件管理器vim-plug教程)
[//englishtitle]: (vim-plug-tutorial)
[//category]: (vim)
[//tags]: (vim)
[//createtime]: (20201015)
[//updatetime]: (20201015)

## 介绍

[vim-plug](https://github.com/junegunn/vim-plug) 是一个轻量级的 vim 插件管理器，使用起来非常简单。

适当的装一些插件能提高 vim 的使用效率，比如说我装了注释插件，支持一键注释多行；go 语言插件，更好的支持 go 语言开发；solidity 插件支持 solidity 高亮等等。

## 安装

mac 和 linux 安装:

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

命令执行完后在 `~/.vim/autoload` 路径下应该有 `plug.vim` 这个文件。

## 使用

**step1: 在.vimrc 中配置插件信息**

在 vim 的配置文件`~/.vimrc`中添加配置，写上要启用的插件名称，以下是示例：

```text
call plug#begin('~/.vim/plugged')
    Plug 'preservim/nerdcommenter'
    Plug 'fatih/vim-go', { 'do': ':GoInstallBinaries' }
    Plug 'tomlion/vim-solidity'
call plug#end()
```

其中以下是固定写法，`~/.vim/plugged`是插件文件存放的路径

```
call plug#begin('~/.vim/plugged')
    ...
    ...
call plug#end()
```

中间的三行就是三个插件名（插件的名称由插件的开发者提供）

```
    Plug 'preservim/nerdcommenter'
    Plug 'fatih/vim-go', { 'do': ':GoInstallBinaries' }
    Plug 'tomlion/vim-solidity'
```

**step2: 安装插件**

打开 vim，在命令模式下执行`:PlugInstall`命令即会开始安装

![](https://cdn.liushiming.cn/img/20201015175215.png)

将有一个新 windows 弹出展示安装进度

![](https://cdn.liushiming.cn/img/20201015175822.png)

**step3: 查看启用的插件**

使用`:scriptnames`可以查看启用的 vim 脚本/插件，可以再确认一下插件是否启用

![](https://cdn.liushiming.cn/img/20201015180148.png)

## 参考资料

[vim-plug github readme](https://github.com/junegunn/vim-plug)
