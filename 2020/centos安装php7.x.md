[//title]:(centos安装php7.x)
[//englishTitle]:(centos-install-php7.x)
[//category]:(centos,php)
[//tags]:(centos,php)
[//createTime]:(20190325)
[//updateTime]:(20200402)

## 概述
本文介绍在centos安装php    

直接`yum install php`安装的php版本可能比较低，安装前可以`yum search php`看一下都有哪些版本，安装成功后`php -v`查看一下版本是否是自己想要的  

如果想安装较新版本的php可以参考下面的步骤  

## step1: 安装EPEL yum库  
``` bash
yum install epel-release
```

## step2: 安装Remi库  
``` bash
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
```

选项-Uvh的[解释](https://unix.stackexchange.com/questions/125567/yum-translation-of-rpm-uvh)   
``` bash
-i # Install, (will throw an error if already installed)
-U # Update (or install if not present), usually preferred over -i 
-v # verbose
-h # hash, basically just shows a pretty progress bar
```

## step3: 安装php  
``` bash
## Install PHP 7.4 
yum --enablerepo=remi-php74 install php php-fpm

## Install PHP 7.3 
yum --enablerepo=remi-php73 install php php-fpm

## Install PHP 7.2 
yum --enablerepo=remi-php72 install php php-fpm

## Install PHP 7.1 
yum --enablerepo=remi-php71 install php php-fpm
```

## step4: 安装其他模块（可选）
``` bash
## For PHP 7.4
yum --enablerepo=remi-php74 install php-xml php-soap php-xmlrpc php-mbstring php-json php-gd php-mcrypt php-mysql

## For PHP 7.3
yum --enablerepo=remi-php73 install php-xml php-soap php-xmlrpc php-mbstring php-json php-gd php-mcrypt php-mysql

## For PHP 7.2
yum --enablerepo=remi-php72 install php-xml php-soap php-xmlrpc php-mbstring php-json php-gd php-mcrypt php-mysql

## For PHP 7.1
yum --enablerepo=remi-php71 install php-xml php-soap php-xmlrpc php-mbstring php-json php-gd php-mcrypt php-mysql
```

安装完成后查看版本  
``` bash
$ php -v
PHP 7.2.29 (cli) (built: Mar 17 2020 11:36:18) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
```

## step5: 启动php-fpm
``` bash
systemctl start php-fpm
```

设置开机启动  
``` bash
systemctl enable php-fpm
```

## step6: 修改php配置（可选）
编辑配置文件  
``` bash
vim /etc/php.ini
```

修改上传文件大小  
``` text
post_max_size = 1024M
memory_limit = 1024M
upload_max_filesize = 1024M
```

重启php-fpm  
``` bash
systemctl restart php-fpm
```

## 参考资料
[install latest php version](https://www.digitalocean.com/community/questions/trying-to-install-latest-php-version-on-centos-7)  
[install latest php on centos 7](https://www.mynotepaper.com/install-latest-php-php-fpm-on-centos-7/)  