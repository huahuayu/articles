[//title]:(linux安装go)
[//englishTitle]:(install-go-in-linux)
[//category]:(go,linux)
[//tags]:(go,linux)
[//createTime]:(20200331)
[//updateTime]:(20200331)

## 概述
golang的安装非常简单，将二进制包拷贝到PATH下即可，主要就是要注意设置GOPATH和goproxy    

本文简述linux下安装golang的步骤  

## 下载二进制包
下载链接 https://golang.org/dl/

选对应的系统版本  

## 安装
将二进制包解压到`/usr/local`  
``` bash
tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz
```

检验安装版本  
``` bash
go version
```

## 创建go目录
``` bash
cd ~
mkdir -p go go/src go/pkg go/bin
```

## 设置GOPATH、GOBIN
在`.profile`文件中设置GOPATH等参数  
``` bash
export PATH=$PATH:/usr/local/go/bin
export GOPATH=$HOME/go
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOBIN
```

## 设置goproxy
使用goproxy代理拉取代码  
``` bash
export GO111MODULE=on
export GOPROXY=https://goproxy.cn
```