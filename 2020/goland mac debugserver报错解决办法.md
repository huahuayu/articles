[//title]: (mac下goland-debug报错debugserver找不到解决办法)
[//englishtitle]: (goland-mac-debugserver-xcode)
[//category]: (mac,goland,xcode,go)
[//tags]: (mac,goland,debug,lldb-server,debugserver)
[//createtime]: (2020-11-07)
[//updatetime]: (2020-11-07)

## 问题

goland debug 的时候报错：

```text
could not launch process: debugserver or lldb-server not found: install XCode's command line tools or lldb-server
```

![](https://cdn.liushiming.cn/img/20201107205257.png)

## 解决办法

使用 `which debugserver` 或者 `lldb-server` 确实搜不到命令。

一开始我以为是 xcode 的问题。

网上有人说运行 `xcode-select --install` 命令。

少数人有用，我运行没用，运行报错:

![](https://cdn.liushiming.cn/img/20201107205619.png)

最终是 github 上面[这个 issue](https://github.com/go-delve/delve/issues/986#issuecomment-394632928)的解答帮助了我解决问题

执行下面命令即可

```bash
sudo mkdir -p /Library/Developer/CommandLineTools/Library/PrivateFrameworks/LLDB.framework/Versions/A/Resources/
sudo ln -s "/Applications/Xcode.app/Contents/SharedFrameworks/LLDB.framework/Versions/A/Resources/debugserver" /Library/Developer/CommandLineTools/Library/PrivateFrameworks/LLDB.framework/Versions/A/Resources/debugserver
```

我看了下`/Applications/Xcode.app/Contents/SharedFrameworks/LLDB.framework/Versions/A/Resources`这个目录下是有`debugserver`命令的，我把路径加到 `PATH` 中，还是没用。

运行上面的命令就好，不知道为什么。

## 参考资料

[lldb-server needs to be installed in \$PATH](https://github.com/go-delve/delve/issues/986)
