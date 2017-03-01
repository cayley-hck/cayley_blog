---
title: owncloud安装在openwrt路由器上
date: 2016-08-04 21:20:42
tags:
---
之前研究owncloud,突然想将它装在家里的路由器上.
说干就干,然后记录以下自己忙活的过程.

1.下载源码:
{% codeblock %}
[root@PandoraBox:/root]# mkdir /data/codebase
[rroot@PandoraBox:/root]# wget https://download.owncloud.org/community/owncloud-9.1.0.tar.bz2
--2016-07-27 17:21:11--  https://download.owncloud.org/community/owncloud-9.1.0.tar.bz2
Resolving download.owncloud.org (download.owncloud.org)... 188.40.127.122, 148.251.209.106, 188.40.68.177, ...
Connecting to download.owncloud.org (download.owncloud.org)|188.40.127.122|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 29055490 (28M) [application/x-bzip2]
Saving to: wncloud-9.1.0.tar.bz2
2016-07-27 17:44:55
{% endcodeblock %}

解压源码:
{% codeblock %}
#需要先安装tar
[root@PandoraBox:/root]# opkg install tar
#解压
[root@PandoraBox:/root]# tar -xvf owncloud-9.1.0.tar.bz2 -C /data/codebase/
{% endcodeblock %}

2.安装php:
{% codeblock %}
[root@PandoraBox:/root]# opkg install php5 php5-cgi php5-fastcgi php5-mod-json php5-mod-session php5-mod-zip libsqlite3 zoneinfo-core php5-mod-pdo php5-mod-pdo-sqlite php5-mod-ctype php5-mod-mbstring php5-mod-gd sqlite3-cli php5-mod-sqlite3 php5-mod-curl curl php5-mod-xml php5-mod-simplexml php5-mod-hash php5-mod-dom php5-mod-iconv

[root@PandoraBox:/root]# opkg install php5-mod-mcrypt php5-mod-openssl php5-mod-fileinfo php5-mod-exif

[root@PandoraBox:/root]#opkg install php5-mod-xmlreader php5-mod-xmlwriter

{% endcodeblock %}

那些梗:

安装完成后,运行发先出错了,错误如下:
{% codeblock %}
[root@PandoraBox:/root]#php-cli 
PHP Warning:  PHP Startup: Unable to load dynamic library '/usr/lib/php/gd.so' - File not found in Unknown on line 0
{% endcodeblock %}
查资料后,发现可能是lib库的问题,重装下:
{% codeblock %}
[root@PandoraBox:/root]#opkg install php5-mod-gd libjpeg libpng libgd  --force-reinstal
{% endcodeblock %}

然后报错:
{% codeblock %}
[root@PandoraBox:/root]#opkg install php5-mod-gd libjpeg libpng libgd  --force-r
einstall
Removing package php5-mod-gd from root...
Removing package libpng from root...
Installing php5-mod-gd (5.4.27-1) to root...
Downloading http://downloads.openwrt.org.cn/PandoraBox/ralink/packages/oldpackages/php5-mod-gd_5.4.27-1_ralink.ipk.
Installing libjpeg (9a-1) to root...
Installing libjpeg (9a-1) to root...
Installing libpng (1.2.51-1) to root...
Downloading http://downloads.openwrt.org.cn/PandoraBox/ralink/packages/packages/libpng_1.2.51-1_ralink.ipk.
Installing libgd (2.1.0-1) to root...
Downloading http://downloads.openwrt.org.cn/PandoraBox/ralink/packages/packages/libgd_2.1.0-1_ralink.ipk.
Installing libjpeg (9a-1) to root...
Configuring libpng.
Collected errors:
 * opkg_download_pkg: Package libjpeg is not available from any configured src.
 * opkg_install_pkg: Failed to download libjpeg. Perhaps you need to run 'opkg update'?
 #所以严重怀疑是libjepg库,没装好
 * opkg_install_cmd: Cannot install package php5-mod-gd.
 * opkg_download_pkg: Package libjpeg is not available from any configured src.
 * opkg_install_pkg: Failed to download libjpeg. Perhaps you need to run 'opkg update'?
 * opkg_install_cmd: Cannot install package libjpeg.
 * opkg_download_pkg: Package libjpeg is not available from any configured src.
 * opkg_install_pkg: Failed to download libjpeg. Perhaps you need to run 'opkg update'?
 * opkg_install_cmd: Cannot install package libgd.

#怀疑libjepg库,没装好,所以单独装一下:
[root@PandoraBox:/root]#opkg install  libjpeg
Installing libjpeg (9a-1) to root...
Collected errors:
 * opkg_download_pkg: Package libjpeg is not available from any configured src.
 * opkg_install_pkg: Failed to download libjpeg. Perhaps you need to run 'opkg update'?
 * opkg_install_cmd: Cannot install package libjpeg.


 #所以应该这个包没法现在,看下还有那些可以替代的包
 [root@PandoraBox:/root]#opkg list libjpeg*
libjpeg - 9a-1
libjpeg - 6b-1 - The Independent JPEG Group's JPEG runtime library
#发现libjpeg - 6b-1可用,然后去官网下载这个包,安装:
[root@PandoraBox:/root]#wget http://downloads.openwrt.org.cn/PandoraBox/ralink/p
ackages/packages/libjpeg_6b-1_ralink.ipk
--2016-08-03 21:15:58--  http://downloads.openwrt.org.cn/PandoraBox/ralink/packages/packages/libjpeg_6b-1_ralink.ipk
Resolving downloads.openwrt.org.cn... 106.185.25.62
Connecting to downloads.openwrt.org.cn|106.185.25.62|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 55298 (54K) [application/octet-stream]
Saving to: 'libjpeg_6b-1_ralink.ipk'

libjpeg_6b-1_ralink 100%[=====================>]  54.00K  15.1KB/s   in 3.6s   

2016-08-03 21:16:03 (15.1 KB/s) - 'libjpeg_6b-1_ralink.ipk' saved [55298/55298]

[root@PandoraBox:/root]#opkg install libjpeg_6b-1_ralink.ipk 
Installing libjpeg (6b-1) to root...
Configuring libjpeg.

#最后再安装一下php-gd,发现问题解决了
[root@PandoraBox:/root]#opkg install php5-mod-gd 
Installing php5-mod-gd (5.4.27-1) to root...
Downloading http://downloads.openwrt.org.cn/PandoraBox/ralink/packages/oldpackages/php5-mod-gd_5.4.27-1_ralink.ipk.

Configuring php5-mod-gd.
{% endcodeblock %}

3.配置web server:

对于web server,本来想用nginx,但发现pandorabox有自带的uhttp,所有用自带的.修改相应配置文件/etc/config/uhttpd 
配置如下:
{% codeblock %}
vim /etc/config/uhttpd

#首先将原来路由器的web的端口改为其他的,我这里改为88 和4430
config uhttpd 'main'
	list listen_http '0.0.0.0:88' 
	list listen_http '[::]:88'
	list listen_https '0.0.0.0:4430'
	list listen_https '[::]:4430'
	option home '/www'
	option rfc1918_filter '1'
	option max_requests '3'
	option max_connections '100'
	option cert '/etc/uhttpd.crt'
	option key '/etc/uhttpd.key'
	option cgi_prefix '/cgi-bin'
	option script_timeout '60'
	option network_timeout '30'
	option http_keepalive '20'
	option tcp_keepalive '1'
	option ubus_prefix '/ubus'
	list index_page 'cgi-bin/luci'

config cert 'px5g'
	option days '730'
	option bits '1024'
	option country 'DE'
	option state 'Berlin'
	option location 'Berlin'
	option commonname 'OpenWrt'
#配置
config uhttpd 'owncloud'
        list listen_http '0.0.0.0:80'
        list listen_http '[::]:80'
        list listen_https '0.0.0.0:443'
        list listen_https '[::]:443'

        option home '/data/codebase/owncloud'
        option index_page  index.php, index.html, index.htm, default.html, default.htm

        option rfc1918_filter '1'
        option max_requests '3'
        option max_connections '100'
        option cert '/data/ssl/c.miaoxiaohei.com.crt'
        option key '/data/ssl/c.miaoxiaohei.com.key'
        option script_timeout '60'
        option network_timeout '30'
        option http_keepalive '20'
        option tcp_keepalive '1'
        option cgi_prefix '/cgi-bin'
        #配置php-cgi
        list interpreter  ".php=/usr/bin/php-cgi"


{% endcodeblock %}


4.打开浏览器,输入192.168.1.1 进入安装安装界面
安装之前需要创建打data目录,存放数据
{% codeblock %}
[root@PandoraBox:/root]# mkdir /data/codeblock/owncloud/data
{% endcodeblock %}

那些梗:
安装的时间,发现报错了,具体错误如下:

设置语言为 en_US.UTF-8/fr_FR.UTF-8/es_ES.UTF-8/de_DE.UTF-8/ru_RU.UTF-8/pt_BR.UTF-8/it_IT.UTF-8/ja_JP.UTF-8/zh_CN.UTF-8 失败
Please install one of these locales on your system and restart your webserver.

然后找了很久的解决方案,1)安装6以下的owncloud版本 2)设置语言 3)修改代码

然后我用修改代码的方法解决了
{% codeblock %}
[root@PandoraBox:/root]#vim /data/codebase/owncloud/lib/private/legacy/util.php 

        public static function isSetLocaleWorking() {                           
                // setlocale test is pointless on Windows                      
                if (OC_Util::runningOnWindows()) {                              
                        return true;                                            
                }                                                               
                                                                                
                \Patchwork\Utf8\Bootup::initLocale();                         
                //将这里注释掉了
                if ('' === basename('..')) {                                    
                        //return false;                                         
                }                                                               
                return true;                                                    
        } 
{% endcodeblock %}

到这里.................就已经安装完成.

安装完成后,发现运行不是很流畅,性能有问题