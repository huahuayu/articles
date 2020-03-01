[//title]:(解决vim选中文字不能复制的问题)
[//englishTitle]:(vim-copy-issue-in-iterm2)
[//category]:(mac,iterm2,vim,problem,problem-solved)
[//tags]:(mac,iterm2,vim)
## 概述
最近发现在`iterm2`中打开`vim`，用鼠标选中文字，并用`command + c`复制选中的文字，但粘贴出来是空的。查了一圈终于在iterm2的[官方gitlab issue库](https://gitlab.com/gnachman/iterm2/issues/7799#)里发现了解决办法。将Enable mouse reporting这个设置去掉即可。

## 版本信息
**操作系统**
Mac os 10.15.2

**iterm2**
iterm2 3.3.6
iterm2 3.3.7

**vim**
Vi IMproved 8.1 (2018 May 18, compiled Dec 26 2018 02:33:06)

## 重现步骤
1. 在`iterm2`中使用`cat`命令打印文件，然后用鼠标选中`command + c`可以正常复制。
2. 但是用`vim`打开文件，选中文字用`command + c`复制，却不能复制。
3. 并且当鼠标点击`vim`中的文字后，`vim`会自动变为`visual`模式。

## 解决方案
`iterm2 > Preferences > Profiles > Terminal > Enable mouse reporting 去掉勾选`
![](http://cdn.liushiming.cn/img/20200112165116.png)

## mouse reporting
mouse reporting的作用是在vim编辑时识别鼠标，可以支持鼠标滚轮查看文件内容（需要在.vimrc中加入设置支持鼠标`set mouse=a`）。关闭了mouse reporting后就不支持鼠标滚轮了。

那要使用鼠标滚轮怎么办？

可在`iterm2 > preferences > advance > 搜索mouse > scroll whell down 那里写\j > scroll whell up那里写\k`,作用就是发送j/k键到vim实现滚动效果。
![](https://cdn.liushiming.cn/img/iterm2scroll.jpg)

## 更多信息
Mac自带的terminal也会有这个问题，在`View > Allow Mouse Reporting 去掉勾选`即可
![](http://cdn.liushiming.cn/img/20200112181443.png)


