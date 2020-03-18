[//title]:(centos-broadcom-wifi驱动安装)
[//englishTitle]:(install-broadcom-wifi-driver-on-centos)
[//category]:(centos,linux,tutorial)
[//tags]:(centos,wifi,driver,broadcom,mac)
[//createTime]:(20190318)
[//lastUpdateTime]:(20200318)

## 概述
家里有一台闲置的Mac mini，我给它装上Centos 7做服务器用，它的无线网卡型号为[broadcom BCM4331](https://www.broadcom.com/products/wireless/wireless-lan-infrastructure/bcm4331)，centos默认不带这个无线网卡驱动，需要自己安装。  

按照centos的[官方wiki](https://wiki.centos.org/HowTos/Laptops/Wireless/Broadcom)介绍，安装这个驱动非常麻烦，需要自己编译安装。而且让我哭笑不得的是，wiki上写明了，按照它的步骤去做，一定会报错的（it's almost certain you'll get an error message instead of compiled driver module），得自己手动打补丁去修改。我跟着做了几次实在是搞不定。  

后来我调查发现centos有一个源[ELRepo](https://elrepo.org/tiki/)(The Community Enterprise Linux Repository), 该源以硬件驱动和内核镜像闻名，其中包括最新版本的显卡驱动、网卡驱动、声卡驱动、摄像头驱动等。我查到里面有个[Broadcom drivers的编译安装指引](https://elrepo.org/tiki/wl-kmod)，按照指引操作顺利安装好wifi驱动。

本文是对这个编译安装指引的翻译 + 总结，看[原文](https://elrepo.org/tiki/wl-kmod)也可以。


## 查看无线网卡型号
``` bash
$ lspci | grep Network
03:00.0 Network controller: Broadcom Inc. and subsidiaries BCM4331 802.11a/b/g/n (rev 02)
```
`lspci`命令可以查看所有的PCI设备列表。  

以上可以确定我的网卡是Broadcom BCM4331。   

## 安装依赖的工具/包
## centos 6
``` bash
yum groupinstall 'Development Tools'
yum install redhat-lsb kernel-abi-whitelists
yum install kernel-devel-$(uname -r)
``` 

## centos 7
``` bash
yum group install 'Development Tools'
yum install redhat-lsb kernel-abi-whitelists
yum install kernel-devel-$(uname -r)
```

## 编译准备
切换到一个普通用户(非root)，新建构建树和.rpmmacros   
``` bash
mkdir -p ~/rpmbuild/{BUILD,RPMS,SPECS,SOURCES,SRPMS}

echo -e "%_topdir $(echo $HOME)/rpmbuild\n%dist .el$(lsb_release -s -r|cut -d"." -f1).local" >> ~/.rpmmacros
```

## 下载wl-kmod*nosrc.rpm
centos 6 http://elrepo.org/linux/elrepo/el6/SRPMS/wl-kmod-6_30_223_271-3.el6.elrepo.nosrc.rpm  

centos 7 http://elrepo.org/linux/elrepo/el7/SRPMS/wl-kmod-6_30_223_271-5.el7.elrepo.nosrc.rpm  

## 下载Broadcom驱动
从Broadcom官网下载驱动（http://www.broadcom.com/support/802.11） （根据自己的情况选择LinuxÂ® STA 32-bit (or 64-bit) drivers)  

这网站可能要科学上网  

下载到 `~/rpmbuild/SOURCES/`目录  

## 编译kmod-wl
使用普通用户构建kmod-wl  

``` 
rpmbuild --rebuild --define 'packager <your-name>' /<path-to-nosrc.rpm>/wl-kmod*nosrc.rpm
```

`<your-name>`：随便取一个名字，比如`wifi-driver`  
`<path-to-nosrc.rpm>` ：【下载wl-kmod*nosrc.rpm】步骤下载的rpm文件路径  

## 安装编译好的kmod-wl
``` bash 
sudo rpm -Uvh /path-to-rpm/kmod-wl*rpm
```

路径应该在 `/home/<your-user-name>/rpmbuild/SOURCES` 下面

## 重启
重启后可以看到有wifi了  
``` bash
reboot
``` 

## 保存编译好的驱动(可选) 
保存编译好的rpm驱动

后续如果需要可以再次安装  
``` bash
rpm -Uvh /path-to-rpm/kmod-wl*rpm
```

下面是我自己编译出来的驱动，适用于broadcom BCM4431, 或许也能适用于其他broadcom无线网卡，有需要可以试下  

[点击下载](https://cdn.liushiming.cn/kmod-wl-6_30_223_271-5.el7.local.x86_64.rpm)

## 参考资料
[centos broadcom driver install wiki](https://wiki.centos.org/HowTos/Laptops/Wireless/Broadcom)  

[elrepo wl-kmod](https://elrepo.org/tiki/wl-kmod)  

[Installing broadcom Drivers Via ELRepo - centos forums](https://forums.centos.org/viewtopic.php?t=65066)  