[//title]: (vim强制保存命令详解)
[//englishtitle]: (vim-force-save-explained)
[//category]: (vim)
[//tags]: (vim)
[//createtime]: (20200422)
[//updatetime]: (20200422)

## 概述

使用 vim 编辑文件时，如果权限不够，提示 readonly，不让你保存，会很烦人，如果这时候退出辛辛苦苦的编辑就白费了

辛运的是可以在不退出当前 vim 的情况下，使用命令`:w !sudo tee %`命令来强制保存

但是这个命令背后的原理你知道吗？

其实背后的知识还真不少

本文将把这条命令逐一拆解，让你一次性搞个明白！

## :w - 保存 buffer

`w`的作用是保存**当前文件缓存到指定的文件**，要深刻理解这句话

查看 vim 帮助`:help :w`

`w` for `w`rite, Write the whole buffer to the current file.

`:w` = `:w current_file`，单独使用时，作用为将文件 buffer 保存在当前文件中  
`:w another_file`， 将文件 buffer 保存到另一个文件中，当前文件不改变，效果就是另存为  
`:w ![cmd]`， 将文件 buffer 作为输入交给 shell 命令处理

## sudo - 使用管理员权限执行命令

`sudo`的作用是用来获取管理员权限

因为当前用户没有写权限，所以单独用`:w`是不能保存文件的

只有通过 `sudo` 提升权限，再执行`tee`命令才能将当前缓存保存到原文件名下

## tee - 管道分支

`tee`意为 T 字形的

`tee`的作用是管道分支，比如`echo "hello" | tee file.txt`，作用是将"hello"这段标准输入，保存到`file.txt`同时打印到标准输出（屏幕）中

又如`ls -l | tee file.txt | less`命令，`tee`将上一个命令的输出一方面写入`file.txt`中，另一方面继续传递给`less`命令

![](https://cdn.liushiming.cn/img/20200422180325.png)

## % - 当前文件路径寄存器

`%`是 vim 的一个 read only 寄存器，它储存着当前文件的绝对路径

可以在 vim 命令模式下这么测试一下 - 输出当前文件名

```text
:!echo %
```

退出 vim 会看到 terminal 会打印出当前文件的路径

## 强制保存后的警告

执行完这个`:w !sudo tee %`命令会出一个警告

因为正在编辑的文件已经在别处被修改了，vim 询问是否要重载新文件进来，按`L`重载

```text
W12: Warning: File "/etc/hosts" has changed and the buffer was changed in Vim as well
See ":help W12" for more info.
[O]K, (L)oad File:
```

## 参考链接

[vim reggisters](https://www.brianstorti.com/vim-registers/)
