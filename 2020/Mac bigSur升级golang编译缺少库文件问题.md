[//title]: (Mac-bigSur升级后go编译问题)
[//englishtitle]: (mac-bigsur-update-golang-build-library-error)
[//category]: (go,mac)
[//tags]: (go,mac)
[//createtime]: (20201230)
[//updatetime]: (20201230)

## 问题描述

升级了 Mac Big Sur, golang cgo 依赖的头文件找不到，编译报错。未升级系统之前做的头文件软链接全部失效了。`/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/`这下面的目录都被重写了。需要按照我[这篇文章](https://liushiming.cn/article/mac-osx10-15-6-golang-stdlib-h-corefoundation-issue.html)来设置。

## 解决办法

简单来说需要以下步骤：

**step1:** 重启 mac，按住 command + r 进入恢复模式，运行命令

```
csrutil disable
```

**step2:** 再次重启 mac，把根目录挂载为可读写

```
sudo mount -uw /
```

**step3:** 然后建立以下软链接

```
cd /System/Library/Frameworks/CoreFoundation.framework
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreFoundation.framework/Headers Headers
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreFoundation.framework/Modules Modules
cd /System/Library/Frameworks/Security.framework
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Security.framework/Headers Headers
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Security.framework/Modules Modules
cd /System/Library/Frameworks/CoreServices.framework
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreServices.framework/Headers Headers
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreServices.framework/Modules Modules
```

但是升级完 `big sur` 之后，挂载根目录报错（即**step2**）：

```bash
sudo mount -uw /
mount_apfs: volume could not be mounted: Permission denied
mount: / failed with 66
```

目前看解决根目录不能写的办法是[这个](https://apple.stackexchange.com/questions/395508/can-i-mount-the-root-system-filesystem-as-writable-in-big-sur)（第一个回答) 但是我没有尝试。**尝试前请做好备份。** 有效果请告诉我一下。
