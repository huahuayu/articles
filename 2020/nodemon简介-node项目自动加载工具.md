[//title]: (nodemon简介-node项目自动加载工具)
[//englishtitle]: (nodemon-introduction-node-autoreload)
[//category]: (node,tools)
[//tags]: (node,nodemon,webstorm,自动加载,autoreload)
[//createtime]: (20200507)
[//updatetime]: (20200507)

## 概述

[nodemon](https://www.npmjs.com/package/nodemon) 用来监视 node.js 应用程序中的文件的任何更改并自动重启服务，非常适合用在开发环境中。 nodemon 将监视启动目录中的文件，如果有任何文件更改，nodemon 将自动重新启动 node 应用程序。

nodemon 不需要对代码或开发方式进行任何更改。 nodemon 只是简单的包装你的 node 应用程序，并监控任何已经改变的文件。

## 使用教程

1. 全局安装

```bash
npm install -g nodemon
```

2. 使用 nodemon 启动应用

以前是使用`node app.js`现在改成

```bash
nodemon app.js
```

3. 在 webStorm 中使用

首先通过`which nodemon`获得二进制包位置，再填入 webStorm 项目的`Run/Debug Configurations`设置中

![](https://cdn.liushiming.cn/img/20200507071241.png)

## 参考资料

[nodemon 使用简介](https://gitpress.io/@rainy/nodemon)
