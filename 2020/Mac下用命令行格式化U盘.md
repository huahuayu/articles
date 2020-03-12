[//title]:(Mac下用命令行格式化U盘)
[//englishTitle]:(format-udisk-on-mac)
[//category]:(mac)
[//tags]:(mac,udisk,diskutil)
[//createTime]:(2020-03-12)
[//lastUpdateTime]:(2020-03-12)
## 概述
Mac下格式化U盘可以用图形界面做，也可以用命令行做  
![](https://cdn.liushiming.cn/img/20200312105227.png)  

某些情况下图形界面不好使，那就只能用命令行了，而且命令行更强大  

本文介绍一下，在Mac下如何用命令行格式化U盘  

## 查看u盘挂载位置  
使用`diskutil list`命令查看磁盘列表  
``` bash
$ diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     314.6 MB   disk0s1
   2:                 Apple_APFS Container disk1         500.0 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +500.0 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD - Data     374.8 GB   disk1s1
   2:                APFS Volume Preboot                 80.9 MB    disk1s2
   3:                APFS Volume Recovery                526.6 MB   disk1s3
   4:                APFS Volume VM                      5.4 GB     disk1s4
   5:                APFS Volume Macintosh HD            11.0 GB    disk1s5

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *8.0 GB     disk2
   1:                 DOS_FAT_32 EDISON                  8.0 GB     disk2s1
```

可以看出，`/dev/disk2`就是u盘，因为它是external的，而且大小和我插入U盘大小对应（具体到自己机器要看清楚）  


## 格式化u盘
将u盘格式化为 MS-DOS fat32格式的命令为  
``` bash
diskutil eraseDisk FAT32 DiskNameGoesHere /dev/DiskNodeIDHere
```

具体到我的u盘，挂载点是/dev/disk2, 我给u盘命名为UDISK，则格式化命令为  
``` bash
diskutil eraseDisk FAT32 UDISK /dev/disk2
```

u盘的命名一定要**全部大写** ，否则会报错  
``` bash
diskutil eraseDisk FAT32 udisk /dev/disk2
udisk does not appear to be a valid volume name for its file system
```

格式化成功  
``` bash
$ diskutil eraseDisk FAT32 UDISK /dev/disk2
Started erase on disk2
Unmounting disk
Creating the partition map
Waiting for partitions to activate
Formatting disk2s2 as MS-DOS (FAT32) with name UDISK
512 bytes per physical sector
/dev/rdisk2s2: 15191032 sectors in 1898879 FAT32 clusters (4096 bytes/cluster)
bps=512 spc=8 res=32 nft=2 mid=0xf8 spt=32 hds=255 hid=411648 drv=0x80 bsec=15220736 bspf=14836 rdcl=2 infs=1 bkbs=6
Mounting disk
Finished erase on disk2
```

再次用`diskutil list`命令查看磁盘列表   
``` bash
$ diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     314.6 MB   disk0s1
   2:                 Apple_APFS Container disk1         500.0 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +500.0 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD - Data     374.8 GB   disk1s1
   2:                APFS Volume Preboot                 80.9 MB    disk1s2
   3:                APFS Volume Recovery                526.6 MB   disk1s3
   4:                APFS Volume VM                      6.4 GB     disk1s4
   5:                APFS Volume Macintosh HD            11.0 GB    disk1s5

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *8.0 GB     disk2
   1:                        EFI EFI                     209.7 MB   disk2s1
   2:       Microsoft Basic Data UDISK                   7.8 GB     disk2s2
```

如果要格式化为别的文件格式，请看参考资料  

## 参考资料
[How to Erase a Disk from Command Line in Mac OS X](https://osxdaily.com/2016/08/30/erase-disk-command-line-mac/)