[//title]: (Mac-Wireshark-you-don't-have-permission-to-capture解决办法)
[//englishtitle]: (Wireshark-you-don't-have-permission-to-capture)
[//category]: (wireshark,network,mac)
[//tags]: (wireshark)
[//createtime]: (20200421)
[//updatetime]: (20200421)

## 问题描述

Wireshark 安装后界面有一个提示：you don't have permission to capture ，即无权限监控网络  
![](https://cdn.liushiming.cn/img/20200421113250.png)

## 版本信息

MacOS 版本：10.15.4  
Wireshark 版本: v3.2.3

## 解决办法

**第一步**
按照提示先把 ChmodBPF 安装了

**第二步**
重启 Wireshark 试下有没有效果

**第三步**  
如果没有效果，运行下面的命令，将当前用户加入到`access_bpf`用户组中去

```bash
sudo dseditgroup -o edit -a `whoami` -t user access_bpf
```

重启 Wireshark，应该没问题了

## 参考资料

[stackoverflow](https://stackoverflow.com/questions/41126943/wireshark-you-dont-have-permission-to-capture-on-that-device-mac)
