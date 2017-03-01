---
title: redis高级命令
date: 2016-07-03 18:53:36
tags:
---
redis高级命令：

订阅：

1	pusbscribe pattern [pattern ...] 
订阅通道匹配给定的模式。
2	pubsub subcommand [argument [argument ...]] 
讲述了PubSub的系统，例如它的客户是活动在服务器上的状态。
3	publish channel message 
发布一条消息到通道。
4	punsubscribe  [pattern [pattern ...]] 
停止监听发布到通道匹配给定模式的消息。
5	subscribe channel [channel ...] 
监听发布到指定的通道信息。
6	unsubscribe [channel [channel ...]] 
停止监听发布给定的通道信息。

php拓展使用方法：
https://github.com/nicolasff/phpredis pecl扩展包目前只提供了两个接口 publish  subscribe

phpredis是c写的php模块

https://github.com/jamm/Memory/blob/master/RedisServer.php

php这个是php是基于redis protocol的fsocketopen链接后操作的类库，提供的接口比较全面；publish可以进入数据，但是subscrbie没有阻塞；

可以在原类包当中修改其加入对阻塞模形的支持；

发布功能：

$redis = new Redis();

$res = $redis->connect($REDIS_HOSTS['CACHE']['host'], $REDIS_HOSTS['CACHE']['port'], 1 );

$res = $redis->publish($key,$value);

定阅功能：                           

$redis = new Redis();

$res = $redis->pconnect($REDIS_HOSTS['CACHE']['host'], $REDIS_HOSTS['CACHE']['port']);

$res = $redis->subscribe(array($key),array('SinaRedis','subscribe_handler'));

第二个参数为回调方法；

public static function subscribe_handler($redis, $channel, $msg){

            print_r($redis);

            echo $chan;

            echo $msg;

            return true;

  }

定阅 redis_subscriber.php

SinaRedis::subscribe('wangbin_test');

发布redis_publisher.php



事务：

1	discard 

取消事务，放弃执行事务块内的所有命令。

2	exec

执行所有事务块内的命令。

3	multi 

标记一个事务块的开始。

4	 unwatch 

取消 WATCH 命令对所有 key 的监视。

5	watch key [key ...] 

监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。



php扩展使用方法：

multi, exec, discard.

$ret = $redis->multi()

    ->set('key1', 'val1')

    ->get('key1')

    ->set('key2', 'val2')

    ->get('key2')

    ->exec();

/*

$ret == array(

    0 => TRUE,

    1 => 'val1',

    2 => TRUE,

    3 => 'val2');

*/

watch, unwatch

$redis->watch('x'); // or for a list of keys: $redis->watch(array('x','another key'));

/* long code here during the execution of which other clients could well modify `x` */

$ret = $redis->multi()

    ->incr('x')

    ->exec();

/*

$ret = FALSE if x has been modified between the call to WATCH and the call to EXE



脚本：

1	eval script numkeys key [key ...] arg [arg ...] 

执行 Lua 脚本。

参数说明：

script： 参数是一段 Lua 5.1 脚本程序。脚本不必(也不应该)定义为一个 Lua 函数。

numkeys： 用于指定键名参数的个数。

key [key ...]： 从 EVAL 的第三个参数开始算起，表示在脚本中所用到的那些 Redis 键(key)，这些键名参数可以在 Lua 中通过全局变量 KEYS 数组，用 1 为基址的形式访问( KEYS[1] ， KEYS[2] ，以此类推)。

arg [arg ...]： 附加参数，在 Lua 中通过全局变量 ARGV 数组访问，访问的形式和 KEYS 变量类似( ARGV[1] 、 ARGV[2] ，诸如此类)。



2	evalsha sha1 numkeys key [key ...] arg [arg ...] 

执行 Lua 脚本。

3	 script exists  script [script ...] 

查看指定的脚本是否已经被保存在缓存当中。

4     scirpt flush

从脚本缓存中移除所有脚本。

5	script kill

杀死当前正在运行的 Lua 脚本。

6	script load  script 

将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本

php 扩展的使用方法：





服务器命令：

序号	命令及描述

 1 bgrewriteaof

异步执行一个 AOF（AppendOnly File） 文件重写操作

2	bgsave

在后台异步保存当前数据库的数据到磁盘

3  client kill  [ip:port] [ID client-id] 

关闭客户端连接

4	client list

获取连接到服务器的客户端连接列表

5   client getname

获取连接的名称

6   client pause timeout 

在指定时间内终止运行来自客户端的命令

7  client setname  connection-name 

设置当前连接的名称

8  cluster slots

获取集群节点的映射数组

9	 commad

获取 Redis 命令详情数组

10	command count

获取 Redis 命令总数

11	command getkeys 

获取给定命令的所有键

12	time

返回当前服务器时间

13	command info  command-name [command-name ...] 

获取指定 Redis 命令描述的数组

14 config get parameter 

获取指定配置参数的值

15 config rewrite

对启动 Redis 服务器时所指定的 redis.conf 配置文件进行改写

16	config set  parameter value 

修改 redis 配置参数，无需重启

17 config resetstat

重置 INFO 命令中的某些统计数据

18	dbsize

返回当前数据库的 key 的数量

19  debug object key

获取 key 的调试信息

20	debug segfault

让 Redis 服务崩溃

21 flushall

删除所有数据库的所有key

22	flushdb

删除当前数据库的所有key

23	info [section] 

获取 Redis 服务器的各种信息和统计数值

24 lastsave

返回最近一次 Redis 成功将数据保存到磁盘上的时间，以 UNIX 时间戳格式表示

25  monitor

实时打印出 Redis 服务器接收到的命令，调试用

26  role

返回主从实例所属的角色

27 save

异步保存数据到硬盘

28 shutdown [NOSAVE] [SAVE] 

异步保存数据到硬盘，并关闭服务器

29  slaveof host port 

将当前服务器转变为指定服务器的从属服务器(slave server)

30  slowlog subcommand [argument] 

管理 redis 的慢日志

31 sync

用于复制功能(replication)的内部命令

