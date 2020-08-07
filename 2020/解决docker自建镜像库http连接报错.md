[//title]: (解决docker自建镜像库http连接报错问题)
[//englishtitle]: (docker-server-gave-HTTP-response-to-HTTPS-client)
[//category]: (docker)
[//tags]: (docker)
[//createtime]: (20200807)
[//updatetime]: (20200807)

## 概述

自建的 docker 镜像库如果未使用 https 连接，通过命令行登录时会报错：

```text
Error response from daemon: Get https://192.168.10.233:8802/v2/: http: server gave HTTP response to HTTPS client
```

## 解决方案

修改 docker daemon 配置，在 json 配置文件中加上`insecure-registries`。Mac 版 docker 通过 gui 也可以配置，路径为`perferences -> docker engine`。

```json
{
  "debug": true,
  "experimental": false,
  "registry-mirrors": ["https://registry.docker-cn.com"],
  "insecure-registries": ["192.168.10.233:8803", "192.168.10.233:8802"]
}
```

重启 docker 并登录即可

```bash
docker login -u [user] -p [password] [host]:[port]
```
