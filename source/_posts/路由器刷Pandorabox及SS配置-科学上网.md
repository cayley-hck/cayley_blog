---
title: 路由器刷Pandorabox及SS配置-科学上网
date: 2016-07-03 14:23:40
tags: 科学上网
---

最近在JD6.18中便宜获得一台优酷路由宝,使用几天后发现上传赚金币是个很坑的玩意,所以果断刷机,折腾了一天,最后弄好了,在这里记录一下.

1.刷机

1)先找到能开telnet的路由器固件
刚到手的路由宝系统版本是1.5的,可以互刷其他版本,但我手贱直接升级为2.1了,不能再降级.不再那么容易root了.
最后找到一个可以使用telnet的固件版,哈哈,只要能进telnet一切都好办了.
下面是固件获取地址(感谢提供该固件的大神):
{% link L1_2.1.0613.8617标准版固件（已开telnet) http://bbs.yj.youku.com/forum.php?mod=viewthread&tid=45797&extra=page%3D1 %} 
<!-- more -->

2)进入路由器刷机
进入路由宝管理地址192.168.11.1 ,选择收到升级,上传下载好的,可以开telnet的固件,进行刷机工作...
大概几分钟后,就刷机成功.
发现telnet连接不上
{% codeblock %}
huangchengkaideMacBook-Air:~ kai$ telnet 192.168.11.1

Trying 192.168.11.1...

Connected to youku-router.

Escape character is '^]'.

 === IMPORTANT ============================

  Use 'passwd' to set your login password

  this will disable telnet and enable SSH

 ------------------------------------------

 {% endcodeblock %}

这里坑了我一会,最后通过google和度娘,发现路由宝在有密码的时候telnet是不打开的,所以...
...当然是恢复出厂设置啦.....

恢复出厂设置后,正常telnet连接上,赞一个
{% codeblock %}
huangchengkaideMacBook-Air:~ kai$ telnet 192.168.11.1

BusyBox v1.22.1 (2016-05-31 09:37:31 CST) built-in shell (ash)

Enter 'help' for a list of built-in commands.



  _______                     ________        __              

 |       |.-----.-----.-----.|  |  |  |.----.|  |_      

 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|        

 |_______||   __|_____|__|__||________||__|  |____|       

          |__| W I R E L E S S   F R E E D O M  



 -----------------------------------------------------

 BARRIER BREAKER (Barrier Breaker, unknown)

 -----------------------------------------------------

  * 1/2 oz Galliano         Pour all ingredients into

  * 4 oz cold Coffee        an irish coffee mug filled

  * 1 1/2 oz Dark Rum       with crushed ice. Stir.

  * 2 tsp. Creme de Cacao

 -----------------------------------------------------

 customized by youku, 

 copyright (c) youku, 2015. all rights reserved.

{% endcodeblock %}

3)先刷个breed

其实刷机都是刷firmware分区的内容,可以通过命令:

mtd -r write xxxx固件版本.bin  firmware所在分区名称

但为了安全起见和以后的各种刷机操作,建议先刷个breed.

breed是一种用于嵌入式系统中的Bootloader,它可以用来恢复路由器的固件，可以说只要刷了这玩意，路由器基本上刷不死了。

那么问题来了,什么是Bootloader 

Bootloader意思为引导加载器，即为用于加载操作系统的程序。它是一大类此类功能程序的统称。现在的 BIOS、UEFI、GRUB、RedBoot、U-Boot、CFE、Breed 等都是 Bootloader

当然有需要也可以刷其它的Bootloader,但我偏好于breed.也可以通过breed刷进入其它的bootloader哦

所谓的刷不死,指的是所有固件更新操作均在 Breed 里面完成。因为有些官方升级固件自带 Bootloader，如果从官方固件的 Web 进行升级，那么会导致 Breed 被覆盖。
所以Breed 在刷入固件时会自动去掉固件自带的 Bootloader，因此能够保证 Breed 本身是“不死”的.

下载breed地址:{% link 进入改地址找到适合路由器版本的bredd http://breed.hackpascal.net %}

下面进入正题:
{% codeblock %}
#先看下Bootloader在那个分区,
[root@Youku-router]cat /proc/mtd 
dev:    size   erasesize  name
mtd0: 02000000 00010000 "ALL"
mtd1: 00030000 00010000 "Bootloader"
mtd2: 00010000 00010000 "Config"
mtd3: 00010000 00010000 "Factory"
mtd4: 00fb0000 00010000 "firmware"
mtd5: 00e2ad1d 00010000 "rootfs"
mtd6: 00730000 00010000 "rootfs_data"

#然后将breed写入到mtd1分区,写入成功后会自动重启路由器

[root@Youku-router]mtd -r write /tmp/breed-mt7620-youku-yk1.bin  mtd1
Unlocking mtd1 ...

Writing from /tmp/breed-mt7620-youku-yk1.bin to mtd1 ...     
Rebooting ...
Connection closed by foreign host.

{% endcodeblock %}

注意:如果执行mtd -r write /tmp/breed-mt7620-youku-yk1.bin  mtd1 命令报错,说mtd1 不能够打开的话,那就是路由器的固件版本将改分区锁定了,不能进行修改,解决方法是找个可以修改mtd1版的固件,或者直接刷firmware固件进行刷机然后再在新固件中刷breed.

4)刷入Pandorabox

pandorabox是一种开放路由器平台，经lintel修改后命名潘多拉openwrt，目前支持MT7620N平台如意云路由器，极路由等等.

pandorabox有各种丰富的插件,很自由和开放

下载地址:{% link  http://downloads.openwrt.org.cn/PandoraBox/  %}
下载适合路由器的版本

先进入breed,按住路由器reset键,先把电源,再查电源,载按住3秒左右.在浏览器中输入192.168.1.1,即可进入breed

![breed界面](/images/breed.png)

现在固件更新,选择固件,上传pandorabox的固件,进行更新.以后刷固件都是以这种方式,从而保证路由器刷不死

等几分钟后,输入192.168.1.1即可进入pandorabox系统,默认账号是root 密码:admin

pandorabox系统默认开通telnet和ssh,可以直接进入终端做一些操作

{% codeblock %}
huangchkaideAir:~ kai$ ssh root@192.168.1.1
root@192.168.1.1's password: 


BusyBox v1.22.1 (2015-06-09 10:32:22 CST) built-in shell (ash)
Enter 'help' for a list of built-in commands.


  _______________________________________________________________ 
 |    ____                 _                 ____               |
 |   |  _ \ __ _ _ __   __| | ___  _ __ __ _| __ )  _____  __   |
 |   | |_) / _` | '_ \ / _` |/ _ \| '__/ _` |  _ \ / _ \ \/ /   |
 |   |  __/ (_| | | | | (_| | (_) | | | (_| | |_) | (_) >  <    |
 |   |_|   \__,_|_| |_|\__,_|\___/|_|  \__,_|____/ \___/_/\_\   |
 |                                                              |
 |                  PandoraBox SDK Platform                     |
 |                  The Core of SmartRouter                     |
 |       Copyright 2013-2015 D-Team Technology Co.,Ltd.SZ       |
 |                http://www.pandorabox.org.cn                  |
 |______________________________________________________________|
  Base on OpenWrt BARRIER BREAKER (14.09, r1024)
[root@PandoraBox:/root]#

{% endcodeblock %}

2.配置Shadowsocks

![登陆pandorabox界面](/images/pandorabox_login.png)

进入pandorabox后,设置:
拨号: 网络->接口->WAN->修改协议PPPoE->输入宽带账号密码->保存&应用->完成
修改wifi名称: 网络->无线->修改->基本设置->修改ESSID->保存&退出->完成
修改wifi密码: 网络->无线->修改->无线安装->现在加密方式->输入密码->保存&退出->完成

下面是科学上网设置:
首先要有Shadowsocks账号密码,呵呵.

设置Shadowsocks账号密码.开启透明代理(重要).如果是国外网站我们才会走ss科学上网.
![设置Shadowsocks1](/images/pandorabox_1.png)

设置udp转发
![设置Shadowsocks2](/images/pandorabox_2.png)

开启chinaDNS解析
![开启ChinaDNS](/images/pandorabox_3.png)

网络->dhcp/dns->设置
在DNS转发那里填入127.0.0.1#1053(chinaDNS的地址和端口)
![dns设置](/images/pandorabox_4.png)
忽略解析文件,打勾(重要)
![dns设置](/images/pandorabox_5.png)

以上设置，国外IP走SS通，国内IP直连，从而实现智能梯子,科学上网

透明代理是根据/etc/chinadns_chnroute.txt这个文件来决定走ss还直连.所以这文件要经常更新

{% codeblock %}
[root@PandoraBox:/root]#crontab -e

00 3 * * * wget -O- 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | grep ipv4 | grep CN | awk -F\| '{ printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > /etc/chinadns_chnroute.txt

{% endcodeblock %}

有个更好用的方法redsocks2,一个修改版redsocks.除了redsocks本来的功能之外,redsocks2可以在不需要黑名单的情况下自动判断被封锁的IP并且使用代理服务器建立连接.

但由于redsocks2暂不支持Shadowsocks的chacha20加密方式,我的ss是chacha20加密方式,所以就没有用到

![redsocks2](/images/pandorabox_6.png)



