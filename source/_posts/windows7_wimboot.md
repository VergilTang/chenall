﻿title: "【分享】成功实现WIN7的WIMBOOT功能"
id: 2015042
date: 2015-04-21 17:28
tags: 
- windows7
- WIMBOOT
- VHD
categories: 
- 系统相关
---

把WINDOWS 8的WIMBOOT功能应用到WINDOWS 7上,目前使用一切正常.

使用WIMBOOT可以节省大量的磁盘空间,特别是多系统的情况下更是明显.另外还具有`WIM`文件的特性,比如**增量备份**,如果每隔一段时间就使用增量更新一下WIM那以后就可以随时恢复到**每个更新点**的状态.

正常安装的话一个64位的系统就差不多需要40GB了,我现在两个系统最多也才40GB,是不是很心动呢!

<!--more-->

首先来秀一下最终的文件列表,很简洁.两个系统最多才占用40GB的空间.我使用用一个60G的固态硬盘.启动速度飞快.7秒左右进桌面

另外这两个系统还是打完了所有补丁再安装了所有需要的常用软件的,还包括了好几个G的VS2012,因为WIM文件相同的文件只会占用一个空间,所以我两个系统的所有的软件都装C盘.

注: 这个`40G`是指**最多**会占用空间,实际上只使用了`15G`左右,考虑到以后要安装软件还有更新什么的,64位保留15G,32位保留10G.

```
注：1. 以下只是我的系统情况演示，只供参考
    2. 2015-05-01 感觉差分VHD是不必要的，以后更新的话只要更新主VHD就行了。并且由于我的内存有8G所以重新制作了一下，增加了一个5G的内存系统(win7.ram)。

有三个系统，并且以后可以随时增加

1. win7 x86 wimboot  			==>x86\win7.vhd
2. win7 x86 wimboot(内存模式)	==>x86\win7.ram
3. win7 x64 wimboot				==>x64\win7.vhd

 BOOTMGR ==>启动文件
 wimboot.wim ==>和wimboot目录下的一样,只是为了方便更新系统
├─boot  ==>启动文件目录
│  │  memtest.exe
│  │  
│  ├─Fonts ==>启动界面字体
│  │      chs_boot.ttf
│  │      cht_boot.ttf
│  │      jpn_boot.ttf
│  │      kor_boot.ttf
│  │      wgl4_boot.ttf
│  │      
│  │      
│  └─zh-CN
│        bootmgr.exe.mui
│        memtest.exe.mui
│          
└─wimboot ==>系统文件夹
    │  wimboot.wim  ==>系统WIM WINDOWS 7 x86/x64二合一镜像 占用14G空间
    │  
    ├─x64  ==>64位系统
    │      win7.vhd  ==>64位系统主15G VHD文件  ==>使用非固实模式只占用400MB左右
    │      
    └─x86	==>32位系统
            Win7.vhd ==>32位系统主10G VHD文件  ==>使用非固实模式只占用400MB左右
            Win7.ram ==>32位系统RAM模式镜像40MB(使用grub4dos map --mem --top来启动)
```       

再来一张截图对比(整个系统有16G实际只占用了703MB的空间)

![](@@POST@@:01.png)

看了上面的简单介绍是不是很心动,也想自己改造一个呢?别急慢慢来.

### 先来介绍一下`WIMBOOT`
  
  [WIMBoot](https://msdn.microsoft.com/zh-cn/library/dn594399.aspx)是Windows image file boot的简称，是Windows 8.1 的一项新型启动方式。这种启动方式让电脑从一个高度压缩的文件启动，大大减少了磁盘空间的占有，还具有快速升级、快速恢复出厂设置等优点.
  
  * Windows 映像文件启动 (WIMBoot) 使你可以设置电脑以便它从压缩的 Windows 映像文件 (WIM file) 直接运行。
  * WIMBoot 可以大幅减少 Windows 文件使用的空间量。这有助于你为用户提供更多可用空间，并且帮助你在带有较小驱动器的电脑上部署 Windows。  

### 让Windows 7支持`WIMBOOT`方式启动

WINDOWS 7 使用WIMBOOT的方法在网上论坛上有很多介绍,不过太部份操作起来都比较复杂,经过我的改造,现在简化了一些步骤,最终只需要依次执行两个批处理就可以了.

更新了一下，现在可以直接在PE下一步到位进行安装更新操作（更新操作需要SYSTEM权限或WIN8PE），并且支持WES7 64位

1. 首先从以下地址下载我修改优化的WIMBOOT辅助工具 WIMBOOT.7Z

链接: http://pan.baidu.com/s/1mgIbMyc 密码: 83qe

2. 启动进入PE，如果原系统是VHD的，需先挂载VHD。

3. 启动wimboot.exe选择系统盘和WIM文件存放磁盘。根据提示操作。

新的功能： 
1. 支持恢复系统。  
2. 支持直接增量更新（增量更新功能请看下面的注意提示）  
3. 支持WINDOWS 7全系列包括WES7（未完全测试）  
4. 自己去发现吧。  

注： * 更新镜像功能需要格式化目标系统磁盘，所以如果WIM文件和系统同盘无法使用该功能。 
     * 增加更新功能需要使用SYSTEM权限否则会应用错误，解决方法两种：  
       1. 使用NSudo这个软件来获取SYSTEM权限，用SYSTEM权限运行WIMBOOT.EXE  
       2. 最后出现应用错误不管它，重启系统进入PE运行WIMBOOT.EXE选中刚才捕获的镜像直接应用。 

### 相关截图

1. 增量更新功能

  ![](@@POST@@:02.png)
  
2. 系统恢复功能

  ![](@@POST@@:03.png)

3. 首次使用的情况

  ![](@@POST@@:04.png)
  
### 附我上面演示系统的制作方法（仅供参考）

1. 需要准备的文件 下载: http://pan.baidu.com/s/1mgIbMyc 密码: 83qe
	* WIMBOOT.WIM  (上面有提供)  
	  这个从[三蛋win7](http://twm000.blog.163.com/blog/static/179786475201502792756980/)提取修改的,包含了32位和64位两个版本.
	* WinNTSetup   (上面有提供)
	* 一个顺手的PE  (自备)

2. 先创建wimboot目录列表,并复制wimboot.wim到wimboot目录下和根目录下(备用).
3. 进入PE用WinNTSetup分别安装两个系统到VHD中  
   * 在WinNTSetup的标题栏上右键先修改WIM API为wimlib. 
   * 注意要勾选 **Windows 映像文件启动(WIMBoot)**.
   * 使用WinNTSetup的安装到VHD功能分别安装两个系统到`x64\win7.vhd`和`x86\win7.vhd`并安装.
4. 分别安装x86和x64的系统要安装的驱动和软件.
5. 现在可以重新更新一下WIMBOOT文件.
   * 进入x86系统挂载x64的VHD文件.使用以下命令捕获x64的新系统(y:是x64系统的分区号,#是wimboot系统分区号).  
     wimlib-imagex append y:\ #:\wimboot.wim x64new --wimboot
   * 进入x64系统挂载x86的VHD文件,捕获x86的新系统同上  
     wimlib-imagex append y:\ #:\wimboot.wim x86new --wimboot
6. 进入PE把根目录下的wimboot.wim覆盖到wimboot目录下,用WinNTSetup把系统用wimboot方式安装到x86\win7.vhd和x64\win7.vhd中.
7. 搞定收工.

一些需要注意的事项:  
1. 必须使用NTFS文件系统.  
2. 为了方便更新系统WIMBOOT.WIM需要有两个,像我就是根目录一个(备用),WIMBOOT目录一个(主要).  
3. 更新系统就是使用一个系统挂载另一个系统的VHD文件用wimlib-imagex append进行更新,这个注意要更新到备用的WIMBOOT.WIM中.  
4. 重新安装必须进入PE先把备用的WIMBOOT.WIM复制到WIMBOOT目录下覆盖,然后重新用新的镜像进行安装.  
5. 个人比较懒,就不上图了,有兴趣看得懂的可以自己搞.有不明白的地方可以留言,我会抽时间回复.  

>﻿文件名称: wimboot.wim  
>文件大小: 3.71 GB (3,979,257,171 字节)  
修改时间: 2015年04月30日，23:47:38  
MD5: 1A98B453AB3F516FAC5C084D85028F42  
SHA1: DD3701475BF7F4C478A9A0A7AF485157EFB48BE7  