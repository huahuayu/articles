[//title]: (windows下用命令行卸载软件)
[//englishtitle]: (windows-unistall-software-by-cmd)
[//category]: (windows)
[//tags]: (windows,命令行,cmd,卸载软件)
[//createtime]: (2020-11-02)
[//updatetime]: (2020-11-03)

## 概述

windows 有图形界面，一般卸载软件都会打开控制面板 -> 查看软件列表 -> 选择要卸载的软件 -> 点击卸载。

但是如果不用图形界面，命令行也可以做到。

本文介绍如何使用命令行卸载 windows 软件。

## 查看软件列表

使用以下命令查看已安装的软件：

```text
Get-WmiObject -Class Win32_Product | Select-Object -Property Name
```

示例：

```text
> Get-WmiObject -Class Win32_Product | Select-Object -Property Name

Name
----
Microsoft DCF MUI (Chinese (Simplified)) 2013
Microsoft Office Professional Plus 2013
Microsoft OneNote MUI (Chinese (Simplified)) 2013
Microsoft Office 32-bit Components 2013
Microsoft Office Shared 32-bit MUI (Chinese (Simplified)) 2013
Microsoft Office OSM MUI (Chinese (Simplified)) 2013
Microsoft Office OSM UX MUI (Chinese (Simplified)) 2013
Microsoft InfoPath MUI (Chinese (Simplified)) 2013
Microsoft Access MUI (Chinese (Simplified)) 2013
Microsoft Excel MUI (Chinese (Simplified)) 2013
Microsoft PowerPoint MUI (Chinese (Simplified)) 2013
Microsoft Publisher MUI (Chinese (Simplified)) 2013
Microsoft Outlook MUI (Chinese (Simplified)) 2013
Microsoft Groove MUI (Chinese (Simplified)) 2013
Microsoft Word MUI (Chinese (Simplified)) 2013
Microsoft Lync MUI (Chinese (Simplified)) 2013
Microsoft Office Proofing (Chinese (Simplified)) 2013
Microsoft Office Shared MUI (Chinese (Simplified)) 2013
Microsoft Office 校对工具 2013 - 简体中文
Microsoft Office Proofing Tools 2013 - English
Kaspersky Endpoint Security for Windows
Camtasia 2020
Snagit 2021
Python Launcher
Java 8 Update 271 (64-bit)
Java SE Development Kit 8 Update 271 (64-bit)
Adobe Refresh Manager
Adobe Acrobat Reader DC - Chinese Simplified
Go Programming Language amd64 go1.15.3
Microsoft Update Health Tools
```

## 将软件信息赋值给 MyApp 变量

在软件列表中找到你要卸载的软件，比如说名字叫 "app-A", 使用以下命令将 app 信息查出赋值给 `$MyApp`

```text
$MyApp = Get-WmiObject -Class Win32_Product | Where-Object{$_.Name -eq "app-A"}
```

执行完后可以查看 `$MyApp` 变量的值，会出现类似下面的结果

```text
> $MyApp
IdentifyingNumber : {7CA3EEB5-7E35-4A95-956E-947279379E85}
Name              : your_app_name
Vendor            : your_app_company
Version           : 1.2.0
Caption           : your_app_title
```

## 卸载软件

运行以下命令卸载软件

```text
$MyApp.Uninstall()
```

## 参考资料

[How To Use PowerShell To Uninstall an Application](https://redmondmag.com/articles/2019/08/27/powershell-to-uninstall-an-application.aspx)
