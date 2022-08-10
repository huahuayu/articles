[//title]: (docker容器修改默认共享内存大小)
[//englishtitle]: (modify-docker-container-default-shm)
[//category]: (docker,postgres)
[//tags]: (docker,postgres)
[//createtime]: (20220810)
[//updatetime]: (20220810)

## 问题

postgres 数据库用 docker 启动，当数据量大的时候，查询 postgres 报错 shared memorey 不够，no space left on device.

![](https://cdn.liushiming.cn/img/20220810173801.png)

使用`docker exec`进入容器查看 shared memory 情况:

```
$ sudo docker exec -it postgresql bash
root@5c5ce5aff3bd:/# df -h /dev/shm
Filesystem      Size  Used Avail Use% Mounted on
shm              64M  812K   64M   2% /dev/shm
```

可以看到默认是 64M, 如果遇到数据量大的查询就会报错。

## 解决办法

方案 1: 停止容器，在容器启动的命令行上面指定 `--shm-size=2g` 来分配更多共享内存。

```bash
sudo docker run -d --name postgresql --shm-size=2g -e POSTGRES_PASSWORD=root -p 5432:5432 -v /data/postgresql:/var/lib/postgresql/data postgres
```

方案 2: 不停止容器，直接停止 docker，修改容器 docker 参数，再重启

使用`docker info` 找到 `Docker Root Dir: /var/lib/docker`

进入到该目录找到容器对应的 id 目录下，修改`hostconfig.json`文件

```bash
vim /var/lib/docker/container/{id}/hostconfig.json
```

搜索`ShmSize`，默认是 64m，然后修改成你设置的内存大小，例如 1g=1073741824
