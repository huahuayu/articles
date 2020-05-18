[//title]: (WebStorm-Nodejs-Unresolved-function-or-method解决办法)
[//englishtitle]: (WebStorm-Nodejs-Unresolved-function-or-method-solution)
[//category]: (node,webstorm,jetbrains)
[//tags]: (webstorm,nodejs,jetbrains,Unresolved-function-or-method)
[//createtime]: (20200508)
[//updatetime]: (20200508)

## 问题概述

WebStorm 不能识别 Nodejs 导入的包中部分方法（但 vscode 没问题），问题表现为：

1. 方法下划了黄线
1. 自动补全功能不能用
1. 鼠标移上去报错"Unresolved function or method"

![](https://cdn.liushiming.cn/img/20200508135839.png)

## 环境信息

WebStorm 2020.1.1

MacOS 10.15.4

## 问题原因

官方回复说，问题的原因为：这些不能识别的属性、方法不是在模块中定义的，它们是在运行时动态添加的。所以 WebStorm 不能使用静态分析来解析它们。 原文参考[[Solved] Nodejs/Express: Unresolved function or method](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360001875360--Solved-Nodejs-Express-Unresolved-function-or-method)。

这是一个好几年没得到解决的问题，有很多人给官方提了 issue，但是一直没解决。

我看到的部分 issue：  
https://youtrack.jetbrains.com/issue/WEB-27212  
https://youtrack.jetbrains.com/issue/WEB-26542

## 解决办法

官方给了一个 workaround

`光标放置在要引入的包名上 -> 按option + enter键 -> 选择 install typescript definitions for better type information`

![](https://cdn.liushiming.cn/img/20200508140756.png)

做了以上动作应该就解决问题了。

同时在`Preferences -> Languages & Frameworks -> javascript -> library`可以看到刚才引入的 typescript。

![](https://cdn.liushiming.cn/img/20200508141937.png)

## 参考资料

[[Solved] Nodejs/Express: Unresolved function or method](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360001875360--Solved-Nodejs-Express-Unresolved-function-or-method)

[Using TypeScript community stubs](https://www.jetbrains.com/help/webstorm/configuring-javascript-libraries.html?_ga=2.34094183.733065705.1588767164-252264001.1585882587#)
