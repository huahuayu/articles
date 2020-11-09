## 问题

想要 docker 开机启动，ubuntu 系统没有装 systemd，根据这个对照表要安装 chkconfig，而 chkconfig 在 ubuntu 没有预装，而且在网上搜索推荐使用 sysv-rc-conf 代替。

| Systemd command                | Sysvinit command             |
| ------------------------------ | ---------------------------- |
| systemctl start service_name   | service service_name start   |
| systemctl stop service_name    | service service_name stop    |
| systemctl restart service_name | service service_name restart |
| systemctl status service_name  | service service_name status  |
| systemctl enable service_name  | chkconfig service_name on    |
| systemctl disable service_name | chkconfig service_name off   |

而用 `apt` 安装，报找不到这个包。

```bash
$ apt install sysv-rc-conf
E: Unable to locate package sysv-rc-conf
```

## 解决办法

问题的原因是 `sysv-rc-conf` 没有包含在默认源里面，需要自己添加源。

```bash
vi /etc/apt/sources.list
```

在文件最后添加

```text
deb http://archive.ubuntu.com/ubuntu/ trusty main universe restricted multiverse
```

更新安装源并安装

```
apt update && apt install sysv-rc-conf
```

链接 chkconfig

```
sudo cp /usr/sbin/sysv-rc-conf /usr/sbin/chkconfig
```

以后用哪个命令都行

```bash
$ which sysv-rc-conf
/usr/sbin/sysv-rc-conf
$ which chkconfig
/usr/sbin/chkconfig
```

## 参考资料

[ubuntu 18 当使用命令 sudo apt-get install sysv-rc-conf 安装不上时，提示“ E: 无法定位软件包问题](https://www.cnblogs.com/hester/p/12254562.html)
