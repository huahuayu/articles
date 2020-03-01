[//title]:(解决vscode出现两个光标的问题)
[//englishTitle]:(vscode-two-cursors-in-vim-mode)
[//category]:(vscode,vim,problem-solved)
[//tags]:(vscode)
[//createTime]:(2020-01-20)
[//lastUpdateTime]:(2020-01-20)
## 问题概述
今天用vscode的vim模式编辑html文件时，发现光标移动时会自动出现两个光标。插入或者删除文字时，会同时修改两处地方。一开始我以为是vscode的新功能，只是我不会用，但是很快发现，真的是没法用。网上搜索一通在vscode的github issue里找到关闭多个光标的方法。

## 版本信息
vscode 1.41.1

## 问题重现
这个新feature的本意应该是自动识别相同tag，一起编辑，但是体验没做好（至少vim下编辑html体验没做好），而且还默认启用了，严重影响编辑。
![](https://cdn.liushiming.cn/img/vscode-multi-cursor-problem.gif)

## 解决方案
perference > 搜索`html.mirrorCursorOnMatchingTag` > 去掉勾选
![](https://cdn.liushiming.cn/img/disable-vscode-mirror-cursor.jpg)


## 参考链接
https://github.com/Microsoft/vscode/issues/66634
