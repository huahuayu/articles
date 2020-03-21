[//title]:(centos安装mysql5.7)
[//englishTitle]:(how-to-install-mysql-57-on-linux-centos)
[//category]:(linux,centos,mysql)
[//tags]:(linux,centos,mysql)
[//createTime]:(2020-03-21)
[//updateTime]:(2020-03-21)

## 概述
本文介绍如何在centos 7系统中安装mysql 5.7  

版本信息：
centos版本：release 7.7.1908
mysql版本：5.7.29  

如果是其他系统安装mysql可以[下载](https://dev.mysql.com/downloads/mysql/5.7.html#downloads)mysql官方的安装文件  

## step1 - 添加mysql yum repo
这是一个一次性操作，可以通过安装MySQL提供的RPM来执行  
``` bash 
# -- On CentOS and RHEL 7 -- 
yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm

# -- On CentOS and RHEL 6 -- 
yum localinstall https://dev.mysql.com/get/mysql57-community-release-el6-9.noarch.rpm

# -- On Fedora 27 -- 
dnf install https://dev.mysql.com/get/mysql57-community-release-fc27-9.noarch.rpm

# -- On Fedora 26 -- 
dnf install https://dev.mysql.com/get/mysql57-community-release-fc26-9.noarch.rpm

# -- On Fedora 25 -- 
dnf install https://dev.mysql.com/get/mysql57-community-release-fc25-9.noarch.rpm
```

如果要安装别的mysql版本，可以在[这里](https://dev.mysql.com/downloads/mysql/)获得相应的rpm链接  

## step2 - 安装mysql server
``` bash
# On CentOS and RHEL 7/6
yum install mysql-community-server

# On Fedora 27/26/25
dnf install mysql-community-server
```

以上命令会安装mysql server 5.7和所需要的依赖  

## stpe3 - 启动mysql
启动mysql  
``` bash  
systemctl start mysqld
```

设置开机启动  
``` bash
systemctl enable mysqld
```

查看mysql服务端口，默认为3306    
``` bash
netstat -plntu | grep mysqld
```

首次启动会生成临时密码，查看日志中的临时密码  
``` bash
grep 'A temporary password' /var/log/mysqld.log |tail -1
```

## step4 - mysql安全配置
执行`mysql_secure_installation`来做mysql初始安全配置    
``` bash
/usr/bin/mysql_secure_installation
```
输入上个步骤拿到的临时密码开始进入交互式配置  

可配置的选项有：

- 为root账户重设密码  
- 关闭root账户远程连接  
- 关闭匿名账户  
- 移除test数据库 

## step5 - 测试登录
``` bash
mysql -uroot -p
```

## step6 - 其他设置（可选）
### 更改mysql端口
如果不想用3306端口，可用在配置文件中自定义端口   
``` bash
vi /etc/my.cnf
```
在里面加上`port=xxxx`  

然后重启mysql服务
``` zsh
$ systemctl restart mysql
```

**注意：** centos的安全模块SElinux比较严格，它设定了mysql的可用端口为1186，3306以及63132~63164，所以端口最好设在这个范围里  
```
$ semanage port -l | grep mysqld
mysqld_port_t                  tcp      1186, 3306, 63132-63164
```

如果想自定义端口，请参考：[centos设置服务使用非标准端口](https://liushiming.cn/2020/03/21/use-selinux-port-labeling-to-allow-services-to-use-non-standard-ports/)  

## 参考资料
[MySQL 5.7 Reference Manual](https://dev.mysql.com/doc/refman/5.7/en/linux-installation-yum-repo.html)  
[How to Install MySQL 5.7 on Linux CentOS and Ubuntu](https://www.howtoforge.com/tutorial/how-to-install-mysql-57-on-linux-centos-and-ubuntu/)  
