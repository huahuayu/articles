[//title]:(centos安装docker)
[//englishTitle]:(install-docker-on-centos-use-yum)
[//category]:(docker,tutorial)
[//tags]:(docker,install docker)
[//createTime]:(20190318)
[//updateTime]:(20200318)

## 概述
centos用命令行安装docker很简单，比ubuntu下简单    

## 安装依赖
``` bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

## 添加docker源
``` bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
 
## 安装docker
``` bash
sudo yum install docker-ce  
```

## 卸载旧版本(可选)
如果安装过程中有类似报错是因为有旧版本    
```
Transaction check error:
  file /usr/bin/docker from install of docker-ce-17.12.0.ce-1.el7.centos.x86_64 conflicts with file from package docker-common-2:1.12.6-68.gitec8512b.el7.centos.x86_64
  file /usr/bin/docker-containerd from install of docker-ce-17.12.0.ce-1.el7.centos.x86_64 conflicts with file from package docker-common-2:1.12.6-68.gitec8512b.el7.centos.x86_64
  file /usr/bin/docker-containerd-shim from install of docker-ce-17.12.0.ce-1.el7.centos.x86_64 conflicts with file from package docker-common-2:1.12.6-68.gitec8512b.el7.centos.x86_64
  file /usr/bin/dockerd from install of docker-ce-17.12.0.ce-1.el7.centos.x86_64 conflicts with file from package docker-common-2:1.12.6-68.gitec8512b.el7.centos.x86_64
```

卸载旧版本  
```
sudo yum erase docker-engine-selinux
sudo yum erase docker-common-2:1.12.6-68.gitec8512b.el7.centos.x86_64
```

然后再重试安装  
``` bash
sudo yum install docker-ce  
```

## 查看docker版本
```
docker -v
```

## 启动docker
``` bash
systemctl start docker
```

## 开机默认启动docker
``` bash
systemctl enable docker
```

## 检查docker运行状态
``` bash
systemctl status docker
```

## docker hello world
跑一个docker hello world  
```
docker run hello-world
```

## 安装特定版本docker
查询有哪些可安装版本  
``` bash
yum list docker-ce --showduplicates | sort -r
```

如果不加`--showduplicates` 则只会显示最新版本  
`sort -r` 降序排列  

安装特定版本  
``` bash
sudo yum install docker-ce-<VERSION STRING>
```