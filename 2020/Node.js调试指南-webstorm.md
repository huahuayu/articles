[//title]: (Node.js调试指南-webstorm)
[//englishtitle]: (nodejs-debug-guide-webstorm)
[//category]: (node,javascript,webstorm)
[//tags]: (nodejs,javascript,debug,调试,chrome,devtools,vscode,webstorm)
[//createtime]: (20200515)
[//updatetime]: (20200515)

## 概述

学习任何一种编程语言，调试(debug)都是必须掌握的技能。

Node.js 的调试，我觉得有几个比较好的选择，我分开用三篇文章来说明：

- [使用 chrome devtools 调试](https://liushiming.cn/article/nodejs-debug-guide-chrome-devtools/)
- [使用 vscode 调试](https://liushiming.cn/article/nodejs-debug-guide-vscode/)
- [使用 webstorm 调试](https://liushiming.cn/article/nodejs-debug-guide-webstorm/)

调试工具对比

| 调试工具        | 特点                                          |
| --------------- | --------------------------------------------- |
| chrome devtools | 依赖少，原生支持                              |
| vscode          | 方便，编码，打断点，调试都在一个工具完成      |
| webstorm        | 最方便，功能强大，jetbrains 系列 IDE 统一体验 |

本文描述如何使用 `webstorm` 来调试 Node.js。

## 示例项目

让我们使用一个真实的小项目来调试，更贴近实际

[这里](https://github.com/huahuayu/node-cli-example)是我用 Node.js 写的一个便签程序，只有两个 js 文件，功能是对便签的增删改查。

把它克隆下来:

```text
git clone git@github.com:huahuayu/node-cli-example.git
cd node-cli-example
```

我们只执行一个功能：增加一个便签，标题为 note1，内容为"a lovely day"

```text
node ./bin/note add --title=note1 --body="a lovely day"
```

输出应该为

```text
note is added successfully! view at /tmp/note.json
```

说明便签已经添加成功，且已写入`/tmp/note.json`文件

再次执行同样的添加便签的命令，会报错`duplicate title!`，说明便签标题重复了，不能添加重复的标题。

```text
$ node ./bin/note add --title=note1 --body="a lovely day"
duplicate title!
```

我们就来试着调试一下这个错误，看下它是怎么报出来的

## 背景知识 - Node.js 调试的基本原理

请参考上一篇文章[《Node.js 调试指南-chrome devtools》](https://liushiming.cn/article/nodejs-debug-guide-chrome-devtools/#%E8%83%8C%E6%99%AF%E7%9F%A5%E8%AF%86_%E2%80%93_Node_js_%E8%B0%83%E8%AF%95%E7%9A%84%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86)，本文不再赘述。

## 使用 webstorm 调试

现在我们使用`webstorm`来调试，`webstorm`调试是最简单的

### step1: 配置运行 profile

配置运行 profile 就是要告诉 webstorm，怎么使项目跑起来，应用入口在哪，要传入哪些参数等等

结合图片，我们来配置运行 profile：

1. 进入`run -> edit configurationns`
1. 添加一个运行配置
1. 在`JavaScript file`填写程序入口
1. 在`Application parameters`填写程序运行参数（如有）
1. 在`name`处填写 profile 名字（一般会根据`JavaScript file`带出来）

![](https://cdn.liushiming.cn/img/20200515151445.png)

### step2: 打断点

打开`service/noteService.js`文件，在错误信息`duplicate title!`报出来的地方打一个断点。

![](https://cdn.liushiming.cn/img/20200515152055.png)

加了这个断点后，等会调试时程序运行到这里就会停住。

### step3: 发起调试

从顶部菜单栏，点击`Run -> Debug 'note'`

![](https://cdn.liushiming.cn/img/20200515152300.png)

调试界面介绍：

1. 断点
1. 单步调试控制
1. 变量 watch
1. 恢复运行、重跑、停止调试功能

![](https://cdn.liushiming.cn/img/20200515152718.png)

## 参考资料

[Node.js Debugging Guide](https://nodejs.org/en/docs/guides/debugging-getting-started/)

[Run/Debug Configuration: Node.js](https://www.jetbrains.com/help/webstorm/run-debug-configuration-node-js.html)
