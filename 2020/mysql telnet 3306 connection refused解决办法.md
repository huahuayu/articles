[//title]:(mysql-telnet-3306-connection-refused解决办法)
[//englishTitle]:(mysql-telnet-3306-connection-refused-solution)
[//category]:(mysql,troubleshoot,problem-solved)
[//tags]:(mysql,telnet)
[//createTime]:(20190927)
[//updateTime]:(20200327)

## 问题描述
mysql看起来在公网提供服务  
``` bash
$ netstat -tulpn | grep mysqld
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      4381/mysqld
```

`telnet 127.0.0.1 3306`可以通  

但是`telnet 公网ip 3306` 报connection refused   

## 解决办法
**修改bind-address** 

```
vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

将`bind-address`改为0.0.0.0  

**重启mysql**

``` bash
systemctl restart mysql
```