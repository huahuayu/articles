[//title]:(PC作为公网服务器教程)
[//englishTitle]:(pc-as-a-server)
[//category]:(network,tutorial)
[//tags]:(公网ip)
[//createTime]:(20200310)
[//lastUpdateTime]:(20200320)
## 概述
家庭电脑和服务器最大的差别就是：

1. 没有公网ip  
1. 或者有公网ip也不固定，每次重启路由器或者断电就会重新分配ip  

今天偶然看到一个推文说广东电信可以自助获得公网ip（我原来一直以为要打电话申请），那就解决第一个问题了。而第二个问题可以用动态域名解决。  
所以我就来了兴致实验一番，本文将记录这个过程。    

## 获得公网ip
今天在twitter看到一个推文，说电信宽带账号前面加一个pub就可以分配到公网ip    
![](https://cdn.liushiming.cn/img/20200310230733.png)


我马上试了一下，果不其然，我是使用极路由，用手机很方便修改了一下宽带账号，加上了pub，可以联网  
![](https://cdn.liushiming.cn/img/20200310231227.png)    

## 验证公网ip
如何验证是否真的分配到公网ip？

### 方法一
看路由器的wan口地址和公网地址是否一致  
![](https://cdn.liushiming.cn/img/20200310232634.png)

非公网ip路由器wan口地址和公网地址不会一致    
![](https://cdn.liushiming.cn/img/20200310232450.png)

### 方法二
用浏览器访问ip138.com，查询到自己的公网ip    
![](https://cdn.liushiming.cn/img/20200310231844.png)  

然后在本地打开终端使用`traceroute`命令(查看数据包在网络上传输的情况)查看这个ip地址，如果只有一跳，说明路由器获得的是公网ip     
``` bash
$ traceroute 183.15.177.160
traceroute to 183.15.177.160 (183.15.177.160), 64 hops max, 52 byte packets
 1  183.15.177.160 (183.15.177.160)  8.231 ms  1.525 ms  1.551 ms
```

## 端口映射
路由器获得了公网ip，不是pc直接获得了公网ip（除非网线直连）。所以pc如果想作为服务器使用，还需要pc的端口和路由器的端口进行映射，好让访问到路由器上特定端口的流量会转发到pc上。  

极路由有端口转发的插件（应该很多路由器都有类似功能），可以直接将公网ip的请求转发到局域网下的某台机器，装好插件，填上本机局域网地址开启服务即可     

![](https://cdn.liushiming.cn/img/20200310233314.png)

## 服务测试
在本地起了一个web服务，端口8003  
![](https://cdn.liushiming.cn/img/20200310233821.png)  

telnet本地端口可以连通    
``` bash
$ telnet localhost 8003
Trying ::1...
Connected to localhost.
Escape character is '^]'.
```

telnet公网ip也能通  
``` bash
shiming@pro ➜  ~ telnet 183.15.177.160 8003
Trying 183.15.177.160...
Connected to 183.15.177.160.
Escape character is '^]'.
```

说明本机服务可以通过公网直接调用了  

## 动态域名
每次断电或者重启路由，公网ip还是会变化的。因此最好有个域名，每次ip变化的时候，域名解析也跟着动态变化。这样不管ip怎么变，只需要访问域名即可。

极路由有动态域名插件，可以绑定一个`jios.org`的二级域名：  
![](https://cdn.liushiming.cn/img/20200311120008.png)

如果自己有域名可以将自己的域名通过CNAME映射到到`jios.org`的二级域名上面。
