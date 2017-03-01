---
title: php7和nginx1.9安装脚本
date: 2016-02-19 12:52:43
tags: php
categories: liunx
comments: true
---

php7和nginx1.9安装脚本:

这里记录php7和nginx 1.9,安装脚本.
{% codeblock %}

#创建user
useradd www
#安装编译工具和需要的库包
yum install epel-* -y
yum install -y wget unzip gcc gcc-c++  make zlib zlib-devel pcre pcre-devel  libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers

#编译安装libiconv库
wget http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.14.tar.gz
tar zxf libiconv-1.14.tar.gz
cd libiconv-1.14
./configure --prefix=/usr
make
make install
cd ..
<!-- more -->

#安装php7
wget http://php.net/distributions/php-7.0.3.tar.bz2
tar -jxvf php-7.0.3.tar.bz2
cd php-7.0.3
#./configure --prefix=/usr/local/php7 --sysconfdir=/usr/local/php7/etc --with-config-file-scan-dir=/usr/local/php7/etc --enable-fpm --with-fpm-user=www --with-fpm-group=www --enable-mbstring --enable-sockets --enable-pcntl --enable-pdo --enable-mysqlnd --with-mysql=mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --enable-sysvshm --enable-shmop  --with-jpeg-dir=/usr --with-freetype-dir=/usr --with-png-dir=/usr --with-zlib-dir=/usr --with-gd --with-openssl --enable-opcache --enable-zip --enable-bcmath --enable-pcntl --enable-ftp --with-curl

./configure --prefix=/usr/local/php7 --sysconfdir=/usr/local/php7/etc --with-config-file-scan-dir=/usr/local/php7/etc --enable-fpm --with-fpm-user=www --with-fpm-group=www --enable-mbstring --enable-sockets --enable-pcntl --enable-pdo --enable-mysqlnd --with-mysql=mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --enable-sysvshm --enable-shmop  --with-jpeg-dir=/usr --with-freetype-dir=/usr --with-png-dir=/usr --with-zlib-dir=/usr --with-iconv=/usr/lib --with-gd --with-openssl --enable-opcache=no --enable-zip --enable-bcmath --enable-pcntl --enable-ftp --with-curl

make
make install
cp php.ini-production /usr/local/php7/etc/php.ini
cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm 
cp sapi/fpm/php-fpm.conf /usr/local/php7/etc/php-fpm.conf  
cp sapi/fpm/www.conf /usr/local/php7/etc/php-fpm.d/www.conf
cd ..

sed -i 's#short_open_tag = Off#short_open_tag = On#g' /usr/local/php7/etc/php.ini
chmod +x /etc/init.d/php-fpm

#安装nginx
wget http://nginx.org/download/nginx-1.9.11.tar.gz
tar -zxvf nginx-1.9.11.tar.gz
cd nginx-1.9.11
./configure --user=www --group=www --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
make;make install
cd ..

#配置nginx
#vi /usr/local/nginx/conf/nginx.conf
#...
#       location ~ \.php$ {
#            root           html;
#           fastcgi_pass   127.0.0.1:9000;
#            fastcgi_index  index.php;
#           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
#            include        fastcgi_params;
#        }

{% endcodeblock%}

