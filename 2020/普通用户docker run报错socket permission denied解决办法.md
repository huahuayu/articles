[//title]: (普通用户docker-run报错socket-permission-denied解决办法)
[//englishtitle]: (docker-socket-permission-denied-resovled)
[//category]: (docker)
[//tags]: (docker,socket,permission-denied)
[//createtime]: (2020-04-28)
[//updatetime]: (2020-04-28)

## 问题描述

使用普通用户运行`docker run`的时候，如果该用户不在用户组`docker`内会有如下报错

```bash
$ docker run hello-world
docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.40/containers/create: dial unix /var/run/docker.sock: connect: permission denied.
See 'docker run --help'.
```

## 解决办法

### step1: 将用户加入 docker 用户组

使用`usermod`命令将当前用户加入 docker 用户组

```bash
sudo usermod -aG docker $USER
```

**命令说明**

- `usermod` - 用来改变用户组、权限、家目录、密码之类的，和用户相关的一些设置
- `a` - a for append, 将用户添加到补充组。仅与-G 选项一起使用
- `G` - 补充用户组 （-g 为主要用户组）
- `docker` - 用户组名
- `$USER` - 当前用户名环境变量

### step2: 刷新用户组

使用`su - $USER`刷新用户组

然后使用`id`命令查看用户组，用户组中应该包含了`docker`了

### step3: 验证问题

再次运行`docker run hello-world`测试，问题应该得到解决了

## 参考资料

[What does ‘sudo usermod -a -G group \$USER’ do on Linux?](https://techoverflow.net/2019/04/30/what-does-sudo-usermod-a-g-docker-user-do-on-linux/)

[Reload a Linux user's group assignments without logging out
](https://superuser.com/questions/272061/reload-a-linux-users-group-assignments-without-logging-out)
