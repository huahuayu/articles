[//title]:(centos安装最新版git)
[//englishTitle]:(centos-install-git2.x)
[//category]:(centos,git)
[//tags]:(centos,git)
[//createTime]:(20190331)
[//updateTime]:(20200331)

## 概述
centos7自带的很多软件版本都比较旧，比如git，自带的版本是1.8.3.1，而github上面git最新发布的版本是2.26了，如果想用最新的feature，可能需要安装新版。  

本文介绍在centos下如何安装最新版的git。  

## 查看自带版本
``` bash
$ git --version
git version 1.8.3.1
```

## 删除旧版
``` bash
$ yum remove git
```

## yum安装
添加ius源并安装git2.x  
``` bash
$ yum -y install  https://centos7.iuscommunity.org/ius-release.rpm
$ yum -y install  git2u-all
```

查看版本  
``` bash
$ git --version
git version 2.16.6
```

## 源码安装
安装依赖工具  
``` bash
sudo yum groupinstall "Development Tools"
sudo yum -y install wget perl-CPAN gettext-devel perl-devel  openssl-devel  zlib-devel
```

安装指定版本  
``` text
export VER="2.22.0"
wget https://github.com/git/git/archive/v${VER}.tar.gz
tar -xvf v${VER}.tar.gz
rm -f v${VER}.tar.gz
cd git-*
sudo make install
```

查看版本  
``` bash
$ git --version
git version 2.22.0
```

## 参考资料
[How to Install latest version of Git ( Git 2.x ) on CentOS 7](https://computingforgeeks.com/how-to-install-latest-version-of-git-git-2-x-on-centos-7/)