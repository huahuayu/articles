[//title]:(简单配置让iterm2用得更爽)
[//englishTitle]:(awesome-iterm2-config)
[//category]:(mac,iterm2)
[//tags]:(mac,iterm2,dotfiles)
[//createTime]:(20200115)
[//lastUpdateTime]:(20200115)
## 概述
`iterm2`比mac原生的terminal好用很多，是mac下必装的软件之一，装好后简单配置就用的很爽了。我个人的配置主要有以下方面，供参考：

* 开机默认启动iterm
* 主题设置为Solarized Dark
* 设置更美观的中文字体
* 开启全局快捷键`opton + space`方便随时唤起`iterm2`
* 设置vim中鼠标选中复制功能
* 设置vim支持鼠标滚动
* 备份配置文件


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

之所以选择这个颜色，因为这个颜色（姑且称为哑光淡蓝色吧），在很多支持配色的软件里都有，是一个经典配色了，而且我vscode也是这个颜色
![](https://cdn.liushiming.cn/img/20200112190608.png)

## 字体设置
`Preferens > Profiles > Text > 勾上Use a different font for non-ASCII text > 选择自己喜欢的字体`

iterm的默认字体是Monaco，Monaco的中文本来也还行，但是如果将字体放大一点，会发现英文字体变大了，但是中文看起来比英文还小。

好在iterm支持为非ASCII的字符设置另一种字体，所以我选择了Helvetica Neue（和Mac下印象笔记的字体一致，相信他们的专业审美），看起来好多了。
![](https://cdn.liushiming.cn/img/20200112192650.png)

实际效果：
![](https://cdn.liushiming.cn/img/20200112195745.png)

## 全局唤醒快捷键
`Preferences > Keys > Hotkey > 勾上Show/hide all windows with a system-wide hotkey`
默认快捷键是`option + space`，用起来很顺手，在任何界面需要敲命令行，按一下唤出，再按一下隐藏，非常方便。
![](https://cdn.liushiming.cn/img/20200112193350.png)

## vim中鼠标选中复制
默认iterm2是开启`mouse reporting`功能的，不把它关闭鼠标选中vim中的文本，按`command + c`键是不能复制的（而是会进入vim的visual模式!）。我查了很久才在iterm2的官方gitlab库中搜到一个[issue](https://gitlab.com/gnachman/iterm2/issues/7799#)说到这个解决方案。网上基本上没有说法。

`Preferens > Profiles > Terminal > 关闭Enable mouse reporting`
![](https://cdn.liushiming.cn/img/20200112200627.png)

## 设置vim支持鼠标滚动
关闭`mouse reporting`后，vim编辑时就不支持鼠标滚动了，但是iterm2可以设置通过发送j/k键来支持滚动，`iterm2 > preferences > advance > 搜索mouse > scroll whell down 那里写\j > scroll whell up那里写\k`
![](https://cdn.liushiming.cn/img/iterm2scroll.jpg)

## 备份配置
自己的配置最好备份好，下次有需要再导入即可，体验保持一致。
`Preferences > Profiles > Other Actions > Save All Profiles as JSON...`
![](https://cdn.liushiming.cn/img/20200112201400.png)

导出的配置文件可以放到dotfiles上传到github保存（但如果保存了密码之类的配置就不要上传了），换电脑的时候再也不用担心软件用不顺手了。

## 结论
* 通过简单配置，iterm2变得更美观易用，对中文支持也更好。
* 将配置保存到dotfiles，使用github管理有助于在任何机器上保持一致的体验。

