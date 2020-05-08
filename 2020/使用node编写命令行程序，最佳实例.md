[//title]: (使用node编写命令行程序，最佳实例)
[//englishtitle]: (node-yargs-cli-in-practice)
[//category]: (node,bash)
[//tags]: (node,yargs,cli,命令行程序,command)
[//createtime]: (20200507)
[//updatetime]: (20200507)

## 概述

`note`是一个非常简单的，用`yargs`开发的命令行程序，它的功能有：

1. 新建便签
1. 查询便签
1. 删除便签

## 快速开始

**安装**

```bash
git clone git@github.com:huahuayu/node-cli-example.git
cd node-cli-example
npm install -g
```

**查看帮助**

使用`node --help`查看帮助

```text
$ node --help
Usage: note [add | get | del] [--title | --body]

Commands:
  note add  create a note
  note get  get a note
  note del  delete a note

Options:
  -h, --help     Show help                                             [boolean]
  -v, --version  Show version number                                   [boolean]

Examples:
  add  note add --title=monday_note --body=a_wonderful_day
  add  note add -t monday_note -b a_wonderful_day
  get  note get -t monday_note
  del  del -t monday_note
```

## 程序特点

- 简洁（仅 80 行），依赖少(只依赖 `yargs`)
- 有必要的帮助文档（包含 Usage、常用命令、常用 option 等）
- 可以查看版本 (`note -v`)
- 有常用的示例
- 参数支持简写（如 `-h`等价于`--help`）
- 设置参数类型（string,number 之类）
- 设置参数选输/必输
- 设置参数默认值
- 敲错命令或参数的时候有提示
- 有子命令帮助（如: `note add -h`）

## 代码讲解

[note.js](https://github.com/huahuayu/node-cli-example/blob/master/note.js)

```js
#!/usr/bin/env node
const yargs = require("yargs");
const log = console.log;

// 如果不在程序中指定，note -v输出的版本号为package.json中的版本号
// 如果程序中指定了version，则note -v输出程序中指定的版本号
yargs.version("1.1.0");

// 定义命令，add, get, del
yargs.command({
    // 命令名
    command: "add",
    // 命令描述，用来生成帮助文档中的描述
    desc: "create a note",
    builder: {
        // 命令参数，便签标题
        title: {
            // 参数描述，用来生成帮助文档
            desc: "note title",
            // true-必输，false-选输
            demandOption: true,
            // 参数类型，帮助文档会有体现
            type: "string",
        },
        // 第二个参数，便签内容
        body: {
            desc: "note content",
            demandOption: false,
            type: "string",
            // 因为第二个参数是非必输项，所以设置一个默认值
            default: "default value",
        },
    },
    // 命令被触发后执行的功能
    handler: function () {
        log(
            "adding a new note, title: " +
            yargs.argv.title +
            " body: " +
            yargs.argv.body
        );
    },
});

yargs.command({
    command: "get",
    desc: "get a note",
    builder: {
        title: {
            desc: "note title",
            demandOption: true,
            type: "string",
        },
    },
    handler: function () {
        log("geting the note, title: " + yargs.argv.title);
    },
});

yargs.command({
    command: "del",
    desc: "delete a note",
    builder: {
        title: {
            describe: "note title",
            demandOption: true,
            type: "string",
        },
    },
    handler: function () {
        log("deleting the note, title: " + yargs.argv.title);
    },
});

yargs
    // 设置Usage
    .usage("Usage: note [add | get | del] [--title | --body]")
    // 设置参数简写
    .alias("t", "title")
    .alias("b", "body")
    .alias("h", "help")
    .alias("v", "version")
    // 设置example
    .example("add", "note add --title=monday_note --body=a_wonderful_day")
    .example("add", "note add -t monday_note -b a_wonderful_day")
    .example("get", "note get -t monday_note")
    .example("del", "del -t monday_note");

// 设置有效性检查： 1.至少一个参数 2.严格检查模式
yargs.demandCommand(1).strict();

// 解析命令行参数
yargs.parse();
```

## 卸载

```bash
rm /usr/local/bin/note
```

## 参考资料

[yargs error handling](https://github.com/yargs/yargs/issues/883)
