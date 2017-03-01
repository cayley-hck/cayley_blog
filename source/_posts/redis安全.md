---
title: redis安全
date: 2016-07-03 18:53:43
tags:
---
redis 安全：

密码：
Redis数据库可以设置安全，所以做出相关的任何客户端都需要在执行命令之前进行身份验证。为了确保Redis需要设置在配置文件中的密码验证一致。
例子
下面给出的例子显示的步骤，以确保Redis实例。
127.0.0.1:6379> CONFIG get requires
1) "requirepass"
2) ""
默认情况下，此属性为空，表示没有设置密码，此实例。您可以通过执行以下命令来更改这个属性

127.0.0.1:6379> CONFIG set requirepass "yiibai"
OK
127.0.0.1:6379> CONFIG get requirepass
1) "requirepass"
2) “kai"

或者修改redis配置文件项  requirepass  ‘kai’,重启服务器
设置密码，如果任何客户端运行命令没有验证后，再(错误)NOAUTH需要验证。错误将再回到这点。因此，客户端需要使用AUTH命令进行认证。

语法
AUTH命令的基本语法如下所示：

127.0.0.1:6379> AUTH password
语法
127.0.0.1:6379> AUTH "yiibai"
OK
127.0.0.1:6379> SET mykey "Test value"
OK
127.0.0.1:6379> GET mykey
"Test value"

备份：

Redis SAVE命令用来创建备份当前Redis数据库。

语法
Redis SAVE命令的基本语法如下所示：

127.0.0.1:6379> SAVE
例子
下面给出的例子创建备份当前的数据库。

127.0.0.1:6379> SAVE

OK

这个命令将创建dump.rdb文件在Redis目录。
还原Redis数据
要恢复Redis数据只是移动Redis备份文件(dump.rdb)到Redis目录，然后启动服务器。为了让Redis读取到Redis目录，使用CONFIG命令如下所示：
 
127.0.0.1:6379> CONFIG get dir

1) "dir"
2) "/user/yiibai/redis-2.8.13/src"

在上述的输出命令/user/yiibai/redis-2.8.13/src是目录，在安装redis服务器。

Bgsave
要创建Redis备份备用命令BGSAVE也可以的。这个命令将开始备份过程，并在后台运行。

例子
127.0.0.1:6379> BGSAVE

Background saving started

AOF 持久化：

AOF 持久化记录服务器执行的所有写操作命令，并在服务器启动时，通过重新执行这些命令来还原数据集。AOF 文件中的命令全部以 Redis 协议的格式来保存，新命令会被追加到文件的末尾。Redis 还可以在后台对 AOF 文件进行重写（rewrite），使得 AOF 文件的体积不会超出保存数据集状态所需的实际大小

通过config get appendonly 命令查看是否开启aof
通过修改配置文件来打开 AOF 功能：

appendonly yes  
或者通过
127.0.0.1:6379> CONFIG set appendonly yes
OK

启动aof后，每当 Redis 执行一个改变数据集的命令时（比如 SET），这个命令就会被追加到 AOF 文件的末尾。
这样的话，当 Redis 重新启时，程序就可以通过重新执行 AOF 文件中的命令来达到重建数据集的目的。

当aof文件大到一定程度，可以通过bgrewriteaof 命令重写aof文件，也可以通过配置文件设置自动触发aof重写



RDB 持久化切换到 AOF 持久化:



1.为最新的 dump.rdb 文件创建一个备份。

2.将备份放到一个安全的地方。

3.执行以下两条命令：

redis-cli> CONFIG SET appendonly yes

redis-cli> CONFIG SET save ""

4.确保命令执行之后，数据库的键的数量没有改变。

5.确保写命令会被正确地追加到 AOF 文件的末尾。

步骤 3 执行的第一条命令开启了 AOF 功能：Redis 会阻塞直到初始 AOF 文件创建完成为止，之后 Redis 会继续处理命令请求，并开始将写入命令追加到 AOF 文件末尾。

步骤 3 执行的第二条命令用于关闭 RDB 功能。这一步是可选的，如果你愿意的话，也可以同时使用 RDB 和 AOF 这两种持久化功能。

别忘了在 redis.conf 中打开 AOF 功能！否则的话，服务器重启之后，之前通过 CONFIG SET 设置的配置就会被遗忘，程序会按原来的配置来启动服务器。



RDB 和 AOF 之间的相互作用:

在版本号大于等于 2.4 的 Redis 中，BGSAVE 执行的过程中，不可以执行 BGREWRITEAOF 。反过来说，在 BGREWRITEAOF 执行的过程中，也不可以执行 BGSAVE 。

这可以防止两个 Redis 后台进程同时对磁盘进行大量的 I/O 操作。

如果 BGSAVE 正在执行，并且用户显示地调用 BGREWRITEAOF 命令，那么服务器将向用户回复一个 OK 状态，并告知用户，BGREWRITEAOF 已经被预定执行：一旦 BGSAVE 执行完毕，BGREWRITEAOF 就会正式开始。

当 Redis 启动时，如果 RDB 持久化和 AOF 持久化都被打开了，那么程序会优先使用 AOF 文件来恢复数据集，因为 AOF 文件所保存的数据通常是最完整的。