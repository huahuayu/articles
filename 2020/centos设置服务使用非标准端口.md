[//title]:(centos设置服务使用非标准端口)
[//englishTitle]:(use-selinux-port-labeling-to-allow-services-to-use-non-standard-ports)
[//category]:(centos,selinux)
[//tags]:(centos,selinux,port)
[//createTime]:(2020-03-21)
[//updateTime]:(2020-03-21)

## 概述
centos下的SELinux有一系列默认的安全规则，SELinux允许特定的服务使用特定的端口（且只能使用这些端口），这样做可以防止恶意程序绑定在预定义的的端口来增强系统安全性。  

如要使用非标准的端口，需要修改SELinux的端口配置  

## 非默认端口启动服务
mysql默认端口为3306，修改端口为3289后通过`systemctl restart mysqld`重启报错: 
```
$ systemctl restart mysqld
Job for mysqld.service failed because the control process exited with error code. See "systemctl status mysqld.service" and "journalctl -xe" for details.
```

`journalctl -xe`查看错误日志  
```
$ journalctl -xe
SELinux is preventing /usr/sbin/mysqld from name_bind access on the tcp_socket port 3289.
```

## 查看默认端口
`semanage port -l`可以查看服务和端口绑定列表， 使用`semanage port -l | grep mysqld`查看mysql可用的端口情况   
``` bash
$ semanage port -l | grep mysqld
mysqld_port_t                  tcp      1186, 3306, 63132-63164
```

mysql默认能使用1186，3306以及63132~63164间的端口  

如果修改mysql端口为1186，则能正常启动  
``` bash
$ vi /etc/my.cnf
$ systemctl restart mysqld
$ netstat -plntu | grep mysqld
tcp6       0      0 :::1186                 :::*                    LISTEN      21053/mysqld
```

## 使用非标准端口
将非标准端口添加到允许的端口列表命令为  
``` bash
semanage port -a -t PORT_TYPE -p tcp PORT  # PORT_TYPE 为端口的标签，PORT是想要添加的端口号  
```

为了将mysql更改为使用非标准端口，我们必须更改SELinux策略并指定允许使用特定端口，以3289为例    
``` bash
$ semanage port -a -t mysqld_port_t -p tcp 3289
$ semanage port -l | grep mysqld
mysqld_port_t                  tcp      3289, 1186, 3306, 63132-63164
```

在SElinux配置了mysql的可用端口之后就可以使用自定义的端口了   
``` bash
$ vi /etc/my.cnf
$ systemctl restart mysqld
$ netstat -plntu | grep mysqld
tcp6       0      0 :::3289                 :::*                    LISTEN      27137/mysqld
```

或者，如果你安装了GUI，那么可以通过`yum install policycoreutils-gui`安装SELinux configuration GUI包，然后运行`system-config-selinux`命令来打开图形界面。在左侧的网络端口菜单中，可以配置SELinux端口，如下所示  
![](https://cdn.liushiming.cn/img/20200321155342.png)

## 参考资料
[Use SELinux Port Labeling To Allow Services To Use Non-Standard Ports](https://www.rootusers.com/use-selinux-port-labeling-to-allow-services-to-use-non-standard-ports/)
