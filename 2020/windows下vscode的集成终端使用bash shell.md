[//title]: (windows下vscode的集成终端使用bash-shell)
[//englishtitle]: (use-bash-shell-as-windows-integrated-shell)
[//category]: (windows,bash,vscode)
[//tags]: (windows,vscode,shell,bash)
[//createtime]: (2020-10-29)
[//updatetime]: (2020-10-29)

## 概述

windows 下 vscode 集成的 terminal 为 windows cmd，对于用惯了 mac 的我来说用起来很痛苦。

所幸电脑里安装了 git bash，有时候要敲 bash 命令的时候还是可以打开 git bash 窗口来敲，但还是不方便，因为要离开 vscode 界面。

那我就在想 vscode 集成的终端是否可以替换为 git bash。

网上搜索一番，没想到真的可行，以下是方案。

## 解决方案

**step1:** 点击 左下角齿轮 -> settings 进入 vscode 设置界面

![](https://cdn.liushiming.cn/img/20201029110108.png)

**step2:** 在搜索栏 搜索 "Shell: Windows"

![](https://cdn.liushiming.cn/img/20201029110202.png)

**step3:** 在搜索结果中找到上图中 highlight 的那一项配置，点击 `Edit in setting.json`

**step4:** 找到你的 git bash 安装位置，我是安装在 D 盘，你可能安装在 C 盘，具体情况具体分析, 将`terminal.integrated.shell.windows`配置改为 `bash.exe`的路径（注意反斜杠要转义）

```json
{
  "editor.fontFamily": "SourceCodePro-Light, Consolas, SourceCodePro-Light",
  "[markdown]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "editor.formatOnSave": true,
  "workbench.colorTheme": "Solarized Dark",
  "editor.wordWrap": "on",
  "editor.minimap.enabled": false,
  "vim.useCtrlKeys": false,
  "terminal.integrated.fontFamily": "monospace",
  "terminal.integrated.shell.windows": "D:\\Program Files\\Git\\bin\\bash.exe"
}
```

**step5:** 重启 vscode, 再次打开 terminal 就是 git bash 了，爽！

## 参考资料

[vscode 将终端改为 Git bash](https://www.jianshu.com/p/efa734089206)
