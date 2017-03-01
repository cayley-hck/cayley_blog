---
title: '配置SSL证书+搭建HTTPS'
date: 2016-02-19 11:48:06
tags: https
categories: liunx
comments: true
---
配置SSL证书+搭建HTTPS:
1.https:
超文本传输安全协议（缩写：HTTPS，全称：Hypertext Transfer Protocol Secure）是超文本传输协议和SSL/TLS的组合，用以提供加密通讯及对网络服务器身份的鉴定。HTTPS连接经常被用于万维网上的交易支付和企业信息系统中敏感信息的传输。HTTPS不应与在RFC 2660中定义的安全超文本传输协议（S-HTTP）相混。
HTTPS 目前已经是所有注重隐私和安全的网站的首选.
SSL/TLS:
理论上讲这是两个协议，后者是前者的继任者，但其实 SSL 3.0 和 TLS 1.0 的差异很小，所以两者很多时候是混为一谈的,这两个都是传输层协议，在他们的基础上可以建议应用层的协议如 FTP 和 Telnet，上面说的 HTTPS 就是建立在 SSL/TLS 基础上的 HTTP。
证书
这里的证书主要指使用公私钥对加密的证书。这里所说的证书是SSL/TLS证书 证书了。一般用于服务器加密（网页、邮件服务等），也就是部署 HTTPS 时所必须的。
这里出现的证书都是用公私钥加密的证书。这涉及到 [[非对称加密]] 技术，每张证书都由一个公钥和一个私钥组成，两个拼在一起才是一套。所以公钥是可以随便发给别人看的，而私钥一定是要保密的，如果私钥泄漏，后果很严重的哦。

信任链与 CA
CA 就是数字证书认证中心，是证书的签发机构.
信任链是证书的世界里有一套信任体系,谁都可以做证书的，只是做出来的证书有没有人相信而已,所以有几家特别靠谱的CA都被浏览器愿意信任,把它们的根证书都列为信任了.所以说能获取得到这些Ca签署的证书也会被主流浏览器所信任的.


这里记录配置 SSL 证书 + 搭建 HTTPS的过程

2.获取crt证书

1)使用 OpenSSL 生成 SSL Key 和 CSR
前提条件:先安装openSSl
这里我使用yum安装:
{% codeblock %}
$ yum install -y openssl
{% endcodeblock %}
目前在 SSL证书购买时，有三种不同的资料验证方式，从而产生了三类SSL证书。

域名型SSL证书（DV SSL） ，即只对域名的所有者（一般是域名管理员邮箱，比如admin@hotmail.com）进行在线检查，具体是发送验证邮件给域名管理员或以该域名结尾的邮箱至于该域名的管理员是真实注册的单位还是另有其人，就不得而知了。

企业型 SSL 证书（OV SSL） ，是要购买者提交组织机构资料和单位授权信等在官方注册的凭证，认证机构在签发SSL证书前不仅仅要检验域名所有权，还必须对这些资料的真实合法性进行多方查验，只有通过验证的才能颁发SSL证书。

增强型 SSL 证书（EV SSL） ，与其他SSL证书一样，都是基于SSL/TLS安全协议，都是用于网站的身份验证和信息在网上的传输加密。它跟普通SSL证书的区别也是明显的，安全浏览器的地址栏变绿，如果是不受信的SSL证书则拒绝显示，如果是钓鱼网站，地址栏则会变成红色，以警示用户

EV SSL 证书获取比较严格,所以这里一般中小站长/企业,申请DV|OV比较多.

无论你用 DV 还是 OV 生成私钥，都需要填写一些基本信息，具体信息如下：

CN:
域名，也称为 Common Name，因为特殊的证书不一定是域名：xiaohei.com
O:
组织或公司名字（Organization）：cayley
OU:
部门（Department）：可以不填写，这里我们写 Web Security
L:
城市（City）：Shenzhen
ST:
省份（State / Province）：Guangdong
C:
国家（Country）：CN

加密位数:
一般加密强度：2048 位，如果你的机器性能强劲，也可以选择 4096 位

所以根据上面的信息，使用 OpenSSL 生成 key 和 csr 的命令:
{% codeblock %}
openssl req -new -newkey rsa:2048 -sha256 -nodes -out b.miaoxiaohei.com.csr -keyout b.miaoxiaohei.com.key -subj "/C=CN/ST=Guangdong/L=Shenzhen/O=cayley/OU=Web Security/CN=b.miaoxiaohei.com"  
{% endcodeblock %}

PS：如果是泛域名证书，则应该填写 *.miaoxiaohei.com

运行命令后,会在当前目录生成两个文件:
    b.miaoxiaohei.com.csr :用于跟第三方CA机构获取证书
    b.miaoxiaohei.com.key :ssl私钥,用于服务器配置

2)从第三方CA机构获取证书:

由于只有浏览器或者系统信赖的 CA 才可以让所有的访问者通畅的访问你的加密网站，而不是出现证书错误的提示。所以跳过自签证书的步骤，直接开始签署第三方可信任的 SSL 证书.

b.miaoxiaohei.com.csr 这个 CSR 文件就是你需要提交给 SSL 认证机构的，当你的域名或组织通过验证后，认证机构就会颁发给你一个 b.miaoxiaohei.com.crt

目前一般市面上针对中小站长和企业的 SSL 证书颁发机构有：

StartSSL

Comodo / 子品牌 Positive SSL

GlobalSign / 子品牌 AlphaSSL

GeoTrust / 子品牌 RapidSSL


这里我使用 {% link StartSSL https://www.startssl.com/ %} 首先进入StartSSL,注册账号,申请免费的证书(免费证书需要一个月申请一次,比较坑),土豪|企业可以直接用钱买.

这时候验证需要提供一些资料,先准备好.

申请成功后会提供,下载证书,会得到下面的几个证书文件
ApacheServer.zip
IISServer.zip
NginxServer.zip
OtherServer.zip

这时候,总共得到3个文件:
  b.miaoxiaohei.com.csr ,b.miaoxiaohei.com.key  1_b.miaoxiaohei.com_bundle.crt

同时，为了方便操作，我把这三个文件都移动到 /home/www/ssl 目录。

证书获取完成.

3.nginx 配置 HTTPS 网站以及增加安全的配置

1)配置nginx
然后可以修改 Nginx 配置文件
{% codeblock %}
$vim /usr/local/nginx/conf/vhost.d/b.miaoxiaohei.conf

server {
    listen 80;
    #listen [::]:80 ssl ipv6only=on; 
    listen 443 ssl;
    #listen [::]:443 ssl ipv6only=o
    server_name b.miaoxiaohei.com;
    root    /home/www/cayleyblogs;
    index   index.html;
    access_log  /home/www/log/cayleyblogs-access.log;
    error_log   /home/www/log/cayleyblogs-error.log;

    ssl on;
    ssl_certificate /home/www/ssl/1_b.miaoxiaohei.com_bundle.crt;
    ssl_certificate_key /home/www/ssl/b.miaoxiaohei.com.key;
}
 {% endcodeblock %}
检测配置文件没问题后,重启 Nginx 即可
{% codeblock %}
$nginx -t && nginx -s reload
{% endcodeblock %}

2)增加安全配置

但是这么做并不安全，默认是 SHA-1 形式，而现在主流的方案应该都避免 SHA-1，为了确保更强的安全性，我们可以采取迪菲－赫尔曼密钥交换

首先，进入 /home/www/ssl 目录并生成一个 dhparam.pem

{% codeblock %}
$cd  /home/www/ssl
# 如果你的机器性能足够强大，可以用 4096 位加密
$openssl dhparam -out dhparam.pem 2048   

$ openssl dhparam -out dhparam.pem 2048
Generating DH parameters, 2048 bit long safe prime, generator 2
This is going to take a long time
...........................++*++*
$ ls  #新增一个dhparam.pem文件
1_b.miaoxiaohei.com_bundle.crt  b.miaoxiaohei.com.csr 
 b.miaoxiaohei.com.key  dhparam.pem

{% endcodeblock %}
生成完毕后，在 Nginx 的 SSL 配置后面加入
{% codeblock %}
        ssl_prefer_server_ciphers on;
        ssl_dhparam /home/www/ssl/dhparam.pem;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers "EECDH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 EECDH+ECDSA+SHA256 EECDH+aRSA+SHA384 EECDH+aRSA+SHA256 EECDH+aRSA+RC4 EECDH EDH+aRSA !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS !RC4";
        keepalive_timeout 70;
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 10m; 
{% endcodeblock %}
同时，如果是全站 HTTPS 并且不考虑 HTTP 的话，可以加入 HSTS 告诉你的浏览器本网站全站加密，并且强制用 HTTPS 访问
{% codeblock %}
        add_header Strict-Transport-Security max-age=63072000;
        add_header X-Frame-Options DENY;
        add_header X-Content-Type-Options nosniff;
{% endcodeblock %}
同时也可以单独开一个 Nginx 配置，把 HTTP 的访问请求都用 301 跳转到 HTTPS
{% codeblock %}
server {  
        listen 80;
        #listen [::]:80 ipv6only=on;
        server_name     b.miaoxiaohei.com;
        return 301 https://b.miaoxiaohei.com$request_uri;
}
{% endcodeblock %}


完整配置代码如下:
{% codeblock %}

server {
    listen 80;
    #listen [::]:80 ssl ipv6only=on; 
    listen 443 ssl;
    #listen [::]:443 ssl ipv6only=o
    server_name b.miaoxiaohei.com;
    root    /home/www/cayleyblogs;
    index   index.html;
    #log info
    access_log  /home/www/log/cayleyblogs-access.log;
    error_log   /home/www/log/cayleyblogs-error.log;
    # location / {
    #    proxy_set_header   X-Real-IP $remote_addr;
    #    proxy_set_header   Host      $http_host;
    #    proxy_pass         http://0.0.0.0:4000;
    # }
    #告诉浏览器网站已启用https ,并强制使用https
    add_header Strict-Transport-Security max-age=63072000;
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;
    #开启https
    ssl on;
    ssl_certificate /home/www/ssl/1_b.miaoxiaohei.com_bundle.crt;
    ssl_certificate_key /home/www/ssl/b.miaoxiaohei.com.key;
    #增强安全配置
    ssl_prefer_server_ciphers on;
    ssl_dhparam /home/www/ssl/dhparam.pem;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers "EECDH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 EECDH+ECDSA+SHA256 EECDH+aRSA+SHA384 EECDH+aRSA+SHA256 EECDH+aRSA+RC4 EECDH EDH+aRSA !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS !RC4";
    keepalive_timeout 70;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m; 
}
#只允许https访问
server {  
        listen 80;
        #listen [::]:80 ipv6only=on;
        server_name     b.miaoxiaohei.com;
        return 301 https://b.miaoxiaohei.com$request_uri;
}
{% endcodeblock %}

4.apache配置:

申请成功后会提供,下载证书 ,解压ApacheServer.zip,会得到下面两个文件:
1_root_bundle.crt
2_b.miaoxiaohei.com.crt

另外需要下載 intermediate 和 root CA 证书
如果缺少这个两个，Firefox 會出現「sec_error_unknown_issuer」
并且出現警告该页面,告訴你不要信任 StartSSL 的 SSL 证书

下载地址如下:
http://www.startssl.com/certs/sub.class1.server.ca.pem
http://www.startssl.com/certs/ca.pem

1)首先检查Apache 有沒有安裝 mod_ssl
如果没有安装,可以通过yum install mod_ssl 來安裝 || 编译安装

2) 接下来将 b.miaoxiaohei.com.key 上到 /etc/pki/tls/private/
   将2_b.miaoxiaohei.com.crt 上传到 /etc/pki/tls/certs/
   将sub.class1.server.ca.pem 以及 ca.pem 上传到 /etc/pki/tls/

3) 解密私钥,如果沒有解密的话，每次 Apache 启动后都要加入密码
解密步骤如下：
{% codeblock %}
#cd /etc/pki/tls/private/
#openssl rsa -in b.miaoxiaohei.com.key -out b.miaoiaohei.com.key
{% endcodeblock %}
接下来会提示你输入密码

4) 在打开 /etc/httpd/conf.d/ssl.conf
将 ssl.conf 各项配置修改如下
{% codeblock %}
SSLCertificateFile /etc/pki/tls/certs/2_b.miaoxiaohei.com.crt
SSLCertificateKeyFile /etc/pki/tls/private/b.miaoxiaohei.com.key
SSLCertificateChainFile /etc/pki/tls/sub.class1.server.ca.pem
SSLCACertificateFile /etc/pki/tls/ca.pem
{% endcodeblock %}
5) 重启apache

参考:

{% link startssl http://www.startssl.com %}

{% link 申請免費 StartSSL 證書並開啟 Apache SSL 完整教學 http://blog.mowd.tw/index.php?pl=950#p_tb %}


