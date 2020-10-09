[//title]: (实测:使用cdn对搜索引擎不友好)
[//englishtitle]: (cdn-impacts-on-search-engine)
[//category]: (seo)
[//tags]: (cdn,阿里云,dnspod,seo,百度收录)
[//createtime]: (20201009)
[//updatetime]: (20201009)

## cdn 原理

下图是简单的 cdn 原理说明。网站使用了 cdn 后，用户访问的大部分数据都是从 cdn 上取的，cdn 服务器没有的内容才去源站取，然后再缓存到 cdn 中。

![](https://cdn.liushiming.cn/img/20201009104832.png)

## 配置 cdn

**step1:**

在cdn服务商那边配置，一般都会有一个控制台：

![](https://cdn.liushiming.cn/img/20201009113351.png)

**step2:**

配置 cdn 需要在 dns 中添加 cname 记录，记录值为 cdn 服务商 提供的域名，以下为举例：

![](https://cdn.liushiming.cn/img/20201009110048.png)

## cdn 对搜索引擎的影响

使用 cdn 据我观察应该是对搜索引擎不友好，会给搜索引擎造成一种网站 ip 地址不断变化的假象，因为搜索引擎爬虫访问到的是各地的 cdn 服务器，有 N 个 ip 都对应着同一个域名。除非做线路分组，判断如果是搜索引擎爬虫，直接访问源站。

![](https://cdn.liushiming.cn/img/20201009110906.png)

但是我用的 dnspod 免费版是不支持线路分组的，所以我也没配过。

我的博客 liushiming.cn 从一开始就配置了阿里云 cdn，然后百度一直不收录，持续了半年，我跟百度提了 N 次反馈没什么用，但是当我取消 cdn 的时候，收录量快速上升，很快就收录了所有页面。

![](https://cdn.liushiming.cn/img/20201009104532.png)

另外提一句，使用阿里云 cdn 期间，google 正常收录，google 的算法还是牛。
