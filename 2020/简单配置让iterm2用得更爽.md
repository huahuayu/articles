[//title]: (简单配置让iterm2用得更爽)
[//englishtitle]: (awesome-iterm2-config)
[//category]: (mac,iterm2)
[//tags]: (mac,iterm2,dotfiles)
[//createtime]: (20200115)
[//updatetime]: (20201020)

## 概述

`iterm2`比 mac 原生的 terminal 好用很多，是 mac 下必装的软件之一，装好后简单配置就用的很爽了。我个人的配置主要有以下方面，供参考：

- 开机默认启动 iterm
- 主题设置为 Solarized Dark
- 设置更美观的中文字体
- 开启全局快捷键`opton + space`方便随时唤起`iterm2`
- 设置 vim 中鼠标选中复制功能
- 设置 vim 支持鼠标滚动
- 新 tab 页使用前一个 tab 页的目录
- 备份配置文件

## 版本信息

**macOS**
10.15.2

**iterm2**
3.3.7

## 开机默认启动

`System Preferences > Users & Groups > Login Items > 点加号 > Applications列表选中iTerm`
![](https://cdn.liushiming.cn/img/20200112193859.png)

## 主题设置

`Preferences > Profiles > Colors > Color Presets > 选择Solarized Dark`

之所以选择这个颜色，因为这个颜色（姑且称为哑光淡蓝色吧），在很多支持配色的软件里都有，是一个经典配色了，而且我 vscode 也是这个颜色
![](https://cdn.liushiming.cn/img/20200112190608.png)

## 字体设置

`Preferens > Profiles > Text > 勾上Use a different font for non-ASCII text > 选择自己喜欢的字体`

iterm 的默认字体是 Monaco，Monaco 的中文本来也还行，但是如果将字体放大一点，会发现英文字体变大了，但是中文看起来比英文还小。

好在 iterm 支持为非 ASCII 的字符设置另一种字体，所以我选择了 Helvetica Neue（和 Mac 下印象笔记的字体一致，相信他们的专业审美），看起来好多了。
![](https://cdn.liushiming.cn/img/20200112192650.png)

实际效果：
![](https://cdn.liushiming.cn/img/20200112195745.png)

## 全局唤醒快捷键

`Preferences > Keys > Hotkey > 勾上Show/hide all windows with a system-wide hotkey`
默认快捷键是`option + space`，用起来很顺手，在任何界面需要敲命令行，按一下唤出，再按一下隐藏，非常方便。
![](https://cdn.liushiming.cn/img/20200112193350.png)

## vim 中鼠标选中复制

默认 iterm2 是开启`mouse reporting`功能的，不把它关闭鼠标选中 vim 中的文本，按`command + c`键是不能复制的（而是会进入 vim 的 visual 模式!）。我查了很久才在 iterm2 的官方 gitlab 库中搜到一个[issue](https://gitlab.com/gnachman/iterm2/issues/7799#)说到这个解决方案。网上基本上没有说法。

`Preferens > Profiles > Terminal > 关闭Enable mouse reporting`
![](https://cdn.liushiming.cn/img/20200112200627.png)

## 设置 vim 支持鼠标滚动

关闭`mouse reporting`后，vim 编辑时就不支持鼠标滚动了，但是 iterm2 可以设置通过发送 j/k 键来支持滚动，`preferences > advance > 搜索mouse > scroll whell down 那里写\j > scroll whell up那里写\k`
![](https://cdn.liushiming.cn/img/iterm2scroll.jpg)

## 设置无限向上滚动

有时候 terminal 上输出的内容很多，需要向上滚动查看输出历史，建议设置无限滚动 `preferences > Profiles > Terminal > Unlimited scrollback`  
![](https://cdn.liushiming.cn/img/20200318174737.png)

## 新 tab 页目录设置

如果不设置，默认新打开一个 tab 页面或者分屏时，新的窗口的进入的目录为用户家目录。可以设置为沿用上一个 tab 页的当前目录 `preferences > profiles > general > reuse previous session's directory`

![](https://cdn.liushiming.cn/img/20201020134410.png)

## 备份配置

自己的配置最好备份好，下次有需要再导入即可，体验保持一致。
`Preferences > Profiles > Other Actions > Save All Profiles as JSON...`
![](https://cdn.liushiming.cn/img/20200112201400.png)

导出的配置文件可以放到 dotfiles 上传到 github 保存（但如果保存了密码之类的配置就不要上传了），换电脑的时候再也不用担心软件用不顺手了。

## 结论

- 通过简单配置，iterm2 变得更美观易用，对中文支持也更好。
- 将配置保存到 dotfiles，使用 github 管理有助于在任何机器上保持一致的体验。
