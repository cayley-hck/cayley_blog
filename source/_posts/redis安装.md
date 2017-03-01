---
title: redis安装
date: 2016-07-03 18:53:00
tags: redis
categories: redis
---
Linux下redis的安装

1.liunx
1)安装redis 
将redis安装到此目录 
/usr/local/redis
将安装包下载到此目录 
/usr/local/src

命令如下： 
可能要先安装gcc:
{% codeblock %}
yum install gcc
{% endcodeblock %}

{% codeblock %}
$ mkdir /usr/local/redis  
$ cd /usr/local/src  
$ wget http://redis.googlecode.com/files/redis-2.6.14.tar.gz  
$ tar xzf redis-2.6.14.tar.gz   
$ ln -s redis-2.6.14 redis #建立一个链接  
$ cd redis  
$ make PREFIX=/usr/local/redis install #安装到指定目录中
#注意上面的最后一行，我们通过PREFIX指定了安装的目录。如果make失败，一般是你们系统中还未安装gcc,那么可以通过yum安装： 
{% endcodeblock %}
<!-- more -->


在安装redis成功后，你将可以在/usr/local/redis看到一个bin的目录，里面包括了以下文件： 

redis-benchmark  redis-check-aof  redis-check-dump  redis-cli  redis-server

2)将redis做成一个服务 

a.复制脚本到/etc/rc.d/init.d目录 

安装Redis时，其服务脚本位于：
/usr/local/src/redis/utils/redis_init_script 
必须将其复制到/etc/rc.d/init.d的目录下： 
{% codeblock %}
cp /usr/local/src/redis/utils/redis_init_script /etc/rc.d/init.d/redis
#将redis_init_script复制到/etc/rc.d/init.d/，同时易名为redis。
{% endcodeblock %}

添加注册服务：
{% codeblock %}
chkconfig --add redis
{% endcodeblock %}

如果报错话：redis脚本格式不支持chkconfig
需要修改redis脚本。 

b.修改redis脚本 
打开使用vi打开脚本，查看脚本信息： 
{% codeblock %}
vim /etc/rc.d/init.d/redis
看到的内容如下(下内容是更改好的信息)： 

#!/bin/sh 
#chkconfig: 2345 80 90 
# Simple Redis init.d script conceived to work on Linux systems 
# as it does use of the /proc filesystem. 
REDISPORT=6379 
EXEC=/usr/local/redis/bin/redis-server 
CLIEXEC=/usr/local/redis/bin/redis-cli 
PIDFILE=/var/run/redis_${REDISPORT}.pid 
CONF="/etc/redis/${REDISPORT}.conf" 
case "$1" in 
start) 
if [ -f $PIDFILE ] 
then 
echo "$PIDFILE exists, process is already running or crashed" 
else 
echo "Starting Redis server..." 
$EXEC $CONF & 
fi 
;; 
stop) 
if [ ! -f $PIDFILE ] 
then 
echo "$PIDFILE does not exist, process is not running" 
else 
PID=$(cat $PIDFILE) 
echo "Stopping ..." 
$CLIEXEC -p $REDISPORT shutdown 
while [ -x /proc/${PID} ] 
do 
echo "Waiting for Redis to shutdown ..." 
sleep 1 
done 
echo "Redis stopped" 
fi 
;; 
*) 
echo "Please use start or stop as first argument" 
;; 
esac 
{% endcodeblock %}

和原配置文件相比： 

1.原文件是没有以下第2行的内容的，

#chkconfig: 2345 80 90 
2.原文件EXEC、CLIEXEC参数，也是有所更改。 

EXEC=/usr/local/redis/bin/redis-server   
CLIEXEC=/usr/local/redis/bin/redis-cli 

3.redis开启的命令，以后台运行的方式执行。

$EXEC $CONF & 
ps:注意后面的那个“&”，即是将服务转到后面运行的意思，否则启动服务时，Redis服务将 

占据在前台，占用了主用户界面，造成其它的命令执行不了。 
4.将redis配置文件拷贝到/etc/redis/${REDISPORT}.conf 
{% codeblock %}
mkdir /etc/redis    
cp /usr/local/src/redis/redis.conf /etc/redis/6379.conf
{% endcodeblock %}

这样，redis服务脚本指定的CONF就存在了。默认情况下，Redis未启用认证，可以通过开启6379.conf的requirepass 指定一个验证密码。 

以上操作完成后，即可注册yedis服务：
{% codeblock %}
chkconfig --add redis
{% endcodeblock %}

3.启动redis服务 
{% codeblock %}
service redis start 
{% endcodeblock %}

第三，将Redis的命令所在目录添加到系统参数PATH中 

修改profile文件：
{% codeblock %}
vi /etc/profile
#在最后行追加: 

export PATH="$PATH:/usr/local/redis/bin"
然后马上应用这个文件： 

. /etc/profile  
{% endcodeblock %}

这样就可以直接调用redis-cli的命令了，如下所示： 
{% codeblock %}
$ redis-cli   
redis 127.0.0.1:6379> auth superman   
OK   
redis 127.0.0.1:6379> ping   
PONG   
redis 127.0.0.1:6379>
{% endcodeblock %}
至此，redis 就成功安装了。 


