[//title]: (virtualbox虚拟机桥接无法联网解决办法)
[//englishtitle]: (virtualbox-vm-bridge-network-connect-internet-solution)
[//category]: (network,virtual-machine)
[//tags]: (virtualbox,bridgenetwork,联网,桥接)
[//createtime]: (20201109)
[//updatetime]: (20201109)

## 问题概述

windows 下 virtualbox 安装 ubuntu 虚拟机。

NAT 模式下，虚拟机能上网，但是主机无法连虚拟机。

桥接模式下，主机可以连虚拟机，但是虚拟机不能上网。

我需要的是桥接模式，且虚拟机可以访问互联网。

## 解决办法

**step1: 添加网络适配器**

windows cmd -> 输入 hdwwiz，添加新硬件

![](https://cdn.liushiming.cn/img/20201109104706.png)

![](https://cdn.liushiming.cn/img/20201109104836.png)

![](https://cdn.liushiming.cn/img/20201109104847.png)

![](https://cdn.liushiming.cn/img/20201109104858.png)

**step2: 设置网络共享**

![](https://cdn.liushiming.cn/img/20201109105025.png)

![](https://cdn.liushiming.cn/img/20201109105035.png)

**step3: 设置虚拟机网络**

![](https://cdn.liushiming.cn/img/20201109105159.png)

**效果**

curl 访问互联网可以通了

![](https://cdn.liushiming.cn/img/20201109105327.png)

内网 ip 地址也可以通过主机 ping 通

![](https://cdn.liushiming.cn/img/20201109105447.png)

## 参考资料

[解决 Win10 Virtualbox5.2.18 桥接不能联网小记](https://www.cnblogs.com/hujiapeng/p/9575213.html)
