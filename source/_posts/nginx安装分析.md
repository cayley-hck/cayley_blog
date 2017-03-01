---
title: nginx安装分析
date: 2016-07-06 23:18:03
tags: nginx
categories: liunx
comments: true
---

nginx的安装分析:

1.安装准备软件
1)编译工具
{% codeblock %}
	yum install -y gcc
{% endcodeblock %}

gcc可用于编译c语言.
{% codeblock %}
	yum install -y gcc-c++
{% endcodeblock %}

g++可用于编译c++

2)依赖库

{% codeblock %}
	yum install -y pcre pcre-devel
{% endcodeblock %}

PCRE是支持perl兼容正则表达式,nginx的http模块来解析正则表达式,很多地方都需要解析正则,所以要将RCRE库编译进nignx

pcre-devel是使用PCRE做二次开发时所需要的开发库,包括头文件等,是编译nginx所需要的.

{% codeblock %}
yum isntall -y zlib zlib-devel
{% endcodeblock %}

zlib库用于对http包内容做gzip格式的压缩,如果要对http响应包进行gzip压缩,减少网络传输量,就需要将zlib库编译进niginx

zlib-devel 是二次开发所需要的库

{% codeblock %}
yum install -y opensll openssl-devel
{% endcodeblock %}

SSL协议上传所需要的库,同时也是md5 sha1等散列函数所需库

......如果还需要安装nginx其他模块,则还要需要安装响应模块所依赖的库......


2.安装

解压源码文件得到文件如下:
{% codeblock %}
[root@niaoyun49026 nginx-1.11.1]# ls -al
total 676
drwxr-xr-x  8 1001 1001    147 Jul 15 14:07 .
drwxr-xr-x. 3 root root     25 Jun 28 13:58 ..
drwxr-xr-x  6 1001 1001   4096 Jun 28 13:58 auto
-rw-r--r--  1 1001 1001 264694 May 31 21:43 CHANGES
-rw-r--r--  1 1001 1001 403645 May 31 21:43 CHANGES.ru
drwxr-xr-x  2 1001 1001   4096 Jun 28 13:58 conf
-rwxr-xr-x  1 1001 1001   2481 May 31 21:43 configure
drwxr-xr-x  4 1001 1001     68 Jun 28 13:58 contrib
drwxr-xr-x  2 1001 1001     38 Jun 28 13:58 html
-rw-r--r--  1 1001 1001   1397 May 31 21:43 LICENSE
drwxr-xr-x  2 1001 1001     20 Jun 28 13:58 man
-rw-r--r--  1 1001 1001     49 May 31 21:43 README
drwxr-xr-x  9 1001 1001     84 Jun 28 13:58 src
{% endcodeblock %}

以默认的方式安装就这么简单

{% codeblock %}
./configure
make
make install
{% endcodeblock %}

3.分析

1)configure的命令做了以下工作:

检测操作内核和系统已经安装的软件和库,如果不满足编译条件做出相应的提示
编译参数的解析(还要分析参数)
中间目录的生成
根据编译参数生成一些c源码,Makefile文件等

查看configure的命令参数:
{% codeblock %}
./configure --help 
{% endcodeblock %}

configure的参数太多了,现在下命令分析:

路径相关参数:

--prefix=PATH :安装目录,默认/usr/local/nginx

--sbin-path=PATH : 可执行文件路径 ,默认<prefx>/sbin/nignx

--conf-path=PATH : 配置文件路径 ,默认<prefx>/conf/nginx.conf

--error-log-path=PATH : error日志路径 ,默认<prefix>/logs/error.log

--pid-path=PATH : pid文件路径,这个文件仅保存ngix master进程ID ,默认<prefix>/logs/nginx.pid

--lock-path=PATH : lock文件路径 ,默认<prefix>/logs/nginx.lock

--builddir=DIR : configure执行时与编译期间产生的临时文件目录,makefile,c文件,目标文件,可执行文件 ,默认<soure path>/objs

--with-perl_modul=PATH : perl module路径,只要使用第三方perl module才需要设置这个路径


--with-perl=PATH : perl binary路径,如果配置的niginx会执行perl脚本,就必须设置改路径

--http-log-path=PATH  : access log文件路径 ,默认<prefix>/logs/access.log

--http-client-body-temp-path=PATH : 设置htpp请求包体临时存放的文件路径, 默认<prefix>/client_body_tmp

--http-proxy-temp-path=PATH : 反向代理时http proxy临时文件路径,默认<prefix>/proxy_tmp

--http-fastcgi-temp-path=PATH : fastcgi临时文件路径,默认<prefix>/fastcgi_tmp

--http-uwsgicgi-temp-path=PATH : uwsgicgi临时文件路径,默认<prefix>/uwsgicgi_tmp

--http-scgi-temp-path=PATH : scgi临时文件路径,默认<prefix>/scgi_tmp

编译相关参数:

-with-cc=PATH :  设置C编译器路径
--with-cpp=PATH : 设置C预处理路径
--with-cc-opt=OPTIONS :  设置C编译器参数
--with-ld-opt=OPTIONS : 设置连接文件参数,将某个库链接到nginx中,用户--with-ld-opt=-llibaryName -LlibaryPath
--with-cpu-opt=CPU : 为指定CPU优化,可选参数有:
          pentium, pentiumpro, pentium3, pentium4,
          athlon, opteron, sparc32, sparc64, ppc64

依赖软件相关参数:
pcre库:
--with-pcre : 强制使用pcre库
--without-pcre : 不使用pcre库文件
--with-pcre=DIR : 设定PCRE库路径
--with-pcre-opt=OPTIONS  : 设置PCRE运行参数
OpenSSL库:
--with-openssl=DIR : 设定OpenSSL库文件路径
--with-openssl-opt=OPTIONS : 设置OpenSSL运行参数
atomic(原子)库:
--with-libatomic : 强制使用atomic库,该库是cpu架构独立的一种原子操作方式,支持x86,ppc64,sparc6
--with-libatomic=DIR : atomic库的路径
散列函数库:
--with-md5=DIR : 设定md5库文件路径
--with-md5-opt=OPTIONS  : 设置md5运行参数
--with-md5-asm : 使用md5源文件编译
--with-sha1=DIR : 设定sha1库文件路径
--with-sha1-opt=OPTIONS : 设置sha1运行参数
--with-sha1-asm : 使用sha1源文件编译
zlib库:
--with-zlib=DIR : 设定zlib库文件路径
--with-zlib-opt=OPTIONS : 设置zlib运行参数
--with-zlib-asm=CPU : 使zlib对特定的CPU进行优化,可选参数:
          pentium, pentiumpro

模块相关参数:
事件模块:
--with-rtsig_module : 允许rtsig模块,默认不安装
--with-select_module : 允许select模块(一种轮询模式,不推荐用在高载环境)
--without-select_module : 不使用select模块
--with-poll_module : 允许poll模块(一种轮询模式,不推荐用在高载环境)
--without-poll_module : 不使用poll模块
--with-aio_module : 使用aio模块
HTTP模块:
默认编译进的http模块:

--without-http_charset_module : 不使用ngx_http_charset_module模块  该模块可以将服务器发出的http响应重编码
--without-http_gzip_module :  不使用ngx_http_gzip_module模块,该模块根据配置文件指定的conten_type对特定的http响应包进行gzip压缩
--without-http_ssi_module :  不使用ngx_http_ssi_module模块,该模块可以在http响应包体中加入指定内容
--without-http_userid_module : 不使用ngx_http_userid_module模块,该模块可以通过http请求的头部信息里的一些字段认证用户信息,以便确认请求合法性
--without-http_access_module : 不使用ngx_http_access_module模块,该模块可以通过ip地址限制是否能访问
--without-http_auth_basic_module : 不使用ngx_http_auth_basic_module模块,该模块提供最简单的用户名密码认证
--without-http_autoindex_module : 不使用ngx_http_autoindex_module模块,该模块提供简单的目录浏览功能呢
--without-http_geo_module : 不使用ngx_http_geo_module模块,该模块可以定义一些变量,这些变量的值将与客户端ip关联,这些nginx可以通过该不同变量做出不同的处理
--without-http_map_module : 不使用ngx_http_map_module模块,该模块可以建立一个key/value的映射表,这样可以针对不同的url做特殊处理.
--without-http_referer_module : 不使用ngx_http_referer_module模块,该模块可以根据请求的referer字段来拒接请求
--without-http_rewrite_module : 不使用ngx_http_rewrite_module模块,该模块可以提供http请求在nginx内部的重定向的功能,需要依赖pcre库
--without-http_proxy_module : 不使用ngx_http_proxy_module模块,该模块提供基本http反向代理功能
--without-http_fastcgi_module : 不使用ngx_http_fastcgi_module模块,该模块提供fastcgi功能
--without-http_memcached_module : 不使用ngx_http_memcached_module模块,该模块可以使nginx直接由上游的memcached服务读取数据,并简单的适配成http响应返回给客户端
--without-http_limit_zone_module : 不使用ngx_http_limit_zone_module模块,该模块可以针对某个ip地址限制并发直连接数
--without-http_empty_gif_module : 不使用ngx_http_empty_gif_module模块,该模块可以使得nginx收到无效请求时,立刻返回内存中1*1像素的gif图片.对于无效请求不会浪费服务器资源
--without-http_browser_module : 不使用ngx_http_browser_module模块,该模块会根据http请求中的user_agent字段来识别浏览器
--without-http_upstream_ip_hash_module : 不使用ngx_http_upstream_ip_hash_module模块,该模块提供当nginx与后端server建立连接时,可以根据ip做散列运算来决定会与哪个后端服务器通讯,这样就可以实现负载均衡


默认不编译进的http模块:

--with-http_ssl_module : 允许ngx_http_ssl_module模块 ,该模块是nginx支持ssl协议,提供https服务
--with-http_realip_module 允许ngx_http_realip_module模块,该模块可以从客户端请求的header信息中获取真正的ip地址
--with-http_addition_module 允许ngx_http_addition_module模块,该模块提供在返回客户端的http包体头部或者尾部增加内容
--with-http_xslt_module 允许ngx_http_xslt_module模块,该模块提供xml格式的数据在发给客户端前加入xsl渲染,依赖libxml2库和libxslt库
--with-http_image_filter_module : 安装http_image_filter_module模块,该模块提供将符合配置的图片实时压缩为指定大小的缩列图后再发给用户,依赖libgd库
--with-http_geoip_module 安装http_geoi_module模块,该模块提供可以根据MaxMind GeoIp的IP地址数据库对客户端的ip地址得到的实际的地理位置信息,依赖MaxMind GeoIP的库文件
--with-http_sub_module 允许ngx_http_sub_module模块,该模块可以在返回给客户端的http响应包中将指定的字符串替换为自己的字符串
--with-http_dav_module 允许ngx_http_dav_module模块,该模块可以让nginx支持webdav标准(PUT,DELETED,COPY,MOVE,MKCOL等)
--with-http_flv_module 允许ngx_http_flv_module模块,该模块是的nginx可以在返回flv格式的视频文件时在header头做一些出来,是的客户端可以直接观看
--with-http_mp4_module 允许ngx_http_mp4_module模块,该模块是的nginx可以在返回mp4格式的视频文件时在header头做一些出来,是的客户端可以直接观看
--with-http_gzip_static_module 允许ngx_http_gzip_static_module模块,如果使用gizp模块对响应的文档进行gizp格式返回给客户端,那个这个文档就会每次都压缩,所以该模块的作用是压缩的文档缓存起来,如果下次请求一样的文档就直接返回文档,提高响应速度和减少对服务器资料的消耗.
--with-http_random_index_module 允许ngx_http_random_index_module模块,该模块的作用是,当客户端访问某个目录时,,随机返回该目录下任意文件.
--with-http_stub_status_module 允许ngx_http_stub_status_module模块,该模块可以让给nginx提高性能统计的页面,获得相关的并发连接,请求的信息
--with-google_perftools_module 允许ngx_google_perftools_module模块,该模块提供google的性能测试工具

邮件代理服务器相关的mail模块参数:
-with-mail  安装邮件服务器反向代理模块.是nginx可以反向代理POP3/IMAP4/SMTP等协议,默认不安装
--with-mail_ssl_module 允许ngx_mail_ssl_module模块,该模块可以使IMAP,POP3,SMTP等协议基于SSL/TLS协议上使用.默认不安装,不依赖openSSL库
--without-mail_pop3_module 不允许ngx_mail_pop3_module模块,使用--with-mail参数后,该模块默认安装
--without-mail_imap_module 不允许ngx_mail_imap_module模块,使用--with-mail参数后,该模块默认安装
--without-mail_smtp_module 不允许ngx_mail_smtp_module模块,使用--with-mail参数后,该模块默认安装


其他参数:
--with-debug : 调试日志,将nginx需要打印debug调试级别的日志的代码编译进nginx
--add-module=PATH 允许使用外部模块,以及路径,通过这个参数指定第三方模块的路径
--without-http 不使用HTTP server功能,禁用http服务器
--without-http-cache : 禁用http服务器里面的缓存cache特性
--with-file-aio : 启用文件的异步I\O功能来处理磁盘文件,需要liunx内核支持原声的异步I/O
--with-ipv6 : 使nignx支持IPv6
--user=USER 设定程序运行的用户环境(www)
--group=GROUP 设定程序运行的组环境(www)

注意:nginx编译时不是功能加的越多越好，应该尽可能少编译模块，不用的最好不要加入。

编译好的nginx可通过 /usr/local/nginx/sbin/nginx -V 查看编译时的参数


2)make命令就是根据configure生成的Makefile文件编译nginx,并生成目标文件&二进制文件


3)make install命令就是将编译好的文件和目录根据编译的设置保存到相应目录

4.configure脚本
看下该脚本分析一下:
{% codeblock %}
#!/bin/sh

# Copyright (C) Igor Sysoev
# Copyright (C) Nginx, Inc.


LC_ALL=C
export LC_ALL

#options脚本会处理传给configure的参数和根据参数定义后续所需要的变量.
. auto/options
#init脚本初始化后续将产生的文件路径
. auto/init
#sourdes脚本分析nginx的源码结构,为后续构造makefile文件准备
. auto/sources

#创建目标文件路径
test -d $NGX_OBJS || mkdir -p $NGX_OBJS
#开始准备建立nginx_auto_hearders.h,autoconf.err等必要文件
echo > $NGX_AUTO_HEADERS_H
echo > $NGX_AUTOCONF_ERR
#往objs/niginx_auto_config.h写入命令行带的参数
echo "#define NGX_CONFIGURE \"$NGX_CONFIGURE\"" > $NGX_AUTO_CONFIG_H

#如果有debug标志位,就将objs/nginx_auto_config.h文件写入debug相关的宏定义
if [ $NGX_DEBUG = YES ]; then
    have=NGX_DEBUG . auto/have
fi

#获取操作系统参数,检查编译的操作系统参数是否支持编译
if test -z "$NGX_PLATFORM"; then
    echo "checking for OS"
    #获取操作系统名称
    NGX_SYSTEM=`uname -s 2>/dev/null`
    #获取操作系统内核版本
    NGX_RELEASE=`uname -r 2>/dev/null`
    #获取操作系统是32位/还是64位的内核
    NGX_MACHINE=`uname -m 2>/dev/null`
    #输出获取的操作系统结果
    echo " + $NGX_SYSTEM $NGX_RELEASE $NGX_MACHINE"

    NGX_PLATFORM="$NGX_SYSTEM:$NGX_RELEASE:$NGX_MACHINE";

    case "$NGX_SYSTEM" in
        MINGW32_*)
            NGX_PLATFORM=win32
        ;;
    esac

else
    echo "building for $NGX_PLATFORM"
    NGX_SYSTEM=$NGX_PLATFORM
fi
#检查并设置编译器,检查gcc是否安装和支持
. auto/cc/conf
#对于非windows操作系统定义一些必要的头文件
if [ "$NGX_PLATFORM" != win32 ]; then
    . auto/headers
fi
#对于目前的操作系统环境检查检查和定义一些特定的操作系统相关的方法和变量
. auto/os/conf
#定义类unix操作系统中通用的头文件和系统调用等,同时检查当前系统环境
if [ "$NGX_PLATFORM" != win32 ]; then
    . auto/unix
fi

. auto/threads
#定义ngx_modulues数组,生产ngx_moduloes.c文件,定义nginx需要用到的模块,ngx_modules数组的先后顺序非常重要,同时如果使用了--add-module=参数加入的模块,也会在这里面
. auto/modules
#检查nginx在链接期间需要的链接的第三方静态库,动态库或者目标文件是否存在
. auto/lib/conf
#处理编译安装后的路径设置
case ".$NGX_PREFIX" in
    .)
        NGX_PREFIX=${NGX_PREFIX:-/usr/local/nginx}
        have=NGX_PREFIX value="\"$NGX_PREFIX/\"" . auto/define
    ;;

    .!)
        NGX_PREFIX=
    ;;

    *)
        have=NGX_PREFIX value="\"$NGX_PREFIX/\"" . auto/define
    ;;
esac
#处理编译安装后的配置文件路径设置
if [ ".$NGX_CONF_PREFIX" != "." ]; then
    have=NGX_CONF_PREFIX value="\"$NGX_CONF_PREFIX/\"" . auto/define
fi
#处理编译安装后的,二进制文件,pid,lock等文件的路径设置
have=NGX_SBIN_PATH value="\"$NGX_SBIN_PATH\"" . auto/define
have=NGX_CONF_PATH value="\"$NGX_CONF_PATH\"" . auto/define
have=NGX_PID_PATH value="\"$NGX_PID_PATH\"" . auto/define
have=NGX_LOCK_PATH value="\"$NGX_LOCK_PATH\"" . auto/define
have=NGX_ERROR_LOG_PATH value="\"$NGX_ERROR_LOG_PATH\"" . auto/define

have=NGX_HTTP_LOG_PATH value="\"$NGX_HTTP_LOG_PATH\"" . auto/define
have=NGX_HTTP_CLIENT_TEMP_PATH value="\"$NGX_HTTP_CLIENT_TEMP_PATH\""
. auto/define
have=NGX_HTTP_PROXY_TEMP_PATH value="\"$NGX_HTTP_PROXY_TEMP_PATH\""
. auto/define
have=NGX_HTTP_FASTCGI_TEMP_PATH value="\"$NGX_HTTP_FASTCGI_TEMP_PATH\""
. auto/define
have=NGX_HTTP_UWSGI_TEMP_PATH value="\"$NGX_HTTP_UWSGI_TEMP_PATH\""
. auto/define
have=NGX_HTTP_SCGI_TEMP_PATH value="\"$NGX_HTTP_SCGI_TEMP_PATH\""
. auto/define
#创建编译时需要的makefile文件
. auto/make
#为makefile文件加入需要的连接的第三方静态库,动态库或者目标文件
. auto/lib/make
#为makefile文件加入install功能,将编译成功的文件复制到安装路径
. auto/install

# STUB,在nginx_auto_config.h文件中加入NGX_SUPPRESS_WARN宏和 NGX_SMP宏
. auto/stubs
#在nginx_auto_config.h文件中加入NGX_USER宏和 NGX_GROUP	宏,如果没有使用--with-user||--with-group参数时默认两者都为nobody
have=NGX_USER value="\"$NGX_USER\"" . auto/define
have=NGX_GROUP value="\"$NGX_GROUP\"" . auto/define

if [ ".$NGX_BUILD" != "." ]; then
    have=NGX_BUILD value="\"$NGX_BUILD\"" . auto/define
fi
#打印configure脚本执行结果,如果有错误则返回错误提示
. auto/summary

{% endcodeblock %}


5.记录安装:

{% codeblock %}

[root@niaoyun49026 nginx-1.11.1]# yum -y gcc gcc-c++ pcre pcre-devel zlib zlib-devel opensll openssl-devel
Loaded plugins: fastestmirror
No such command: gcc. Please use /usr/bin/yum --help
[root@niaoyun49026 nginx-1.11.1]# yum  install -y gcc gcc-c++ pcre pcre-devel zlib zlib-devel opensll openssl-devel
Loaded plugins: fastestmirror
.................(大多了,省略一下)....................................
Package gcc-4.8.5-4.el7.x86_64 already installed and latest version
Package gcc-c++-4.8.5-4.el7.x86_64 already installed and latest version
Package pcre-8.32-15.el7_2.1.x86_64 already installed and latest version
Package zlib-1.2.7-15.el7.x86_64 already installed and latest version
.................(大多了,省略一下)....................................                                                                                                  
Installed:
  openssl-devel.x86_64 1:1.0.1e-51.el7_2.5                        pcre-devel.x86_64 0:8.32-15.el7_2.1                        zlib-devel.x86_64 0:1.2.7-15.el7                       

Dependency Installed:
  keyutils-libs-devel.x86_64 0:1.5.8-3.el7       krb5-devel.x86_64 0:1.13.2-12.el7_2       libcom_err-devel.x86_64 0:1.42.9-7.el7       libselinux-devel.x86_64 0:2.2.2-6.el7      
  libsepol-devel.x86_64 0:2.1.9-3.el7            libverto-devel.x86_64 0:0.2.5-4.el7      

Complete!

#基本必须库已经安装完了,下面执行configure 脚本
[root@niaoyun49026 nginx-1.11.1]# ./configure 
checking for OS
 + Linux 3.10.0-123.el7.x86_64 x86_64
checking for C compiler ... found
 + using GNU C compiler
 + gcc version: 4.8.5 20150623 (Red Hat 4.8.5-4) (GCC) 
checking for gcc -pipe switch ... found
................(大多了,省略一下)....................................                                                                                                  
creating objs/Makefile

Configuration summary
  + using system PCRE library
  + OpenSSL library is not used
  + md5: using system crypto library
  + sha1: using system crypto library
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"

#执行confgrue脚本后,多了Makefile文件和objs目录
[root@niaoyun49026 nginx-1.11.1]# ls -al
total 684
drwxr-xr-x  9 1001 1001   4096 Jul 15 15:07 .
drwxr-xr-x. 3 root root     25 Jun 28 13:58 ..
drwxr-xr-x  6 1001 1001   4096 Jun 28 13:58 auto
-rw-r--r--  1 1001 1001 264694 May 31 21:43 CHANGES
-rw-r--r--  1 1001 1001 403645 May 31 21:43 CHANGES.ru
drwxr-xr-x  2 1001 1001   4096 Jun 28 13:58 conf
-rwxr-xr-x  1 1001 1001   2481 May 31 21:43 configure
drwxr-xr-x  4 1001 1001     68 Jun 28 13:58 contrib
drwxr-xr-x  2 1001 1001     38 Jun 28 13:58 html
-rw-r--r--  1 1001 1001   1397 May 31 21:43 LICENSE
-rw-r--r--  1 root root    376 Jul 15 15:07 Makefile(这里的makefile的功能都是调用objs/Makefile的)
drwxr-xr-x  2 1001 1001     20 Jun 28 13:58 man
drwxr-xr-x  3 root root    119 Jul 15 15:07 objs
-rw-r--r--  1 1001 1001     49 May 31 21:43 README
drwxr-xr-x  9 1001 1001     84 Jun 28 13:58 src

#再看下objs目录下东西吧
[root@niaoyun49026 objs]# ls -al
total 84
drwxr-xr-x 3 root root   119 Jul 15 15:07 .
drwxr-xr-x 9 1001 1001  4096 Jul 15 15:14 ..
-rw-r--r-- 1 root root 16426 Jul 15 15:07 autoconf.err(保存执行过程生产的结果)
-rw-r--r-- 1 root root 38230 Jul 15 15:07 Makefile(编译使用,和install)
-rw-r--r-- 1 root root  6719 Jul 15 15:07 ngx_auto_config.h(宏定义)
-rw-r--r-- 1 root root   657 Jul 15 15:07 ngx_auto_headers.h(宏定义)
-rw-r--r-- 1 root root  5508 Jul 15 15:07 ngx_modules.c(记录nignx要使用的模块和顺序)
drwxr-xr-x 9 root root    84 Jul 15 15:07 src(源码和存放的目标文件)
#看下src
[root@niaoyun49026 objs]# ls -al src
total 0
drwxr-xr-x 9 root root  84 Jul 15 15:07 .
drwxr-xr-x 3 root root 119 Jul 15 15:07 ..
drwxr-xr-x 2 root root   6 Jul 15 15:07 core
drwxr-xr-x 3 root root  20 Jul 15 15:07 event
drwxr-xr-x 4 root root  29 Jul 15 15:07 http
drwxr-xr-x 2 root root   6 Jul 15 15:07 mail
drwxr-xr-x 2 root root   6 Jul 15 15:07 misc
drwxr-xr-x 4 root root  29 Jul 15 15:07 os
drwxr-xr-x 2 root root   6 Jul 15 15:07 stream


#ngx_modules.c文件记录nginx模块,就是定义ngx_module数组的,下面看下这个文件

[root@niaoyun49026 objs]# cat ngx_modules.c 
#include <ngx_config.h>
#include <ngx_core.h>
................(大多了,省略一下)....................................                                                                                                  
ngx_module_t *ngx_modules[] = {
    &ngx_core_module,
    &ngx_errlog_module,
    &ngx_conf_module,
    &ngx_regex_module,
    &ngx_events_module,
    &ngx_event_core_module,
    &ngx_epoll_module,
    &ngx_http_module,
    &ngx_http_core_module,
    &ngx_http_log_module,
    &ngx_http_upstream_module,
    &ngx_http_static_module,
    &ngx_http_autoindex_module,
    &ngx_http_index_module,
    &ngx_http_auth_basic_module,
    &ngx_http_access_module,
    &ngx_http_limit_conn_module,
    &ngx_http_limit_req_module,
    &ngx_http_geo_module,
    &ngx_http_map_module,
    &ngx_http_split_clients_module,
    &ngx_http_referer_module,
    &ngx_http_rewrite_module,
    &ngx_http_proxy_module,
    &ngx_http_fastcgi_module,
    &ngx_http_uwsgi_module,
    &ngx_http_scgi_module,
    &ngx_http_memcached_module,
    &ngx_http_empty_gif_module,
    &ngx_http_browser_module,
    &ngx_http_upstream_hash_module,
    &ngx_http_upstream_ip_hash_module,
    &ngx_http_upstream_least_conn_module,
    &ngx_http_upstream_keepalive_module,
    &ngx_http_upstream_zone_module,
    &ngx_http_write_filter_module,
    &ngx_http_header_filter_module,
    &ngx_http_chunked_filter_module,
    &ngx_http_range_header_filter_module,
    &ngx_http_gzip_filter_module,
    &ngx_http_postpone_filter_module,
    &ngx_http_ssi_filter_module,
    &ngx_http_charset_filter_module,
    &ngx_http_userid_filter_module,
    &ngx_http_headers_filter_module,
    &ngx_http_copy_filter_module,
    &ngx_http_range_body_filter_module,
    &ngx_http_not_modified_filter_module,
    NULL
};

#模块名称
char *ngx_module_names[] = {
    "ngx_core_module",
    "ngx_errlog_module",
    "ngx_conf_module",
    "ngx_regex_module",
    "ngx_events_module",
    "ngx_event_core_module",
    "ngx_epoll_module",
    "ngx_http_module",
    "ngx_http_core_module",
    "ngx_http_log_module",
    "ngx_http_upstream_module",
    "ngx_http_static_module",
    "ngx_http_autoindex_module",
    "ngx_http_index_module",
    "ngx_http_auth_basic_module",
    "ngx_http_access_module",
    "ngx_http_limit_conn_module",
    "ngx_http_limit_req_module",
    "ngx_http_geo_module",
    "ngx_http_map_module",
    "ngx_http_split_clients_module",
    "ngx_http_referer_module",
    "ngx_http_rewrite_module",
    "ngx_http_proxy_module",
    "ngx_http_fastcgi_module",
    "ngx_http_uwsgi_module",
    "ngx_http_scgi_module",
    "ngx_http_memcached_module",
    "ngx_http_empty_gif_module",
    "ngx_http_browser_module",
    "ngx_http_upstream_hash_module",
    "ngx_http_upstream_ip_hash_module",
    "ngx_http_upstream_least_conn_module",
    "ngx_http_upstream_keepalive_module",
    "ngx_http_upstream_zone_module",
    "ngx_http_write_filter_module",
    "ngx_http_header_filter_module",
    "ngx_http_chunked_filter_module",
    "ngx_http_range_header_filter_module",
    "ngx_http_gzip_filter_module",
    "ngx_http_postpone_filter_module",
    "ngx_http_ssi_filter_module",
    "ngx_http_charset_filter_module",
    "ngx_http_userid_filter_module",
    "ngx_http_headers_filter_module",
    "ngx_http_copy_filter_module",
    "ngx_http_range_body_filter_module",
    "ngx_http_not_modified_filter_module",
    NULL
};

{% endcodeblock %}







