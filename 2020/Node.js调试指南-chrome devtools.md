[//title]: (Node.js调试指南-chrome-devtools)
[//englishtitle]: (nodejs-debug-guide-chrome-devtools)
[//category]: (node,javascript,chrome)
[//tags]: (nodejs,javascript,debug,调试,chrome,devtools,vscode,webstorm)
[//createtime]: (20200514)
[//updatetime]: (20200514)

## 概述

学习任何一种编程语言，调试(debug)都是必须掌握的技能。

Node.js 的调试，我觉得有几个比较好的选择，我分开用三篇文章来说明：

- 使用 chrome devtools 调试
- 使用 vscode 调试
- 使用 webstorm 调试

本文描述如何使用 chrome devtools 来调试 Node.js。

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

在运行 Node.js 文件时，加上`--inspect`选项，Node.js 就会起一个进程监听调试客户端，默认监听`127.0.0.1:9229`，而且每个进程还被分配一个惟一的 UUID。监听地址形如`ws://127.0.0.1:9229/f9ce8dfa-ffff-4361-8155-3b1ed20df998`。

```text
$ node --inspect ./bin/note add --title=note1 --body="a lovely day"
Debugger listening on ws://127.0.0.1:9229/f9ce8dfa-ffff-4361-8155-3b1ed20df998
For help, see: https://nodejs.org/en/docs/inspector
duplicate title!
```

想要调试 Node.js 程序，调试客户端必须要知道这个监听地址，才能监控程序的执行、设置断点等等。这就是 Node.js 调试的基本原理。

[官方文档](https://nodejs.org/en/docs/guides/debugging-getting-started/#inspector-clients)上介绍的调试客户端有：

- `node-inspect`命令行调试器
- `chrome devtools`和`microsoft edge`浏览器
- `visual studio code`
- `jetbrains webstorm`
- `gitpod`
- `eclipse`

## 使用 chrome 调试

现在我们使用`chrome devtools`来调试

### step1: 打断点

打开`service/noteService.js`文件，在错误信息`duplicate title!`的上面，加上`debugger`关键字。

```js
/**
 * 新增note
 * @param argv
 */
const add = (argv) => {
  const notes = loadNotes(argv.notePath);

  // 找出相同标题的note, 如果没有重复的标题，duplicateTitleNote 等于 undefined
  const duplicateTitleNote = notes.find((note) => note.title === argv.title);

  debugger;

  if (duplicateTitleNote) {
    console.log("duplicate title!");
    return;
  }

  notes.push({，
    title: argv.title,
    body: argv.body,
  });

  try {
    fs.writeFileSync(argv.notePath, JSON.stringify(notes, null, 2));
    console.log("note is added successfully! view at " + argv.notePath);
  } catch (e) {
    console.log("fail to add note: " + e.message);
    process.exit(1);
  }
};
```

手动加了这个断点后，等会调试时程序运行到这里就会停住。

### step2: 使用 node inspect 命令运行程序

使用命令`node inspect ./bin/note add --title=note1 --body="a lovely day"`运行程序，进入了 debug 模式

程序运行到第一行就停住了，等待下一步指令

```text
$ node inspect ./bin/note add --title=note1 --body="a lovely day"
< Debugger listening on ws://127.0.0.1:9229/a59eb45e-26c9-4ad4-afa0-83c4d598a5cf
< For help, see: https://nodejs.org/en/docs/inspector
< Debugger attached.
Break on start in bin/note:2
  1 #!/usr/bin/env node
> 2 const yargs = require("yargs");
  3 const noteService = require("../service/noteService");
  4 const fs = require("fs");
debug>
```

### step3: 使用 chrome devtools

打开 chrome，访问`chrome://inspect`

可以看到 chrome 已经捕获到了调试请求

![](https://cdn.liushiming.cn/img/20200514211537.png)

点击`inspect`就可以进入`chrome devtools`

![](https://cdn.liushiming.cn/img/20200514211930.png)

对照上图，介绍如下：

1. 运行控制区
1. watch 查看变量
1. 断点列表
1. 程序目前运行到的位置
1. 项目文件区，要自己添加项目根目录进来
1. 控制台

点一下 1 区的运行按钮，程序就会运行到断点，watch `duplicateTitleNote`发现确实是重复了

![](https://cdn.liushiming.cn/img/20200514212623.png)

按下 `f8` 或`command + \`继续运行程序至结束

### step4: 再次调试

`debug`结束后，想再次调试，在`debug`命令行处再敲一个 "restart" 即可，又可以在 chrome 调试了。

![](https://cdn.liushiming.cn/img/20200514213330.png)

## 参考资料

[Node.js Debugging Guide](https://nodejs.org/en/docs/guides/debugging-getting-started/)
