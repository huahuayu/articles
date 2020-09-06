[//title]: (使用vscode调试bash脚本)
[//englishtitle]: (debug-Bash-on-macOS)
[//category]: (mac,bash,vscode)
[//tags]: (mac,bash,debug,vscode)
[//createtime]: (2020-09-06)
[//updatetime]: (2020-09-06)

## 概述

复杂的 bash 脚本怎么调试？可以用 vscode 安装 bash debug 插件调试。步骤如下 ：

## 安装 bash debug 插件

插件地址：https://marketplace.visualstudio.com/items?itemName=rogalmic.bash-debug

## 前提条件

- Mac
  - 系统安装的 bash 版本 > 4.x (mac 自带版本为 3.2，需要手动升级，参考[Mac 升级最新版 bash](https://liushiming.cn/article/upgrading-bash-on-macos.html))
- Windows
  - 未测试

## 配置 debug profile

截图如下，右键点击->新标签打开图片->查看大图

![](https://cdn.liushiming.cn/img/20200906195556.png)

对照截图：

**step1:** 进入 debug 视图

**step2:** 点击设置

**step3:** 点击添加 debug 配置，选第一个就好了 Bash-Debug (hardcoded script name)

![](https://cdn.liushiming.cn/img/20200906194938.png)

**step4:** 填写 debug 配置

- cwd: 工作目录
- program: 需要调试的 bash 脚本路径（相对工作目录来说）
- args: 脚本参数，字符数组形式传入

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "bashdb",
      "request": "launch",
      "name": "Bash-Debug (hardcoded script name)",
      "cwd": "${workspaceFolder}/test-network",
      "program": "network.sh",
      "args": ["up", "-a", "--verbose"]
    }
  ]
}
```

**step5:** 点击进入调试
