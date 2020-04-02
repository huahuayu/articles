[//title]:(DHCP服务下手动设定ip地址)
[//englishTitle]:(DHCP-manual-ip-address-setting)
[//category]:(network)
[//tags]:(network,dhcp)
[//createTime]:(20200329)
[//updateTime]:(20200329)

## 概述
路由器开启DHCP之后，能手动设置某一台电脑的ip吗？  

答案是可以的。

但是设定的ip地址要在DHCP地址池之外，否则有可能造成局域网ip冲突。  

本文以Mac为例，介绍如何在DHCP路由下设定电脑ip地址。

## 查看路由器DHCP池
登录路由器后台，查看DHCP服务ip地址分配范围，可以自定义的  
![](https://cdn.liushiming.cn/img/20200329182100.png)  

## 设置电脑ip  
打开网络设置 --> 选择Advanced --> 选择TCP/IP --> 修改ip地址（ip地址要在DHCP地址池之外）  

![](https://cdn.liushiming.cn/img/20200329182408.png)

![](https://cdn.liushiming.cn/img/20200329182448.png)