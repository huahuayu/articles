[//title]: (一文搞懂vim复制粘贴)
[//englishtitle]: (copy-and-paste-in-vim)
[//category]: (vim,problem,problem-solved)
[//tags]: (vim)
[//createtime]: (20200118)
[//updatetime]: (20200402)

## 概述

复制粘贴是文本编辑最常用的功能，但是在 vim 中复制粘贴还是有点麻烦的，有一点学习成本。本文总结了使用 vim 复制粘贴的典型场景和使用方法，希望对读者有帮助。

## vim 内部复制、粘贴、剪切

### 选择文本

- v+光标移动 （按字符选择）高亮选中所要的文本，然后进行各种操作（比如，d 表示删除）。
- V （按行选择）
- v+选中的内容+c 更改选中的文字

### 复制：y(ank)

- y 用 v 命令选中文本后，用 y 进行复制
- yy 复制当前行，然后用 p 进行粘贴
- 5yy 复制从当前行开始的 5 行
- y\_ 等同于 yy
- Y 等同于 yy
- yw 复制当前单词
- y\$ 从当前位置复制到行尾
- y0 从当前位置复制到行首
- y^ 从当前位置复制到第一个非空白字符
- yG 从当前行复制到文件结束
- y20G 从当前行复制到第 20 行
- y?bar 复制至上一个出现 bar 的位置

### 粘贴：p(aste)

- p(小写) 在光标位置之后粘贴
- P(大写) 在光标位置之前粘贴

### 剪切

- v + 方向键(hjkl)选中内容 + d 剪切 + p 粘贴

## 剪贴板

### 默认剪贴板

vim 提供 12 个剪贴板，它们的名字分别为 vim 有 11 个粘贴板，分别是`0`、`1`、`2`、`...`、`9`、`a`、`“`。如果开启了系统剪贴板，则会另外多出两个：`+`和`*`。使用`:reg`命令，可以查看各个粘贴板里的内容。

```text
:reg
```

在 vim 中简单用 y 只是复制到`“`（双引号)粘贴板里，同样用 p 粘贴的也是这个粘贴板里的内容。

### 复制和粘贴到指定剪贴板

要将 vim 的内容复制到某个粘贴板，需要退出编辑模式，进入正常模式后，选择要复制的内容，然后按"Ny 完成复制，其中 N 为粘贴板号（注意是按一下双引号然后按粘贴板号最后按 y），例如要把内容复制到粘贴板 a，选中内容后按"ay 就可以了。

要将 vim 某个粘贴板里的内容粘贴进来，需要退出编辑模式，在正常模式按"Np，其中 N 为粘贴板号。比如，可以按"5p 将 5 号粘贴板里的内容粘贴进来，也可以按"+p 将系统全局粘贴板里的内容粘贴进来。

## 系统剪贴板

Vim 支持系统剪贴板，需要打开 clipboard 功能。使用下面的命令，检查当前版本的 Vim，是否支持 clipboard。

```bash
$ vim --version | grep "clipboard"
-clipboard       +insert_expand   +path_extra      +user_commands
+emacs_tags      -mouseshape      +startuptime     -xterm_clipboard
root@sz ➜  ~ vim --version
VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Jun 07 2019 15:35:43)
```

`-clipboard`代表不支持`+clipboard`代表支持。
如果不支持的话，需要安装 gvim 或者在编译的时候开启系统剪贴板选项。

### vim 复制到系统剪贴板

- `"*y`
- `"+y`
- `"+2yy` – 复制两行
- `{Visual}"+y` - copy the selected text into the system clipboard
- `"+y{motion}` - copy the text specified by {motion} into the system clipboard
- `:[range]yank +` - copy the text specified by `[range]` into the system clipboard

### vim 剪切到系统剪贴板

- `"+dd` – 剪切一行

### 从系统剪贴板粘贴到 vim

- `"*p`
- `"+p`
- `Shift+Insert`
- `:put +` - Ex command puts contents of system clipboard on a new line
- `<C-r>`+ - From insert mode (or commandline mode)

`"+p`比 Ctrl-v 命令更好，它可以更快更可靠地处理大块文本的粘贴，也能够避免粘贴大量文本时，发生每行行首的自动缩进累积，因为`Ctrl-v`是通过系统缓存的 stream 处理，一行一行地处理粘贴的文本。

### 设置 vim 默认使用系统剪贴板

确定 vim 支持`+clipboard`后，如果想`y/p`直接和系统剪贴板打通，可以在`~/.vimrc`中加上以下配置）：

```text
set clipboard^=unnamed,unnamedplus
```

其中 unnamed 代表`*`寄存器，unnamedplus 代表`+`寄存器。在 mac 系统中，两者都一样；一般在 linux 系统中`+`和`*`是不同的，`+`对应`ctrl + c`,`ctrl + v`的桌面系统剪贴板，`*`对应[x 桌面系统](https://en.wikipedia.org/wiki/X_Window_System)的剪贴板（用鼠标选择复制，用鼠标中键粘贴）。

### 服务器 vim 复制到本地剪贴板

通过 ssh 连接到 linux 服务器时，vim 是跑在远程服务器的，不能使用`y`复制到本地的剪贴板。这时有几个方法：

#### cmd + c

用鼠标选中文字`cmd + c`复制（windows 下`ctrl + c`复制），`cmd + v`粘贴到本地。这个方法是最自然的，但是想要用这个方法有几个前提：

- vim 配置中开启鼠标支持，`.vimrc`文件中加上

```text
set mouse=a
```

- terminal 客户端关闭`mouse reporting`选项，否则鼠标点击 vim 界面会进入 visual 模式。参见我这篇文章[解决 iterm2 中 vim 选中文字不能复制的问题](https://liushiming.cn/article/vim-copy-issue-in-iterm2/)
- 如果使用 tmux，tmux 需要配置支持鼠标滚轮，否则最多只能复制当前页面的内容

#### 本地 vim 通过 scp 编辑远程文件

使用本地 vim 通过 scp 直接编辑远程文件。这样就可以使用本地寄存器`"+y"`复制了。这种方法对远程 vim 配置没有要求。

```text
vim scp://remoteuser@server.com//absolute/path/to/file
```

_注意 com 和 absolute 间是两个反斜杠`//`并不是敲错了。_

## 参考链接

- [Accessing the system clipboard](https://vim.fandom.com/wiki/Accessing_the_system_clipboard)
- [Editing remote files via scp in vim](https://vim.fandom.com/wiki/Editing_remote_files_via_scp_in_vim)
- [Vim 配置入门](http://www.ruanyifeng.com/blog/2018/09/vimrc.html)
