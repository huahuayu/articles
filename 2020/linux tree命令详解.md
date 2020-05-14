[//title]: (linux-tree命令详解)
[//englishtitle]: (linux-tree-command-in-detail)
[//category]: (linux)
[//tags]: (linux,tree,打印目录结构)
[//createtime]: (20200514)
[//updatetime]: (20200514)

## 概述

`tree`命令的作用是用树状图列出目录结构

## 常用命令

指定打印文件和目录的深度最高为 "num "级（其中 1 表示当前目录）

```text
tree -L num
```

例如

```text
$ tree -L 1
.
├── app.js
├── bin
├── node_modules
├── package-lock.json
├── package.json
├── public
├── routes
└── views
```

---

仅打印目录

```text
tree -d
```

例如

```text
$ tree -d -L 1
.
├── bin
├── node_modules
├── public
├── routes
└── views
```

---

打印隐藏文件

```text
tree -a
```

---

打印树时不加缩进线，而是显示路径（使用 -N 来不转义空格和特殊字符）。

```text
tree -i -f
```

例如

```text
$ tree -i -f -L 1
.
./app.js
./bin
./node_modules
./package-lock.json
./package.json
./public
./routes
./views
```

---

以人类可读的格式打印出目录/文件的大小（单用`-s`打印 byte 大小，`-h` = human readable）

```text
tree -s -h
```

例如

```text
$ tree -s -h -L 1
.
├── [1.0K]  app.js
├── [  96]  bin
├── [2.9K]  node_modules
├── [ 26K]  package-lock.json
├── [ 295]  package.json
├── [ 160]  public
├── [ 128]  routes
└── [ 160]  views
```

---

使用通配符(glob)模式打印树状结构中的文件，并删掉不包含匹配文件的目录。

```text
tree -P '\*.txt' ---prune
```

例如

```text
$ tree -P '*.js' --prune -L 2
.
├── app.js
└── routes
    ├── index.js
    └── users.js
```

---

使用通配符(glob)模式打印出树的层次结构中的目录，并剪掉那些不是想要的目录的祖先的目录。

```text
tree -P directory_name --matchdirs --prune
```

例如

```text
$ tree -P bin --matchdirs --prune -L 3
.
├── bin
│   └── www
└── node_modules
    ├── acorn
    │   └── bin
    ├── clean-css
    │   └── bin
    ├── jade
    │   └── bin
    ├── mkdirp
    │   └── bin
    └── uglify-js
        └── bin
```

---

打印时忽略指定的目录/文件（支持模糊匹配）

```text
tree -I 'directory_name1|directory_name2'
```

例如

```text
$ tree -I 'node_modules|package*' -L 2
.
├── app.js
├── bin
│   └── www
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.jade
    ├── index.jade
    └── layout.jade
```

## 参考资料

本文除示例外，翻译自`tldr tree`的帮助说明
