[//title]:(Mac下用命令行给U盘重新分区)
[//englishTitle]:(mac-partition-udisk)
[//category]:(mac)
[//tags]:(U盘,mac,分区,格式化)
[//createTime]:(20200312)
[//lastUpdateTime]:(20200318)
## 概述
今天用[Mac制作Linux启动U盘](#)过程中，急着出门，直接把U盘拔下来了，之后U盘不能访问也无法被格式化。  

后来只好通过命令行重新分区，挽救了我的U盘。

## 格式化U盘
一般情况下参考这篇[Mac下用命令行格式化U盘](https://liushiming.cn/2020/03/12/format-udisk-on-mac/)就可以了   

但是今天用`diskutil eraseDisk`命令都不行  

## 重新分区
既然格式化不了就只能重新分区了  

分区命令  
```
diskutil partitionDisk MountPoint [numberOfPartitions] [APM|MBR|GPT] [part1Format part1Name part1Size part2Format part2Name part2Size part3Format part3Name part3Size ...]
```

我执行的命令是  
```
diskutil partitionDisk /dev/disk2 1 MBRFormat "MS-DOS FAT32" UDISK 8GB
```

命令解释：  
`diskutil partitionDisk` : 分区命令
`/dev/disk2` : U盘挂载点  
`1` : 分一个区  
`MBRFormat` : 储存分区信息的方式为MBR(Master Boot Record, 主引导记录)，MBR形式的分区最高支持2T的磁盘。  
`MS-DOS FAT32`: 文件系统使用MS-DOS FAT32  
`UDISK` : U盘名字，要全部大写  
`8GB` : 分区大小，因为只有一个区，所以是整个U盘的大小  

重新分区之后U盘可以正常使用了，great！

## 参考资料
[Pro Terminal Commands: Using diskutil](https://www.applegazette.com/mac/pro-terminal-commands-using-diskutil/)  
[What’s the Difference Between FAT32, exFAT, and NTFS?](https://www.howtogeek.com/235596/whats-the-difference-between-fat32-exfat-and-ntfs/)  
[What’s the Difference Between GPT and MBR When Partitioning a Drive?](https://www.howtogeek.com/193669/whats-the-difference-between-gpt-and-mbr-when-partitioning-a-drive/)   