[//title]:(Mac下用命令行制作Linux启动U盘)
[//englishTitle]:(mac-create-bootable-linux-usb)
[//category]:(mac)
[//tags]:(usb启动盘)
[//createTime]:(20200312)
[//lastUpdateTime]:(20200313)
## 概述
家里有台闲置的Mac mini，我想给它装上linux系统做服务器用，以往我都是用软碟通(UltraISO)制作U盘启动盘。这次试下在Mac下用命令行做启动盘，本文记录整个过程。  

## 下载linux镜像
ubuntu: https://ubuntu.com/#download  
centos: https://www.centos.org/download/  
都可以  
下载iso格式的  

## 格式化u盘
参考 [Mac下用命令行格式化U盘](https://liushiming.cn/2020/03/12/format-udisk-on-mac/)  

## 将iso文件转换为img文件
命令    
``` bash
hdiutil convert -format UDRW -o /path/to/target.img /path/to/source.iso
``` 

执行命令情况  
``` bash
$ hdiutil convert -format UDRW -o ubuntu.img ubuntu.iso
Reading Driver Descriptor Map (DDM : 0)…
Reading Ubuntu 16.04.6 LTS amd64         (Apple_ISO : 1)…
Reading Apple (Apple_partition_map : 2)…
Reading Ubuntu 16.04.6 LTS amd64         (Apple_ISO : 3)…
.............................................................................................................................................................................................................
Reading EFI (Apple_HFS : 4)…
.............................................................................................................................................................................................................
Reading Ubuntu 16.04.6 LTS amd64         (Apple_ISO : 5)…
...............................................................................................................................................................................................................
Elapsed Time:  3.968s
Speed: 400.0Mbytes/sec
Savings: 0.0%
created: /Users/shiming/Downloads/ubuntu.img.dmg
```

**注意：**  
命令执行完后，`ubuntu.img`文件后面会自动加上`.dmg`后缀，要将它删掉 
``` bash
mv ubuntu.img.dmg ubuntu.img
```

## 确定U盘挂载点
执行`diskutil list`确定U盘挂载点  
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
   1:                APFS Volume Macintosh HD - Data     376.4 GB   disk1s1
   2:                APFS Volume Preboot                 80.9 MB    disk1s2
   3:                APFS Volume Recovery                526.6 MB   disk1s3
   4:                APFS Volume VM                      7.5 GB     disk1s4
   5:                APFS Volume Macintosh HD            11.0 GB    disk1s5

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *8.0 GB     disk2
   1:                        EFI EFI                     209.7 MB   disk2s1
   2:       Microsoft Basic Data UDISK                   7.8 GB     disk2s2
```
`/dev/disk2`就是，因为他是external的，且大小和我插入的U盘一致  

## 取消挂载
使用命令`diskutil unmountDisk /dev/diskN`取消挂载, diskN是上一个步骤确定的U盘  

``` bash
$ diskutil unmountDisk /dev/disk2
Unmount of all volumes on disk2 was successful
```

## 制作引导盘
使用命令`sudo dd if=/path/to/downloaded.img of=/dev/diskN bs=1m`制作引导盘 

这个命令执行时比较长，1.6G的文件，大概执行了15分钟   
``` bash
$ sudo dd if=ubuntu.img of=/dev/disk2 bs=1m
1587+1 records in
1587+1 records out
1664614400 bytes transferred in 922.708091 secs (1804053 bytes/sec)
```

## 参考资料
[How to Erase a Disk from Command Line in Mac OS X](https://osxdaily.com/2016/08/30/erase-disk-command-line-mac/)  
[How to make a bootable usb stick from an iso file on an apple mac os x](https://www.lewan.com/blog/2012/02/10/making-a-bootable-usb-stick-on-an-apple-mac-os-x-from-an-iso)