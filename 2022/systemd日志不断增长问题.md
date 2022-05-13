[//title]: (logrotate解决systemd日志不断增长问题)
[//englishtitle]: (logrotate-resolve-systemd-journal-keeps-growing)
[//category]: (linux,systemd,utility,tools)
[//tags]: (linux,systemd,log,utility,logrotate)
[//createtime]: (20220513)
[//updatetime]: (20220513)

## 问题

通过监控发现服务器的系统盘占用越来越大，就快没有空间了，通过[寻找大文件大目录](https://liushiming.cn/article/find-large-dir-and-file.html)发现是`/var/log/messages` 文件占用的多。`tail -f` 查看了一下还在不断的打印，这是某个业务系统的日志，因为使用 systemd 管理，所以打印在这里。

可以看到现在日志是这样一个状态

```text
messages
messages-20220417
messages-20220424
messages-20220501
messages-20220508
```

不带后缀的 `messages` 是最新的日志，带日期的是滚动存储的日志，每周一个。

## 问题解决

### 方案一

纯手动。

`sudo -c "cat /dev/null > /var/log/messages"` 把日志清了。以后定时关注磁盘的占用情况，不够用了再来删。

### 方案二

定时删。

使用 cron job 定时执行 `cat /dev/null > /var/log/messages` 清理。但是直觉认为太暴力了，应该有更好的方式。

### 方案三

正确的姿势。

稍微 google 了一下，这个日志文件本来就是由 `logrotate` 来管理的，所以只要改下 `logrotate` 的配置就好，改成保留 5 天的日志，滚动删除。

主配置文件在 `/etc/logrotate.conf` 提供默认配置，同时会加载 `/etc/logrotate.d` 目录中的配置，可以覆写默认配置（是不是觉得和 nginx 很像）。

`/var/log/messages` 的配置在 `/etc/logrotate.d/syslog`

```text
/var/log/cron
/var/log/maillog
/var/log/messages
/var/log/secure
/var/log/spooler
{
    daily
    rotate 5
    missingok
    sharedscripts
    postrotate
        /bin/kill -HUP `cat /var/run/syslogd.pid 2> /dev/null` 2> /dev/null || true
    endscript
}
```

这是我新加的

```text
    daily
    rotate 5
```

副作用：

这么一改，这几个日志文件的配置全部受影响

```text
/var/log/spooler
/var/log/secure
/var/log/message
/var/log/maillog
/var/log/cron
```

debug 试运行

```bash
sudo logrotate -d /etc/logrotate.conf
```

`-d` option 可以以 debug 执行 logrotate，看看是不是你想要的结果。

没有问题就可以 `-f` 强制刷新一下配置，如果不刷新配置也没关系，默认一天会刷新一次（cron job）

```bash
sudo logrotate -f /etc/logrotate.conf
```

当然你刷新单个配置也是可以的

```bash
logrotate -f /etc/logrotate.d/nginx
```

P.s. `logrotate` 是 1996 年就出现的系统管理工具，有现成的工具就不用自己造轮子了。

## Reference

[/var/log/message 归档探究](https://qingwave.github.io/var-log-message-logrotate/)
[Linux 日志切割神器 logrotate 原理介绍和配置详解](https://wsgzao.github.io/post/logrotate/)
