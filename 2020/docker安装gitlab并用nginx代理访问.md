[//title]:(docker安装gitlab并用nginx代理访问)
[//englishTitle]:(docker-install-gitlab-proxy-by-nginx)
[//category]:(gitlab,docker,nginx,git)
[//tags]:(gitlab,docker,nginx,install)
[//createTime]:(20190325)
[//updateTime]:(20200402)

## 概述
本文记录用docker安装gitlab再使用nginx代理访问的过程  

主要内容包括：  

- docker中安装gitlab
- 安装命令解释  
- 使用nginx代理gitlab 80端口和22端口

## 环境说明 
**docker宿主机**  
centos: centos-release-7-7.1908.0.el7.centos.x86_64  
docker: 19.03.8  
gitlab image: gitlab-ce 11.10.4-ce.0
domain: i.liushiming.cn  

**nginx主机**  
ubuntu: 16.04.6
nginx: 1.16.1  
domain: git.liushiming.cn  

## 启动命令
在docker宿主机运行  
``` bash
sudo docker run --detach \
  --hostname localhost \
  --publish 4443:443 --publish 8000:80 --publish 2222:22 \
  --env GITLAB_OMNIBUS_CONFIG="external_url 'http://git.liushiming.cn'; gitlab_rails['gitlab_ssh_host'] = 'git.liushiming.cn'; gitlab_rails['gitlab_shell_ssh_port'] = 22;" \
  --name gitlab \
  --restart always \
  --volume /srv/gitlab/config:/etc/gitlab:Z \
  --volume /srv/gitlab/logs:/var/log/gitlab:Z \
  --volume /srv/gitlab/data:/var/opt/gitlab:Z \
  gitlab/gitlab-ce:11.10.4-ce.0
```

## 端口说明  
443端口：https访问  
80端口：http访问  
22端口：ssh访问，让用户保存自己的公钥在服务器中，以便用户可以通过类似`ssh://git@yourserver.yourdomain`访问gitlab项目 参考[How does gitlab make use of port 22](https://forum.gitlab.com/t/how-does-gitlab-make-use-of-port-22-ssh/4311)  

## 路径说明
| 宿主机路径         | 容器目录        | 作用         |
| ------------------ | --------------- | ------------ |
| /srv/gitlab/data   | /var/opt/gitlab | 储存应用数据 |
| /srv/gitlab/logs   | /var/log/gitlab | 储存日志数据 |
| /srv/gitlab/config | /etc/gitlab     | 储存配置文件 |

## 参数说明
external_url：定义http clone的url  
gitlab_ssh_host：定义ssh clone的url  
gitlab_shell_ssh_port：定义ssh clone的端口，默认22  

实际效果  
![](https://cdn.liushiming.cn/img/20200325114935.png)

## 配置gitlab（可选）
如果启动时不加`--env`参数，也可以在容器启动后在容器内的`/etc/gitlab/gitlab.rb`修改配置文件  

进入容器  
``` zsh
docker exec -it gitlab /bin/bash
```

在容器内部修改`gitlab.rb`配置文件 
``` zsh
vim /etc/gitlab/gitlab.rb
```

修改external_url参数(一定要带协议名http/https)  
``` text
external_url 'http://git.liushiming.cn'
```

## nginx反向代理gitlab  
特殊原因，因为docker的宿主机80端口被禁用，所以我用另一台服务器以nginx代理的方式访问gitlab  

### 代理80端口
新建`/etc/nginx/sites-enabled/gitlab`
``` text
upstream  git{
    # 域名对应 docker宿主机域名
    # 端口对应 docker宿主机映射域名
    server  i.liushiming.cn:8000;
}


server{
    listen 80;
    # 此域名是提供给最终用户的访问地址
    server_name git.liushiming.cn;

    location / {
        # 这个大小的设置非常重要，如果 git 版本库里面有大文件，设置的太小，文件push 会失败，根据情况调整
        client_max_body_size 100m;

        proxy_redirect off;
        #以下确保 gitlab中项目的 url 是域名而不是 http://git，不可缺少
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        # 反向代理到 gitlab 内置的 nginx
        proxy_pass http://git;
        index index.html index.htm;
    }
}
```

### 代理22端口
为什么要代理22端口？  

因为如果不代理22端口，则gitlab的ssh clone/push不能用  

ssh是tcp协议而非http协议，所以和80端口代理配置不同    

在`/etc/nginx/nginx.conf`插入  
``` text
stream {
     server {
         listen  22;
         proxy_pass i.liushiming.cn:2222;
     }
}
```

`nginx -t`如果报错`unknown directive “stream”`，在`nginx.conf`第一行插入下面这行，加载ngx_stream_module模块    

``` text
load_module /usr/lib/nginx/modules/ngx_stream_module.so;
```

22端口被代理转发之后，git push会报警告，`yes`继续连接即可    
``` bash
$ git push
Warning: the ECDSA host key for 'git.liushiming.cn' differs from the key for the IP address '47.107.78.234'
Offending key for IP in /Users/shiming/.ssh/known_hosts:64
Matching host key in /Users/shiming/.ssh/known_hosts:73
Are you sure you want to continue connecting (yes/no)? yes
```

将`~/.ssh/known_hosts`文件中的冲突的ip地址公钥删除  
``` bash
ssh-keygen -R 47.107.78.234
```

## 参考资料
[GitLab Docker images](https://docs.gitlab.com/omnibus/docker/)  
[gitlab config options](https://docs.gitlab.com/omnibus/settings/configuration.html)  
[docker下gitlab安装配置使用](https://www.jianshu.com/p/080a962c35b6)  