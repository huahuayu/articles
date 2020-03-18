[//title]:(Repository base is listed more than once in the configuration解决办法)
[//englishTitle]:(Repository-base-is-listed-more-than-once-in-the-configuration-solved)
[//category]:(centos,yum,problem-solved)
[//tags]:(centos,yum)
[//createTime]:(20200318)
[//lastUpdateTime]:(20200318)

## 概述
使用`yum check-update`命令时，提示"Repository base is listed more than once in the configuration"  

``` bash
$ yum check-update
Loaded plugins: fastestmirror, langpacks
Repository base is listed more than once in the configuration
Repository updates is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository centosplus is listed more than once in the configuration
Repository epel is listed more than once in the configuration
Repository epel-debuginfo is listed more than once in the configuration
Repository epel-source is listed more than once in the configuration
Loading mirror speeds from cached hostfile
 * base: mirrors.zju.edu.cn
 * elrepo: mirrors.tuna.tsinghua.edu.cn
 * extras: mirrors.aliyun.com
 * updates: mirrors.ustc.edu.cn
```

## 原因
`/etc/yum.repos.d`目录里面的源重复了  

``` bash
$ cd /etc/yum.repos.d/ && ls
CentOS-Base-official.repo  CentOS-Debuginfo.repo  CentOS-Sources.repo                                        docker-ce.repo     epel-offical.repo
CentOS-Base.repo           CentOS-fasttrack.repo  CentOS-Vault.repo                                          elrepo.repo        epel-testing.repo
CentOS-CR.repo             CentOS-Media.repo      _copr:copr.fedorainfracloud.org:librehat:shadowsocks.repo  epel-alibaba.repo
``` 

其中CentOS-Base-official.repo和CentOS-Base.repo重复，epel-offical.repo和epel-alibaba.repo重复，因为之前我引入了alibaba的源，让下载速度更快一点，同时将官方源重命名保存，所以会有重复  

## 解决办法
将重复的源移除就好  
``` bash
$ rm CentOS-Base-official.repo epel-offical.repo
```

再执行`yum check-update`就没有提示了    
``` bash
$ yum check-update
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * elrepo: mirrors.tuna.tsinghua.edu.cn
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
```

