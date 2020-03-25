[//title]:(linux修改ssh服务端口)
[//englishTitle]:(modify-sshd-port)
[//category]:(linux,ssh)
[//tags]:(linux,ssh)
[//createTime]:(20200325)
[//updateTime]:(20200325)

## 概述
本文描述如何修改ssh服务端口  

## 修改配置
``` bash
$ vi /etc/ssh/sshd_config
```
将`Port 22` 改成想要的端口，如`Port 2202`

## 重启sshd服务
``` bash
$ systemctl restart sshd
```

## 查看端口情况
``` bash
$ nginx netstat -tulpn | grep sshd
tcp        0      0 0.0.0.0:2202            0.0.0.0:*               LISTEN      9576/sshd
```

## 测试连接
``` bash
$ telnet your_domain.com 2202
```

## 开启防火墙
如果telnet不通，可能是防火墙未开启端口  

### firewalld防火墙 
``` bash
$ firewall-cmd --permanent --add-port=2202/tcp
$ firewall-cmd --reload
```

### 阿里云防火墙
在控制台修改  
![](https://cdn.liushiming.cn/img/20200324134612.png)
