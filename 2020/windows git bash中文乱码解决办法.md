[//title]: (windows-git-bash中文乱码解决办法)
[//englishtitle]: (drools-example-ecommerce-pricing)
[//category]: (java,drools,tutorial)
[//tags]: (java,drools,rule-engine)
[//createtime]: (2020-10-28)
[//updatetime]: (2020-10-28)

## 问题

windows git bash 执行 `git status` 出现中文乱码情况：

```bash
$ git status
On branch main
Your branch is up to date with 'origin/main'.

  (use "git restore --staged <file>..." to unstage)
        modified:   "01-\351\241\271\347\233\256/log.md"
```

## 解决方案

运行下面几条命令：

```bash
$ git config --global i18n.commitencoding utf-8
$ git config --global i18n.logoutputencoding gbk
$ git config --global core.quotepath false
```

编辑 `Gitbash安装目录\etc\profile` 在最后添加一行：

```bash
export LESSCHARSET=utf-8
```

我不知道是哪个命令起了作用，反正问题解决了。

## 参考资料

[git status 显示中文和解决中文乱码](https://blog.csdn.net/u012145252/article/details/81775362)

[Git for windows 中文乱码解决方案](https://www.jianshu.com/p/e11cdfe1733d)
