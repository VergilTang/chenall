title: "[分享]成功利用GPXE启动无盘WINDOWS 2003."
id: 37
date: 2008-12-23 16:32:23
tags: 
- GPXE
- ISCSI
- SCSI
- WIN2003
- 无盘2003
categories: 
- 个人日记
---

留个贴子纪念下,(具体实现方法网上有许多介绍,我就不再写了^_^).

使用STARWIND作为ISCSI服务端,在DHCP上设定使用GPXE启动,并设定好ROOT-PATH目录(DHCP 017选项)为iscsi:192.168.0.2::::win2003,启动成功了,

顺便制了一个专门作为系统维护专用的ISCSI磁盘,以后哪台电脑出问题要重装之类的可就方便了,我以前是在每台电脑上都安装一个维护工具箱,现在用ISCSI就不用了,

只要PXE启动选择用这个启动就会在客户端虚拟出一个磁盘来,也方便更新,,

而且用这个磁盘里面的GHO文件来安装系统可比以前用网络映射方式快多了,