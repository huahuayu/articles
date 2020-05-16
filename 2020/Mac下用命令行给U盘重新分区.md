[//title]: (Mac下用命令行给U盘重新分区)
[//englishtitle]: (mac-partition-udisk)
[//category]: (mac)
[//tags]: (U盘,mac,分区,格式化)
[//createtime]: (20200312)
[//updatetime]: (20200402)

## 概述

今天用[Mac 制作 Linux 启动 U 盘](#)过程中，急着出门，直接把 U 盘拔下来了，之后 U 盘不能访问也无法被格式化。

后来只好通过命令行重新分区，挽救了我的 U 盘。

## 格式化 U 盘

一般情况下参考这篇[Mac 下用命令行格式化 U 盘](https://liushiming.cn/article/format-udisk-on-mac/)就可以了

但是今天用`diskutil eraseDisk`命令都不行

## 重新分区

既然格式化不了就只能重新分区了

分区命令

```bash
diskutil partitionDisk MountPoint [numberOfPartitions] [APM|MBR|GPT] [part1Format part1Name part1Size part2Format part2Name part2Size part3Format part3Name part3Size ...]
```

我执行的命令是

```bash
diskutil partitionDisk /dev/disk2 1 MBRFormat "MS-DOS FAT32" UDISK 8GB
```

命令解释：  
`diskutil partitionDisk` : 分区命令
`/dev/disk2` : U 盘挂载点  
`1` : 分一个区  
`MBRFormat` : 储存分区信息的方式为 MBR(Master Boot Record, 主引导记录)，MBR 形式的分区最高支持 2T 的磁盘。  
`MS-DOS FAT32`: 文件系统使用 MS-DOS FAT32  
`UDISK` : U 盘名字，要全部大写  
`8GB` : 分区大小，因为只有一个区，所以是整个 U 盘的大小

重新分区之后 U 盘可以正常使用了，great！

## 参考资料

[Pro Terminal Commands: Using diskutil](https://www.applegazette.com/mac/pro-terminal-commands-using-diskutil/)  
[What’s the Difference Between FAT32, exFAT, and NTFS?](https://www.howtogeek.com/235596/whats-the-difference-between-fat32-exfat-and-ntfs/)  
[What’s the Difference Between GPT and MBR When Partitioning a Drive?](https://www.howtogeek.com/193669/whats-the-difference-between-gpt-and-mbr-when-partitioning-a-drive/)
