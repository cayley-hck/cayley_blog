---
title: PhantomJS总结
date: 2016-09-12 22:17:44
tags:
---
PhantomJS是一个无界面的,可脚本编程的WebKit浏览器引擎。它原生支持多种web标准：DOM 操作，CSS选择器，JSON，Canvas 以及SVG。
使用phantomjs可以
无UI界面的网站测试
屏幕快照(png ,jpeg,gif,pdf)
页面操作自动化
网络监控

安装:

liunx :
{% codeblock %}
#下载
[root@niaoyun49026 ~]# wget https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-2.1.1-linux-x86_64.tar.bz2
#解压
[root@niaoyun49026 ~]# tar -xvf php-7.0.9.tar.bz2 
#执行bin/phantomjs即可运行

{% endcodeblock %}

mac:

下载phantomjs-1.9.8-macosx.zip并解压，bin/phantomjs直接可用。
或者通过Homebrew安装
{% codeblock %}
brew update && brew install phantomjs huangchengkaideMacBook-Air:~ kai$ brew install phantomjs ==> Downloading https://homebrew.bintray.com/bottles/phantomjs-2.1.1.el_capitan. ######################################################################## 100.0%
==> Pouring phantomjs-2.1.1.el_capitan.bottle.tar.gz
🍺  /usr/local/Cellar/phantomjs/2.1.1: 49 files, 50.5M
{% endcodeblock %}


命令行使用:
phantomjs [options] somescript.js [arg1 [arg2 [...]]]

Options:
  --cookies-file=<val>                 设置保存cookies的文件路径 
  --config=<val>                       设置json格式配置文件的路径(这些options项以json格式保存)
  --debug=<val>                        debug模式,显示debug信息和警告级别的信息: 'true' or 'false' (default)
  --disk-cache=<val>                   是否启用磁盘缓存: 'true' or 'false' (default)
  --disk-cache-path=<val>              指定缓存文件路径 
  --ignore-ssl-errors=<val>            是否忽略 SSL证书错误 : 'true' or 'false' (default)
  --load-images=<val>                  Loads all inlined images: 'true' (default) or 'false'
  --local-url-access=<val>             是否允许使用 'file:///' URLs: 'true' (default) or 'false'
  --local-storage-path=<val>           指定本地保存 local storage的路径
  --local-storage-quota=<val>          设置最大保存local storage 的大小 (in KB)
  --offline-storage-path=<val>         指定最大保存 offline storage 的路径
  --offline-storage-quota=<val>        设置最大保存离线 offline storage 的大小(in KB)
  --local-to-remote-url-access=<val>   Allows local content to access remote URL: 'true' or 'false' (default)
  --max-disk-cache-size=<val>          Limits the size of the disk cache (in KB)
  --output-encoding=<val>              设置输出编码(cmd) default is 'utf8'
  --remote-debugger-port=<val>         Starts the script in a debug harness and listens on the specified port
  --remote-debugger-autorun=<val>      Runs the script in the debugger immediately: 'true' or 'false' (default)
  --proxy=<val>                        设置 proxy server, e.g. '--proxy=http://proxy.company.com:8080'
  --proxy-auth=<val>                   Provides authentication information for the proxy, e.g. ''-proxy-auth=username:password'
  --proxy-type=<val>                   设置代理类型, 'http' (default), 'none' (disable completely), or 'socks5'
  --script-encoding=<val>              Sets the encoding used for the starting script, default is 'utf8'
  --script-language=<val>              设置脚本语言: 'javascript'
  --web-security=<val>                 是否启用 web 安全模式, 'true' (default) or 'false'
  --ssl-protocol=<val>                 Selects a specific SSL protocol version to offer. Values (case insensitive): TLSv1.2, TLSv1.1, TLSv1.0, TLSv1 (same as v1.0), SSLv3, or ANY. Default is to offer all that Qt thinks are secure (SSLv3 and up). Not all values may be supported, depending on the system OpenSSL library.
  --ssl-ciphers=<val>                  Sets supported TLS/SSL ciphers. Argument is a colon-separated list of OpenSSL cipher names (macros like ALL, kRSA, etc. may not be used). Default matches modern browsers.
  --ssl-certificates-path=<val>        Sets the location for custom CA certificates (if none set, uses environment variable SSL_CERT_DIR. If none set too, uses system default)
  --ssl-client-certificate-file=<val>  设置本地ssl证书文件路径
 --ssl-client-key-file=<val>          设置客户端ssl私钥 --ssl-client-key-passphrase=<val>    设置客户ssl私钥密码
  --webdriver=<val>                    Starts in 'Remote WebDriver mode' (embedded GhostDriver): '[[<IP>:]<PORT>]' (default '127.0.0.1:8910') 
  --webdriver-logfile=<val>            File where to write the WebDriver's Log (default 'none') (NOTE: needs '--webdriver') 
  --webdriver-loglevel=<val>           WebDriver Logging Level: (supported: 'ERROR', 'WARN', 'INFO', 'DEBUG') (default 'INFO') (NOTE: needs '--webdriver') 
  --webdriver-selenium-grid-hub=<val>  URL to the Selenium Grid HUB: 'URL_TO_HUB' (default 'none') (NOTE: needs '--webdriver') 
  -w,--wd                              相当于 前面的'--webdriver' 选项
  -h,--help                            显示帮助文档&&退出 
  -v,--version                         输出该版本 


配置:

json格式配置文件格式如下:

{
  /* Same as: --ignore-ssl-errors=true */
  "ignoreSslErrors": true,

  /* Same as: --max-disk-cache-size=1000 */
  "maxDiskCacheSize": 1000,

  /* Same as: --output-encoding=utf8 */
  "outputEncoding": "utf8"

  /* etc. */
}

注意:配置项需要转换为驼峰命名法的格式哦

//todo ,有空在写


