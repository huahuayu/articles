[//title]:(nginx-unknown-directive-stream解决办法)
[//englishTitle]:(unknown-directive-stream-in-nginx-conf)
[//category]:(nginx,troubleshoot,problem-solved)
[//tags]:(nginx,ngx_stream_module)
[//createTime]:(20200318)
[//updateTime]:(20200327)

## 问题描述
今天在nginx.con文件里加`stream`段落做tcp 22端口的反向代理  
```
stream {
    server {
        listen  22;
        proxy_pass i.liushiming.cn:2222;
    }
}
```

报错  
``` text
unknown directive “stream” in /etc/nginx/nginx.conf:xx
```

## 解决办法
在nginx.conf顶端插入下面这句，加载`ngx_stream_module`模块  
```
load_module /usr/lib/nginx/modules/ngx_stream_module.so;
```

测试一下，没问题了  
```
$ nginx -t
```

## 参考资料
[unknown-directive-stream-in-etc-nginx-nginx](https://serverfault.com/questions/858067/unknown-directive-stream-in-etc-nginx-nginx-conf86)  