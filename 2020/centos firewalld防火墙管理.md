[//title]:(centos-firewalld防火墙管理)
[//englishTitle]:(centos-firewalld-management)
[//category]:(linux,centos,network)
[//tags]:(linux,centos,firewalld,network,zone,service,port)
[//createTime]:(2020-03-21)
[//updateTime]:(2020-03-27)

## 概述
centos 7默认使用`firewalld`作为防火墙服务。firewalld与iptables不兼容，它们不应该一起使用。

同时运行这两个服务将会搞乱防火墙, 所以只能选择一个。  

本文介绍`firewalld`的使用。  

## zone 
在`firewalld`中有zone的概念，zone是一套预定义的防火墙配置，不同的场景可使用不同的zone。

按可信度从高到低排列，有以下默认zone    

| 名字     | 描述                                                                                                                                         |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Trusted  | 完全信任环境，不过滤任何流量，在信任程度上，它排第一，它不会过滤任何内容。                                                                   |
| Home     | 在家庭网络中使用，它开启以下服务:ssh、mdns、ipp-client、samba-client和dhcpv6-client。                                                        |
| Internal | 内部网络中使用，和家庭网络类似，但它还允许以下服务:ssh、mdns、ipp-client、samba-client和dhcpv6-client。                                      |
| Work     | 工作网络使用，它允许以下服务返回:ssh、ipp-client和dhcpv6-client。                                                                            |
| Public   | 在公共网络中使用，它允许以下服务ssh和dhcpv6-client。这是默认启用的Zone。                                                                     |
| External | 用于伪装。你认为网络中其他的计算机不可信并且可能伤害你的计算机。只允许选中的连接接入。                                                       |
| Dmz      | 用以允许隔离区（dmz）中的电脑有限地被外界网络访问。只接受被选中的连接。                                                                      |
| Block    | 任何进入的网络连接都被拒绝，并返回 IPv4 的 icmp-host-prohibited 报文或者 IPv6 的 icmp6-adm-prohibited 报文。只允许由该系统初始化的网络连接。 |
| Drop     | 丢弃所有连接，不返回任何信息                                                                                                                 |

系统预定义的zone文件存储在`/usr/lib/firewalld/zones`目录下，而自定义zone文件需存储在`/etc/firewalld/zone`目录。  

### 查看zone列表
``` bash
$ firewall-cmd --list-all-zones
```

### 查看生效的zone配置
``` bash
$ firewall-cmd --list-all
```

### 改变默认zone
```
$ firewall-cmd --set-default-zone=work
```

该命令立即生效，且永久生效，无需reload  

### 查看指定网卡用哪个zone
先用`ifconfig`查看有哪些网卡，然后用下面的命令查看网卡使用哪个zone    
```
$ firewall-cmd --get-zone-of-interface=lo
```

### 给网卡设置zone
```
$ firewall-cmd --zone=public --add-interface=lo
```

### 改变网卡zone
```
$ firewall-cmd --zone=work --change-interface=lo
```

### 删除网卡zone
``` bash
firewall-cmd --zone=work --remove-interface=lo
```

### 查看所有网卡所在的zone
```
$ firewall-cmd --get-active-zones
work
  interfaces: lo
public
  interfaces: wlp3s0
```

## 服务
服务是firewalld概念中的次要关键元素。在zone文件中使用服务是管理防火墙规则的最佳方式。服务用于在区域文件中为相关网络服务创建预定义的规则。  

### 开放服务
开放http服务  
```
$ firewall-cmd --permanent --add-service=http
success
$ firewall-cmd --reload
success
```

预定义的服务可使用`ls /usr/lib/firewalld/services/`查看，这个目录下的xml不应该去动的。  

可以将`/usr/lib/firewalld/services/`下的xml拷贝到`/etc/firewalld/services/`然后再修改来自定义服务。  

### 关闭服务
关闭http服务  
```
$ firewall-cmd --permanent --remove-service=http
success
$ firewall-cmd --reload
success
```

### 查看开放的服务
```
$ firewall-cmd --list-services
dhcpv6-client http ssh
```

### 查看服务的标准端口
使用`semanage port -l | grep [service_name]`查看服务的标准端口（默认允许使用）  
``` bash
$ semanage port -l | grep mysqld
mysqld_port_t                  tcp      1186, 3306, 63132-63164
```

### 设置服务使用非标准端口
参见[centos设置服务使用非标准端口](https://liushiming.cn/2020/03/21/use-selinux-port-labeling-to-allow-services-to-use-non-standard-ports/)

## 端口
### 开放端口
开放单个端口 
``` bash
$ firewall-cmd --permanent --add-port=63150/tcp
success
```

开放某个范围的端口
``` bash
$ firewall-cmd --permanent --add-port=200-300/tcp
success
```

reload生效
``` bash
$ firewall-cmd --reload
success
```


### 关闭端口
关闭单个/某个范围端口  
``` bash
$ firewall-cmd --permanent --remove-port=63150/tcp
success
$ firewall-cmd --permanent --add-port=200-300/tcp
success
```

reload生效  
``` bash
$ firewall-cmd --reload
success
```

### 查看开放的端口
```
$ firewall-cmd --list-ports
63150/tcp
```

## 服务 or 端口？ 
如果我们可以指定端口，为什么还要使用服务呢?  
因为在服务中可以配置一个服务使用多个端口，开启一个服务可以同时开启多个端口，便于管理。   

## 参考资料
[How To Open A Port In CentOS 7 With Firewalld](https://www.rootusers.com/how-to-open-a-port-in-centos-7-with-firewalld/)
[Firewalld Basic concepts Explained with Examples](https://www.computernetworkingnotes.com/rhce-study-guide/firewalld-basic-concepts-explained-with-examples.html)

