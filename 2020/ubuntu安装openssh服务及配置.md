[//title]: (ubuntu安装openssh服务及配置)
[//englishtitle]: (openssh-installation-and-configuration)
[//category]: (ssh,linux,ubuntu)
[//tags]: (linux,ssh,ubuntu)
[//createtime]: (20190422)
[//updatetime]: (20200422)

## 概述

本文描述如何安装 openssh server，以及如何配置 sshd 服务

## 安装

```bash
sudo apt-get install openssh-server
```

## 设置开机启动

```bash
sudo systemctl enable ssh
```

## 启动 sshd 服务

```bash
sudo systemctl start ssh
```

## 测试本地连接

```bash
ssh user@localhost
```

如果使用 root 登录且密码确定没敲错，但还是报`Permission denied`

```bash
root@ubuntu:~# ssh root@localhost
root@localhost's password:
Permission denied, please try again.
root@localhost's password:
Permission denied, please try again.
root@localhost's password:
Permission denied (publickey,password).
```

可能是设置中不允许 root ssh 登录，所以需要检查一下`/etc/ssh/sshd_config`把 `PermitRootLogin` 设置为 yes 再重试

```text
# Authentication:
PermitRootLogin yes
```

## 测试外网连接

通过 ifconfig 查看局域网 ip，通常是 10、172 或 192 开头的，假设说是 10.211.55.21

测试外网 ssh 连接

```bash
ssh user@10.211.55.21
```

如果本地能连通，外网不能连通，首先检查 `sshd` 的监听端口，是否是 0.0.0.0

```bash
root@ubuntu:~# netstat -lntpu | grep sshd
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      21067/sshd
tcp6       0      0 :::22                   :::*                    LISTEN      21067/sshd
```

如果不是，需要将配置文件`/etc/ssh/sshd_config`中的`ListenAddress`设置为 0.0.0.0

如果还是无法登录，再检查防火墙设置，`iptables`、`firewalld`或者阿里云防火墙设置

## 禁止密码登录(可选)

编辑`/etc/ssh/sshd_config`文件，

1. 如果是禁止 root 用户密码登录（但支持证书登录）

```bash
# Authentication:
# yes - 允许root登录 no-不允许root登录 prohibit-password-禁止root使用密码登录
PermitRootLogin prohibit-password
```

2. 如果是禁止个别用户、用户组
   在最后加上`Match`段，在里面加上

```bash
# disable ssh login by password for some user
Match User user1
    PasswordAuthentication no
    ChallengeResponseAuthentication no
```

```bash
# disable ssh login by password for some group
Match Group group1
    PasswordAuthentication no
    ChallengeResponseAuthentication no
```

3. 如果禁止所有用户密码登录，将以下参数都改为 no

```bash
PasswordAuthentication no
ChallengeResponseAuthentication no
```

重启 ssh 服务

```bash
systemctl restart ssh
```

无证书登录时会报错

```bash
$ ssh shiming@ubuntu
shiming@ubuntu: Permission denied (publickey).
```

## ssh debug

因为各种奇怪的原因连接不上 ssh 时，可以使用`ssh -v user@host.com`来进行 debug，可以看到更多的信息帮助排错
