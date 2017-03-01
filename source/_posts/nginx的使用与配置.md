---
title: nginx的使用与配置
date: 2016-07-17 20:51:08
tags: nginx
categories: liunx
---

安装完成nginx后,默认路径在/usr/local/nginx目录,在该目录可以看到一下文件
{% codeblock %}
[root@niaoyun49026 nginx]# ls -al
total 8
drwxr-xr-x  11 root   root  142 Jul 15 16:15 .
drwxr-xr-x. 13 root   root 4096 Jul 15 16:00 ..
drwx------   2 nobody root    6 Jul 15 16:15 client_body_temp(保存客户端请求包体的临时目录)
drwxr-xr-x   2 root   root 4096 Jul 15 16:00 conf(配置文件)
drwx------   2 nobody root    6 Jul 15 16:15 fastcgi_temp
drwxr-xr-x   2 root   root   38 Jul 15 16:00 html(nginx显示50x等错误信息的网页)
drwxr-xr-x   2 root   root   55 Jul 15 16:15 logs(nginx的日志文件)
drwx------   2 nobody root    6 Jul 15 16:15 proxy_temp
drwxr-xr-x   2 root   root   18 Jul 15 16:00 sbin(nginx的二进制文件)
drwx------   2 nobody root    6 Jul 15 16:15 scgi_temp
drwx------   2 nobody root    6 Jul 15 16:15 uwsgi_temp
{% endcodeblock %}

nginx的命令控制
{% codeblock %}
[root@niaoyun49026 sbin]# ./nginx -h
nginx version: nginx/1.11.1
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
  -?,-h         : this help(显示帮助信息)
  -v            : show version and exit(显示版本)
  -V            : show version and configure options then exit(显示版本&&编译时参数设置值)
  -t            : test configuration and exit(检测配置文件的设置是否有错误)
  -T            : test configuration, dump it and exit(检测配置设置,并显示详细信息)
  -q            : suppress non-error messages during configuration testing(检测配置文件时,使用-q参数可以不把error级别以下的信息显示)
  -s signal     : send signal to a master process: stop, quit, reopen, reload(发送信号到nginx master进程,控制nginx 进程停止,退出,重启等操作)
  -p prefix     : set prefix path (default: /usr/local/nginx/)(指定安装路径,是nignx在安装路径下查找配置文件启动)
  -c filename   : set configuration file (default: conf/nginx.conf)(指定配置文件路径启动)
  -g directives : set global directives out of configuration file(临时指定一些全局配置项,以使新的配置项生效.注意指定的配置项不能跟nginx.conf文件中的配置项冲突)
{% endcodeblock %}

使用:

{% codeblock %}
#检查配置文件设置是否正确
[root@niaoyun49026 sbin]# /usr/local/nginx/sbin/nginx -t
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
#使用-q参数,不显示error级别以下的信息,检查配置文件的时候
[root@niaoyun49026 sbin]# /usr/local/nginx/sbin/nginx -t -q
#查看nginx的版本
[root@niaoyun49026 sbin]# /usr/local/nginx/sbin/nginx -v
nginx version: nginx/1.11.1
#查看nginx的版本和编译时的设置参数
[root@niaoyun49026 sbin]# /usr/local/nginx/sbin/nginx -V
nginx version: nginx/1.11.1
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-4) (GCC) 
configure arguments:(因为使用默认参数编译,所以这里显示为空)
#快速停止nginx服务
[root@niaoyun49026 ~]# /usr/local/nginx/sbin/nginx -s stop
#相当于 kill -s SIGTRM <nginx master pid > || kill -s SIGINT <nginx master pid>
#优雅的停止nginx服务,先处理完当前所有的请求在提示服务
[root@niaoyun49026 ~]# /usr/local/nginx/sbin/nginx -s quit
#相当于 kill -s SIGQUIT <nginx master pid>
#如果要优雅的停止某个子线程的话
#kill -s SIGWINCH <nginx worker pid>

#重启nginx ,先检查配置项是否有误,如果都正确就优雅的方式停止服务,再启动nginx
[root@niaoyun49026 ~]# /usr/local/nginx/sbin/nginx -s reload
#相当于 kill -s SIGHUP <niginx master pid>

#使日志文件回滚,就是将日志文件备份后,移动到备份目录,在生成一个新日志文件
[root@niaoyun49026 ~]# /usr/local/nginx/sbin/nginx -s reopen
#相当于 kill -s SIGUSR1 <nginx master pid>

#启动nginx
[root@niaoyun49026 sbin]# /usr/local/nginx/sbin/nginx 
#启动nginx同时指定配置文件路径
[root@niaoyun49026 sbin]# /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
#启动nginx,以指定安装目录的方式启动
[root@niaoyun49026 sbin]# /usr/local/nginx/sbin/nginx -p /usr/local/nginx/
#启动nginx,使用-g参数临时指定配置项
[root@niaoyun49026 sbin]# /usr/local/nginx/sbin/nginx -g "pid /tmp/test.pid;"
#注意,因为这里临时指定了pid的文件路径,所有重启&&停止等操作,都要指定启动的时候的pid路径参数,否则会找不到pid,无法对进程进行操作
[root@niaoyun49026 sbin]# /usr/local/nginx/sbin/nginx -g "pid /tmp/test.pid;"

{% endcodeblock %}

认识nginx进程:

启动nginx服务,可以发现nginx有master process 和 worker process 在运行
{% codeblock %}
[root@niaoyun49026 ~]# ps -ef | grep  nginx
root      16230      1  0 21:47 ?        00:00:00 nginx: master process /usr/local/nginx/sbin/nginx
nobody    16231  16230  0 21:47 ?        00:00:00 nginx: worker process
root      16233  16175  0 21:47 pts/1    00:00:00 grep --color=auto nginx
{% endcodeblock %}
master process: 唯一的,该进程不会对客户端请求提供服务,主要负责管理worker进程,为管理员提供命令行服务,包括启动,停止,重启等服务,当任意一个worker进程出现错误时,master进程会理解启动一个新的worker进程,继续提供服务
worker process: 多个,处理客户端的请求.一般情况下,worker 进程数和服务器的cpu的核数相当(进程间切换的代价最小),worker进程之间通过共享内存,原子操作等进程间通信方式实现负载均衡等功能.不同于apache(一个进程或线程只能处理一个请求),nginx的一个worker进程可以同时处理的请求数只受限于内存大小.worker进程间处理并发请求几乎没有同步锁,worker进程通常不会进入睡眠状态.

