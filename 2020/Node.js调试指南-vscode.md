[//title]: (Node.js调试指南-vscode)
[//englishtitle]: (nodejs-debug-guide-vscode)
[//category]: (node,javascript,vscode)
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

本文描述如何使用 `vscode` 来调试 Node.js。

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

## 使用 vscode 调试

现在我们使用`vscode`来调试

### step1: 安装 Debugger for Chrome 插件

安装[Debugger for Chrome 插件](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)

这个插件的功能为：

- 设置断点，可以在源文件上直接打断点
- 提供变量 watch
- 提供控制台

![](https://cdn.liushiming.cn/img/20200515115319.png)

### step2: 配置 debugger

vscode 自带调试器，可以调试很多程序，但是需要通过 json 来配置。

![](https://cdn.liushiming.cn/img/20200515120226.png)

结合上图说明：

1. 点击运行调试的按钮
1. 点下配置按钮
1. 点击添加配置，在菜单中选择选择 Node launch program
1. 修改`program`参数，指定入口程序
1. 增加`args`参数（因为本例子是带命令行参数的），`["add", "--title=note1 --body=\"a lovely day\""]`，command 和 option 必须分开用逗号隔开写
1. 其他参数的含义：`type`程序类型，`request`指调试的方式是启动一个新程序（launch），还是调试一个已经启动的程序(attach)，`name`是配置名

参数还有很多，详细请参考 vscode 官方文档 [Launch configuration attributes](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_launch-configuration-attributes)

最终我们配置如下

```json
{
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch Program",
      "program": "${workspaceFolder}/bin/note",
      "args": ["add", "--title=note1 --body=\"a lovely day\""],
      "skipFiles": ["<node_internals>/**"]
    }
  ]
}
```

### step3: 打断点

打开`service/noteService.js`文件，在错误信息`duplicate title!`报出来的地方打一个断点。

![](https://cdn.liushiming.cn/img/20200515115947.png)

加了这个断点后，等会调试时程序运行到这里就会停住。

### step4: 发起调试

调试步骤，结合图片看：

1. 切换到运行/调试视图
1. 点击运行按钮
1. 程序会运行到断点位置
1. 通过浮动的运行工具栏来单步调试、恢复运行、或者终止调试

![](https://cdn.liushiming.cn/img/20200515140640.png)

## 参考资料

[Node.js Debugging Guide](https://nodejs.org/en/docs/guides/debugging-getting-started/)

[Node.js debugging in VS Code](https://code.visualstudio.com/docs/nodejs/nodejs-debugging)
