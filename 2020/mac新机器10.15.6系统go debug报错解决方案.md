[//title]: (mac新机器10.15.6系统go-debug报错解决方案)
[//englishtitle]: (mac-osx10.15.6-golang-stdlib.h-CoreFoundation-issue)
[//category]: (golang,mac)
[//tags]: (go,mac,头文件,cgo,CommandLineTools,stdlib)
[//createtime]: (20201109)
[//updatetime]: (20201109)

## 问题描述

全新 Macbook pro 2020 下午刚到手(20201109)，OSX 10.15.6 系统。

使用 goland 开发 go 项目，`go run main.go`没问题。

debug 报错：

```text
# runtime/cgo
_cgo_export.c:3:10: fatal error: 'stdlib.h' file not found
```

因为是新机器，我能确定就是 Mac 系统本身的问题。

网上各种解决方案众说纷纭，各位读者，不用找了，下面我要说的可能就是最好的解决方案。

## 解决办法

**step1:**

首先确保执行了`xcode-select --install`

**step2:**

C 头文件在这个路径`/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/`

只需要做一个软连接到`/usr/local/include/`

```bash
sudo ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/* /usr/local/include/
```

可能会有一些 warning，说文件已存在（我执行没有出现任何报错），不用管。

做了以上步骤后，再次 debug，还是报错，但是错误不一样了, wtf?

```text
# crypto/x509

/usr/local/go/src/crypto/x509/root_cgo_darwin_amd64.go:20:10: fatal error: 'CoreFoundation/CoreFoundation.h' file not found
#include <CoreFoundation/CoreFoundation.h>
^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/usr/local/go/src/crypto/x509/root_cgo_darwin_amd64.go:20:10: note: did not find header 'CoreFoundation.h' in framework 'CoreFoundation' (loaded from '/System/Library/Frameworks')
1 error generated.
```

**接下来的操作有点折腾!**

首先应该确定安装最新稳定版的 Xcode，或运行：

```bash
xcode-select --install
```

重启电脑，按住 Command+R 进入 恢复模式，在恢复模式中启动 terminal，输入：

```bash
csrutil disable
```

再次重启电脑，运行下面命令，把根目录挂载为可读写：

```bash
sudo mount -uw /
```

建立以下软链接：

```bash
cd /System/Library/Frameworks/CoreFoundation.framework
sudo ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreFoundation.framework/Headers Headers
sudo ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreFoundation.framework/Modules Modules
cd /System/Library/Frameworks/Security.framework
sudo ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Security.framework/Headers Headers
sudo ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Security.framework/Modules Modules
```

重新尝试 debug go 应用，成功！

## 参考资料

[Catalina 升级 error 'CoreFoundation/CoreFoundation.h'](https://zhuanlan.zhihu.com/p/122250460)
