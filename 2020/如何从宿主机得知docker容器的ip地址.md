[//title]: (如何从宿主机得知docker容器的ip地址?)
[//englishtitle]: (How-to-get-a-Docker-container's-IP-address-from-the-host)
[//category]: (docker)
[//tags]: (docker)
[//createtime]: (20200831)
[//updatetime]: (20200831)

## 概述

docker 容器启动后如何得知容器的 ip 地址？这是一个经常遇到的一个问题。

查看容器 ip 大体有 2 种方法：

- 在容器里面用 ifconfig 查看
- 在宿主机使用`docker inspect`查看

下面分别介绍两种方法。

## 在容器内 ifconfig

在容器内使用`ifconfig`很好理解，但是有点麻烦。

步骤：

1. 首先得进入容器`docker exec -it $container /bin/bash`
2. 在容器内安装`net-tools`（才有`ifconfig`命令）
3. 使用 ifconfig 查询 ip 地址

非常不方便，不建议采用。

## 宿主机使用 docker inspect

**方法一**：

使用`docker inspect`后查看`IPAddress`的值

```bash
docker inspect $container_name | grep -i 'ipaddress'
            "SecondaryIPAddresses": null,
            "IPAddress": "",
                    "IPAddress": "172.18.0.4",
```

**方法二**：

直接查询 ip

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $container_name
```

方法二结果更直接，会直接得到 ip 地址，没有多余的输出。

## 更多资料

更多关于 docker 的知识，参考[docker-cookbook](https://liushiming.cn/article/docker-cookbook.html)
