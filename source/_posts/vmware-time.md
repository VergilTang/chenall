title: "解决虚拟机系统时间不准的问题"
id: 104
date: 2009-10-26 20:56:35
tags: 
- crontab
- panabit
- Vmware
- 时间不准
categories: 
- 系统相关
- 实用文集
---

有使用虚拟机的朋友应该就会发现在虚拟机上的时间是不准确的，往往会慢上许多。对于普通应用来说当然是没有问题的，但对于一些对时间控制要求比较高一点的场合就不行了，一般情况下可以通过安装虚拟机附加工具来解决，方法网上也有许多，这里就不再介绍了。

最近安装了一个[Panabit](http://www.panabit.com/)到虚拟机(vmware server)上，用于网络行为管理控制，定义了一些规则以后，我需要在某个时间段启用某个规则，发现时间不准不好控制，在网上找了许多方法，都不太管用。像装附加工具，由于使用的是精简的BSD系统无法安装TOOLS，所以这个方法也就没有用了。

后来就找到了这个命令ntpdate命令可以同步ntp服务器上的时间。再结合系统的定时程序cron让它每隔一分钟就同步一次以保证时间的准确。

具体方法：

1.  首先需要有一个NTP服务器，如果可以上网可以使用网上提供的NTP服务器，如果没有就自己建一个
2.  修改BSD系统文件让它自动同步时间
    先在/etc/rc.conf里面添加一句cron start,用于启动cron服务。

    然后在/etc/crontab文件里面添加以下内容用于自动执行时间同步命令(把ntp.server.address换成你的NTP服务器地址）
	```
	*/1        *        *        *        *        root        ntpdate ntp.server.address
    ```
3.  重启BSD系统，过一段时间再查一下时间是不是变得准确了虽然还是会有几秒的误差，但是影响不大。

PS：不太熟悉BSD或LINUX系统，以上是结合网上找到的资料整理出来的，如果大家的更好的办法麻烦告之一下。