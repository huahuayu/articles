[//title]:(一文搞懂vim复制粘贴)
[//englishTitle]:(copy-and-paste-in-vim)
[//category]:(vim,problem,problem-solved)
[//tags]:(vim)
[//createTime]:(20200118)
[//lastUpdateTime]:(20200118)
## 概述
复制粘贴是文本编辑最常用的功能，但是在vim中复制粘贴还是有点麻烦的，有一点学习成本。本文总结了使用vim复制粘贴的典型场景和使用方法，希望对读者有帮助。

## vim内部复制、粘贴、剪切

### 选择文本

- v+光标移动 （按字符选择）高亮选中所要的文本，然后进行各种操作（比如，d表示删除）。
- V （按行选择）
- v+选中的内容+c 更改选中的文字

### 复制：y(ank)

- y 用v命令选中文本后，用y进行复制
- yy 复制当前行，然后用p进行粘贴
- 5yy 复制从当前行开始的5行
- y_ 等同于yy
- Y 等同于yy
- yw 复制当前单词
- y$ 从当前位置复制到行尾
- y0 从当前位置复制到行首
- y^ 从当前位置复制到第一个非空白字符
- yG 从当前行复制到文件结束
- y20G 从当前行复制到第20行
- y?bar 复制至上一个出现bar的位置

### 粘贴：p(aste)
- p(小写) 在光标位置之后粘贴
- P(大写) 在光标位置之前粘贴

### 剪切
- v + 方向键(hjkl)选中内容 + d 剪切 + p粘贴

## 剪贴板

### 默认剪贴板

vim提供12个剪贴板，它们的名字分别为vim有11个粘贴板，分别是`0`、`1`、`2`、`...`、`9`、`a`、`“`。如果开启了系统剪贴板，则会另外多出两个：`+`和`*`。使用`:reg`命令，可以查看各个粘贴板里的内容。

```
:reg
```

在vim中简单用y只是复制到`“`（双引号)粘贴板里，同样用p粘贴的也是这个粘贴板里的内容。

### 复制和粘贴到指定剪贴板

要将vim的内容复制到某个粘贴板，需要退出编辑模式，进入正常模式后，选择要复制的内容，然后按"Ny完成复制，其中N为粘贴板号（注意是按一下双引号然后按粘贴板号最后按y），例如要把内容复制到粘贴板a，选中内容后按"ay就可以了。

要将vim某个粘贴板里的内容粘贴进来，需要退出编辑模式，在正常模式按"Np，其中N为粘贴板号。比如，可以按"5p将5号粘贴板里的内容粘贴进来，也可以按"+p将系统全局粘贴板里的内容粘贴进来。

## 系统剪贴板

Vim支持系统剪贴板，需要打开clipboard功能。使用下面的命令，检查当前版本的Vim，是否支持clipboard。
``` bash
$ vim --version | grep "clipboard"
-clipboard       +insert_expand   +path_extra      +user_commands
+emacs_tags      -mouseshape      +startuptime     -xterm_clipboard
root@sz ➜  ~ vim --version
VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Jun 07 2019 15:35:43)
```
`-clipboard`代表不支持`+clipboard`代表支持。
如果不支持的话，需要安装gvim或者在编译的时候开启系统剪贴板选项。

### vim复制到系统剪贴板
- `"*y`
- `"+y`
- `"+2yy` – 复制两行
- `{Visual}"+y` - copy the selected text into the system clipboard
- `"+y{motion}` - copy the text specified by {motion} into the system clipboard
- `:[range]yank +` - copy the text specified by `[range]` into the system clipboard

### vim剪切到系统剪贴板
- `"+dd` – 剪切一行

### 从系统剪贴板粘贴到vim
- `"*p`
- `"+p`
- `Shift+Insert`
- `:put +` - Ex command puts contents of system clipboard on a new line
- `<C-r>`+ - From insert mode (or commandline mode)

`"+p`比 Ctrl-v 命令更好，它可以更快更可靠地处理大块文本的粘贴，也能够避免粘贴大量文本时，发生每行行首的自动缩进累积，因为`Ctrl-v`是通过系统缓存的stream处理，一行一行地处理粘贴的文本。

### 设置vim默认使用系统剪贴板
确定vim支持`+clipboard`后，如果想`y/p`直接和系统剪贴板打通，可以在`~/.vimrc`中加上以下配置）：
```
set clipboard^=unnamed,unnamedplus
```
其中unnamed代表`*`寄存器，unnamedplus代表`+`寄存器。在mac系统中，两者都一样；一般在linux系统中`+`和`*`是不同的，`+`对应`ctrl + c`,`ctrl + v`的桌面系统剪贴板，`*`对应[x桌面系统](https://en.wikipedia.org/wiki/X_Window_System)的剪贴板（用鼠标选择复制，用鼠标中键粘贴）。


### 服务器vim复制到本地剪贴板
通过ssh连接到linux服务器时，vim是跑在远程服务器的，不能使用`y`复制到本地的剪贴板。这时有几个方法：

#### cmd + c
用鼠标选中文字`cmd + c`复制（windows下`ctrl + c`复制），`cmd + v`粘贴到本地。这个方法是最自然的，但是想要用这个方法有几个前提：

* vim配置中开启鼠标支持，`.vimrc`文件中加上

```
set mouse=a
```
* terminal客户端关闭`mouse reporting`选项，否则鼠标点击vim界面会进入visual模式。参见我这篇文章[解决iterm2中vim选中文字不能复制的问题](https://liushiming.cn/2020/01/18/%e8%a7%a3%e5%86%b3iterm2%e4%b8%advim%e9%80%89%e4%b8%ad%e6%96%87%e5%ad%97%e4%b8%8d%e8%83%bd%e5%a4%8d%e5%88%b6%e7%9a%84%e9%97%ae%e9%a2%98/)
* 如果使用tmux，tmux需要配置支持鼠标滚轮，否则最多只能复制当前页面的内容

#### 本地vim通过scp编辑远程文件
使用本地vim通过scp直接编辑远程文件。这样就可以使用本地寄存器`"+y"`复制了。这种方法对远程vim配置没有要求。

```
vim scp://remoteuser@server.com//absolute/path/to/file
```
*注意com和absolute间是两个反斜杠`//`并不是敲错了。*

## 参考链接

* [Accessing the system clipboard](https://vim.fandom.com/wiki/Accessing_the_system_clipboard) 
* [Editing remote files via scp in vim](https://vim.fandom.com/wiki/Editing_remote_files_via_scp_in_vim) 
* [Vim 配置入门](http://www.ruanyifeng.com/blog/2018/09/vimrc.html)
