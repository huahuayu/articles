[//title]: (vscode最佳实践-好用的插件和配置推荐)
[//englishtitle]: (vscode-best-practice)
[//category]: (vscode,vim,best-practice)
[//tags]: (vscode,vim,插件,配置,最佳实践,prettier,中文,多项目,版本控制,git,时间统计,docker)
[//createtime]: (20200516)
[//updatetime]: (20200516)

## 概述

vscode 作为近年来异军突起的一个文本编辑器，攫取了越来越大的市场份额。我自己使用 vscode 的频率也越来越高，`vscode` + `vim`插件用的十分顺手。

我觉得现在有必要将 vscode 使用的一些小技巧记录下来，在这件事上投入一些时间和精力，以便在这个强大的工具上获得更好的长期回报。

本文的内容可能会很跳跃，我发现了好用的功能就会记下来，随时更新。

对于好用的定义，每个人都有自己的看法。如果你觉得 vscode 有哪些点好用的功能/插件，欢迎留言告诉我。

## 版本信息

我使用的 vscode 环境为 mac，如果你用的是 window/linux，有些快捷键可能不一样。

我使用的 vscode 版本信息:

```text
版本: 1.45.0
提交: d69a79b73808559a91206d73d7717ff5f798f23c
日期: 2020-05-07T15:57:33.467Z
Electron: 7.2.4
Chrome: 78.0.3904.130
Node.js: 12.8.1
V8: 7.8.279.23-electron.0
OS: Darwin x64 19.4.0
```

## vim 插件

如果你是 vim 用户，我强烈建议你装上 vscode 的 [vim 插件](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim)。

如果你不是 vim 用户，我强烈建议你去学习 vim。

学习 vim 是我这辈子回报最高的时间投资，你可以认为它就是一套文本编辑快捷键，学会了终身受益，编辑效率大大提高。

在命令行上就不用说了，vim 是绝对主流编辑器。在图形界面上，主流的文本编辑器和 IDE 都有 vim 插件，只要装上 vim 插件，**你不需要再投入时间学习另一套快捷键**，一切都是熟悉的感觉。

这种跨系统，跨平台，跨软件，使用同一套热键的感觉超级棒。

![](https://cdn.liushiming.cn/img/20200516163247.png)

## 命令面板

使用`command + shift + p`打开 `Command Palette`(命令面板)，这是 vscode 最重要的热键（[参考](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)）。

通过命令面板可以访问 vscode 几乎所有的设置，想要什么功能直接在它提供的搜索栏上搜索即可，只有你想不到没有做不到。

## 中文支持

vscode 默认是英文，但是微软官方也出了多语种支持，通过安装插件的方式可支持中文，我觉得这个汉化做的很好。微软的汉化一向做得不错，毕竟 windows 系统和 office 的经验摆在这。

在扩展商店中搜索 Chinese，安装即可。

![](https://cdn.liushiming.cn/img/20200516161012.png)

另一种安装和切换语言的方法，按`command + shift + p`，搜索`configure display language`。

![](https://cdn.liushiming.cn/img/20200516151211.png)

按`enter`，选择切换语言或者安装其他语言。

![](https://cdn.liushiming.cn/img/20200516151332.png)

## prettier 格式化

[prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) 是一款代码格式化软件，配合 vscode 设置 `format on save` 使用非常方便，每次保存文件时都会根据文件类型自动格式化，这让我感到很愉悦。

在团队协作中，保持同样的代码格式至关重要。

![](https://cdn.liushiming.cn/img/20200516165257.png)

保存时自动格式化设置：`preferences -> 搜索 format -> Default Formatter选择esbenp.prettier-vscode -> Format On Save勾上`

![](https://cdn.liushiming.cn/img/20200516165623.png)

## 多项目切换

我经常用 vscode 打开多个项目，在 mac 下切换多个项目时我觉得很麻烦。

以前我的做法是 `dock 找到 vscode 图标 -> 右键点击 -> 选择想要打开的文件或目录`，相信大部分人跟我一样。

直至我发现了[Projetct Manager](https://marketplace.visualstudio.com/items?itemName=alefragnani.project-manager)这个插件。

![](https://cdn.liushiming.cn/img/20200516161254.png)

它可以在左侧边栏存放常用的项目，要切换项目时点一下就可以，非常方便。

![](https://cdn.liushiming.cn/img/20200516152752.png)

如何添加项目？

在当前项目按`command + shift + p` 输入 `save project`点击保存。

![](https://cdn.liushiming.cn/img/20200516153048.png)

## 版本控制增强

[GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) 是对 vscode 原生版本控制的增强，主要提供 git blame 功能，可以清晰的看到每一个行代码是谁改的，拒绝甩锅。

![](https://cdn.liushiming.cn/img/20200516162033.png)

## sonarlint 代码风格检查

[sonarlint](https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarlint-vscode) 是一款代码检查工具，在 vscode, jetbrains 都有插件，提供一致的用户体验。

相比于别的 lint 软件来说，sonarlint 的优势在于它可以和 sonarqube 配合使用。sonarqube 是很多公司都在用的中心化代码质量分析工具。

![](https://cdn.liushiming.cn/img/20200516185020.png)

## 编程时间统计

[Code Time](https://marketplace.visualstudio.com/items?itemName=softwaredotcom.swdc-vscode) 这款插件可以统计你的编码时间，而且它还同时支持`Jetbrains`系列 IDE，两边可以使用同一个账号登录。这样一来保留的记录就比较完整了。

很酷，不过其实也没什么卵用。

![](https://cdn.liushiming.cn/img/20200516161541.png)

## docker 插件

[docker 插件](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)是微软官方开发的一款配合 docker 使用的强大的插件。

![](https://cdn.liushiming.cn/img/20200516191609.png)

docker 插件可以支持编辑、生成 docker file；可以查看容器，运行镜像等等。

![](https://cdn.liushiming.cn/img/docker-view-context-menu.gif)

## 发现更多优秀插件

[vscode marketplace](https://marketplace.visualstudio.com/vscode) 是 vscode 插件在线商店，在这里可以浏览最受欢迎的插件。
