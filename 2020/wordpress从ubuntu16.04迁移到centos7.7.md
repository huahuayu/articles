[//title]:(wordpress从ubuntu16.04迁移到centos7.7)
[//englishTitle]:(wordpress-migration-from-ubuntu-to-centos)
[//category]:(wordpress,php,nginx)
[//tags]:(wordpress,migration,fastcgi_pass,php,nginx)
[//createTime]:(20200330)
[//updateTime]:(20200402)

## 概述
最近买了个性能更好的服务器（之前用轻量级服务器），但是阿里云不支持从轻量级服务器无缝迁移到ecs，所以应用迁移需要自己折腾一番。  

新的服务器我选择的是centos镜像，而之前用的是ubuntu，迁移的过程我预计会踩点坑，不过总算搞定了。  

本文记录wordpress博客从ubuntu迁移到centos的过程。

## 迁移步骤
###  数据库迁移

- 导出wordpress旧数据  
- 导入到新的服务器mysql中  

### 文件迁移
www目录下的wordpress目录通过scp整体迁移到新服务器  

chown迁移数据的用户和用户组  

### 环境准备
版本信息  
``` text
php 7.2.29  
mysql 5.7.29    
nginx 1.16.1  
```

### 修改wordpress配置
修改`wp-config.php`配置，主要是数据库连接配置  

### nginx迁移
ubuntu下的配置`/etc/nginx/sites-enabled/wordpress`  
``` text
server {
    listen 80;
    listen [::]:80;
    server_name liushiming.cn www.liushiming.cn;
    return 301 https://$server_name$request_uri;
}

server {
	listen 443 ssl;
	server_name liushiming.cn www.liushiming.cn;

	ssl on;
	ssl_certificate      /etc/nginx/ssl/fullchain.cer;
	ssl_certificate_key  /etc/nginx/ssl/liushiming.cn.key;

	root /var/www/html/wordpress;
	index index.php index.html index.htm;

	location / {
		try_files $uri $uri/ /index.php?$args;
	}

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		include fastcgi_params;
	}
}
```

迁移到centos下的配置    
``` text
server {
    listen 80;
    listen [::]:80;
    server_name liushiming.cn www.liushiming.cn;
    return 301 https://$server_name$request_uri;
        root /var/www/html/wordpress;
}

server {
        listen 443 ssl;
        server_name liushiming.cn www.liushiming.cn;

        ssl on;
        ssl_certificate      /etc/nginx/ssl/fullchain.cer;
        ssl_certificate_key  /etc/nginx/ssl/liushiming.cn.key;

        root /var/www/html/wordpress;
        index index.php index.html index.htm;

        location / {
                try_files $uri $uri/ /index.php?$args;
        }

        location ~ \.php$ {
                include fastcgi-php.conf;
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }
}
```

### ssl证书

- 将ssl证书文件拷贝到新服务器上  
- 解决ssl证书在新服务器上自动续期的问题  

### 修改dns解析
将域名A记录解析到新的服务器  

### 参考资料
[migrating wordpress website](https://www.wpexplorer.com/migrating-wordpress-website/)  


## 故障排除
### httpcode 502
#### 问题描述
访问博客首页报错502
查看nginx错误日志`tail 100 -f /var/log/nginx/error.log`
``` text
connect() to unix:/var/run/php/php7.2-fpm.sock failed (2: No such file or directory)
```

#### 解决办法  
修改wordpress配置`/etc/nginx/conf.d/wordpress.conf`  
``` text
fastcgi_pass unix:/var/run/php/php7.2-fpm.sock
# 改为
fastcgi_pass 127.0.0.1:9000;
```

因为`/etc/php-fpm.d/www.conf`的配置为，是http服务    
``` text
listen = 127.0.0.1:9000
```

其实要fastcgi_pass使用socket也可以（而且效率更高），但是需要php-fpm配置  
``` text
listen = /var/run/php-fpm.sock
listen.owner = nginx  # 此owner要和nginx的user一样
listen.group = nginx
listen.mode = 0660
```

修改配置后重启php-fpm  
``` bash
systemctl restart php-fpm
```

#### 参考资料
[Nginx中fastcgi_pass的配置问题](https://blog.csdn.net/lanwangxia/article/details/87885687)
[PHP-FPM: Socket vs TCP/IP and sysctl tweaking](https://easyengine.io/tutorials/php/fpm-sysctl-tweaking/)  
[nginx error connect to php5 fpm sock failed](https://stackoverflow.com/questions/23443398/nginx-error-connect-to-php5-fpm-sock-failed-13-permission-denied)

### 您的PHP似乎没有安装运行WordPress所必需的MySQL扩展
#### 错误描述
访问博客首页报错`您的PHP似乎没有安装运行WordPress所必需的MySQL扩展`  

#### 解决办法  
缺少了php-mysql组件，所以需要安装  
``` bash
yum --enablerepo=remi-php72 install php-mysql 
```

重启php-fpm  
``` bash
systemctl restart php-fpm
```

### Call to undefined function simplexml_load_string()
#### 错误描述
进入wordpress后台报错`Call to undefined function simplexml_load_string()`  

#### 解决办法
缺少了php-xml组件，需要安装，预防问题，将常用模块一并安装  
``` bash
yum --enablerepo=remi-php72 install php-xml php-soap php-xmlrpc php-mbstring php-json php-gd php-mcrypt php-mysql
```

#### 参考资料
[使用某些框架出现simplexml_load_string()的原因以及解决方案](https://blog.csdn.net/df981011512/article/details/73850810)
