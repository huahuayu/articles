[//title]: (nginx反向代理请求到虚拟机)
[//englishtitle]: (mac-nginx-reverse-proxy-config)
[//category]: (mac,nginx)
[//tags]: (mac,nginx,虚拟机,反向代理,reverse-proxy)
[//createtime]: (2020-11-18)
[//updatetime]: (2020-11-18)

## 需求说明

1. 外部访问不到虚拟机的 ip，但是可以访问到主机的 ip。
2. 主机和虚拟机之间可以相互通讯。
3. 服务跑在虚拟机中。
4. 外部需要访问虚拟机中的服务。

如果要想请求到虚拟机的服务，就需要在主机中启动反向代理。

![](https://cdn.liushiming.cn/img/20201118104412.png)

## 实现方案

在主机中安装 nginx，使用 nginx 做反向代理。

**step1: 安装**

```bash
brew install nginx
```

使用`nginx -v`查看安装的版本

```bash
$ nginx -v
nginx version: nginx/1.19.4
```

**step2: 运行 nginx**

仅运行

```bash
brew services run nginx
```

运行且设置为开机启动（可选）

```bash
brew services start nginx
```

测试是否正确运行，浏览器访问`http://localhost/`

**step3: 配置反向代理**

配置目录：`/usr/local/etc/nginx`

`nginx.conf`是主配置，可以看到主配置文件中的最后一句` include servers/*;` 说明除了主配置文件，还会加载`servers`目录下的配置文件。

所以我们将配置加在`servers`目录下。

新建文件 `forwardToVM.conf`

```bash
touch forwardToVM.conf
```

配置一个最简单的反向代理

```
server {
    listen       8080;

    location / {
        proxy_pass http://10.211.55.4:8080;
    }
}
```

**step4: 重启 nginx**

先使用 `nginx -t` 验证配置文件。

```bash
$ sudo nginx -t
nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful
```

重启服务

```bash
sudo nginx -s reload
```

**step5: 测试**

使用外部 client 请求主机，看是否能正确反向代理。

## brew service 命令

附 `brew services` 命令的帮助信息

```
Usage: brew services [subcommand]

Manage background services with macOS' launchctl(1) daemon manager.

If sudo is passed, operate on /Library/LaunchDaemons (started at boot).
Otherwise, operate on ~/Library/LaunchAgents (started at login).

[sudo] brew services [list]:
    List all managed services for the current user (or root).

[sudo] brew services run (formula|--all):
    Run the service formula without registering to launch at login (or boot).

[sudo] brew services start (formula|--all):
    Start the service formula immediately and register it to launch at login
(or boot).

[sudo] brew services stop (formula|--all):
    Stop the service formula immediately and unregister it from launching at
login (or boot).

[sudo] brew services restart (formula|--all):
    Stop (if necessary) and start the service formula immediately and register
it to launch at login (or boot).

[sudo] brew services cleanup:
    Remove all unused services.

      --all                        Run subcommand on all services.
  -d, --debug                      Display any debugging information.
  -q, --quiet                      Suppress any warnings.
  -v, --verbose                    Make some output more verbose.
  -h, --help                       Show this message.
```
