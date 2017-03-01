---
title: 使用ownCloud搭建云盘
date: 2016-07-27 16:31:31
tags:
---

ownCloud:
	ownCloud是一个自由且开源的个人云存储解决方案(网盘)，包括两个部分：服务器和客户端。
ownCloud在客户端可通过网页界面，或者安装专用的客户端软件来使用。网页界面当然就是任何能打开网页的平台都支持，而客户端软件也支持相当多平台，Windows、Linux、iOS、Android皆有。
除了云存储之外，ownCloud也可用于同步行事历、电子邮件联系人、网页浏览器的书签；此外还有多人在线文件同步协作的功能。(维基百科).
	同时ownCloud使用php语言进行开发的,github地址:https://github.com/owncloud,官网地址: http://owncloud.org

安装:

1)前提

需要安装php && http服务器软件.
这里我安装php7和nginx,具体安装方法请参考网上或者<php7和nignx一键安装脚本>

需要安装mysql || mariadb
mariadb:是mysql的一个开源分支,兼容mysql.这里为了方便一下,直接用yum安装mariadb了
先搜索一下包:
{% codeblock %}
[root@niaoyun49026 libiconv-1.14]# yum search mariadb
#启动mariadb
systemctl start mariadb
#可以设置为开机启动
systemctl enable mariadb 
#设置 root密码等相关
mysql_secure_installation

#停止
systemctl stop mariadb

#测试登陆
mysql -uroot -p

{% endcodeblock %}

设置db密码等安全:

{% codeblock %}
[root@niaoyun49026 libiconv-1.14]# mysql_secure_installation 
/usr/bin/mysql_secure_installation: line 379: find_mysql_client: command not found

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] 
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] 
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] 
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
[root@niaoyun49026 libiconv-1.14]# 

{% endcodeblock %}

2)安装
先下载源码:
{% codeblock %}
[root@niaoyun49026 data]# mkdir /data/codebase
[root@niaoyun49026 data]# wget https://download.owncloud.org/community/owncloud-9.1.0.tar.bz2
--2016-07-27 17:21:11--  https://download.owncloud.org/community/owncloud-9.1.0.tar.bz2
Resolving download.owncloud.org (download.owncloud.org)... 188.40.127.122, 148.251.209.106, 188.40.68.177, ...
Connecting to download.owncloud.org (download.owncloud.org)|188.40.127.122|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 29055490 (28M) [application/x-bzip2]
Saving to: wncloud-9.1.0.tar.bz2
2016-07-27 17:44:55 (21.0 KB/s) - wncloud-9.1.0.tar.bz2saved [29055490/29055490]
{% endcodeblock %}
 
配置数据文件夹:
{% codeblock %}
#解压:
tar -xvf /data/owncloud-9.1.0.tar.bz2 -C /data/codebase/
#进入owncloud文件
cd /data/codebase/owncloud
#owncloud需要对apps、data、config目录有write的权限，要是没有这三个文件需要手动创建
#由于wget下来的版本没有data，那么就来mkdir
mkdir data
#分别给予write权限
sudo chown -R www: www data
sudo chown -R  www: www config
sudo chown -R  www: www apps
{% endcodeblock %}

 nginx配置:
{% codeblock %}
upstream php-handler {
    server 127.0.0.1:9000;
    #server unix:/var/run/php5-fpm.sock;
}

server {
    listen 80;
    server_name cloud.miaoxiaohei.com;
    # enforce https
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name cloud.miaoxiaohei.com;

    ssl_certificate /data/ssl/cloud.miaoxiaohei.com.crt;
    ssl_certificate_key /data/ssl/cloud.miaoxiaohei.com.key;

    # Add headers to serve security related headers
    # Before enabling Strict-Transport-Security headers please read into this topic first.
    # add_header Strict-Transport-Security "max-age=15768000; includeSubDomains; preload;";
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Robots-Tag none;
    add_header X-Download-Options noopen;
    add_header X-Permitted-Cross-Domain-Policies none;

    # Path to the root of your installation
    root /data/codebase/owncloud/;

    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }

    # The following 2 rules are only needed for the user_webfinger app.
    # Uncomment it if you're planning to use this app.
    #rewrite ^/.well-known/host-meta /public.php?service=host-meta last;
    #rewrite ^/.well-known/host-meta.json /public.php?service=host-meta-json last;

    location = /.well-known/carddav {
        return 301 $scheme://$host/remote.php/dav;
    }
    location = /.well-known/caldav {
        return 301 $scheme://$host/remote.php/dav;
    }

    location /.well-known/acme-challenge { }

    # set max upload size
    client_max_body_size 512M;
    fastcgi_buffers 64 4K;

    # Disable gzip to avoid the removal of the ETag header
    gzip off;

    # Uncomment if your server is build with the ngx_pagespeed module
    # This module is currently not supported.
    # pagespeed off;

    error_page 403 /core/templates/403.php;
    error_page 404 /core/templates/404.php;

    location / {
        rewrite ^ /index.php$uri;
    }

    location ~ ^/(?:build|tests|config|lib|3rdparty|templates|data)/ {
        return 404;
    }
    location ~ ^/(?:\.|autotest|occ|issue|indie|db_|console) {
        return 404;
    }

    location ~ ^/(?:index|remote|public|cron|core/ajax/update|status|ocs/v[12]|updater/.+|ocs-provider/.+|core/templates/40[34])\.php(?:$|/) {
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        fastcgi_param HTTPS on;
        fastcgi_param modHeadersAvailable true; #Avoid sending the security headers twice
        fastcgi_param front_controller_active true;
        fastcgi_pass php-handler;
        fastcgi_intercept_errors on;
        fastcgi_request_buffering off;
    }

    location ~ ^/(?:updater|ocs-provider)(?:$|/) {
        try_files $uri/ =404;
        index index.php;
    }

    # Adding the cache control header for js and css files
    # Make sure it is BELOW the PHP block
    location ~* \.(?:css|js)$ {
        try_files $uri /index.php$uri$is_args$args;
        add_header Cache-Control "public, max-age=7200";
        # Add headers to serve security related headers (It is intended to have those duplicated to the ones above)
        # Before enabling Strict-Transport-Security headers please read into this topic first.
        # add_header Strict-Transport-Security "max-age=15768000; includeSubDomains; preload;";
        add_header X-Content-Type-Options nosniff;
        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-XSS-Protection "1; mode=block";
        add_header X-Robots-Tag none;
        add_header X-Download-Options noopen;
        add_header X-Permitted-Cross-Domain-Policies none;
        # Optional: Don't log access to assets
        access_log off;
    }

    location ~* \.(?:svg|gif|png|html|ttf|woff|ico|jpg|jpeg)$ {
        try_files $uri /index.php$uri$is_args$args;
        # Optional: Don't log access to other assets
        access_log off;
    }
}

{% endcodeblock %}

同时记得开发80和443端口:
{% codeblock %}

#80端口 http
iptables -A INPUT -p tcp -m tcp --sport 80 -j ACCEPT
iptables -A OUTPUT -p tcp -m tcp --dport 80 -j ACCEPT 
#443端口 https,ssl
iptables -A INPUT -p tcp -m tcp --sport 443 -j ACCEPT
iptables -A OUTPUT -p tcp -m tcp --dport 443 -j ACCEPT
{% endcodeblock %}

当然,如果不想那么烦,可以 将防火墙关闭(但这样很危险)
{% codeblock %}
systemctl stop  iptables.service
{% endcodeblock %}

在浏览器打开地址安装:

![安装](/images/owncloud_install.png)

数据选择有两种,SQLite||Mysql,其中sqlite比较省服务器资源,但对于访问量比较多的情况下,建议还是使用mysql咯








