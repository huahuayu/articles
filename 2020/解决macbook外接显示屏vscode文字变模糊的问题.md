[//title]:(解决macbook外接显示屏vscode文字变模糊的问题)
[//englishTitle]:(vscode-font-rendering-blurry)
[//category]:(vscode,problem,problem-solved)
[//tags]:(vscode)
[//createTime]:(20200120)
[//lastUpdateTime]:(20200120)
## 概述
一段时间来我发现我的macbook pro外接显示器的时候，不管是在公司还是家里，如果vscode是开着的，再接显示屏里面的字会变模糊。只有关闭vscode再重新打开文字才清晰。别的应用没有发现这样的情况。

## 环境说明
vscode 1.41.1
macOS 10.15.2
外接显示器 dell u2412m

## 解决方法
在terminal中执行以下命令，重启vscode，以后再连屏幕应该不会了。
```
defaults write com.microsoft.VSCode.helper CGFontRenderingFontSmoothingDisabled -bool NO
```

在这个[issue](https://github.com/microsoft/vscode/issues/51132#issuecomment-542357538)里贡献者在2019年10月16号说已经在最新版里面修复了，但是我的vscode当前就是最新版，不知道为什么没有修复。总之执行以上命令可以解决问题。

## 参考链接
* [vscode github issue](https://github.com/microsoft/vscode/issues/51132#issuecomment-424299892)
* [Fixing Terrible Blurry Font Rendering Issue in macOS Mojave](https://dev.to/mrahmadawais/onedevminute-fixing-terrible-blurry-font-rendering-issue-in-macos-mojave--lck)
