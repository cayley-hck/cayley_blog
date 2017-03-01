---
title: redis配置
date: 2016-07-03 18:53:13
tags:
---
redis配置：

使用方法：

1）使用文件：在启动service是使用文件 
{% codeblock %}
/usr/local/redis/bin/redis-server /config.conf
{% endcodeblock %}

2）启动redis后在程序中配置
通过config set config_name  config_value 设置
通过config get config_name :(*表示所有)

配置说明：

常用
1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
{% codeblock %}
    daemonize 
 {% endcodeblock %}

2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
{% codeblock %}
    pidfile /var/run/redis.pid
 {% endcodeblock %}
3. 指定Redis监听端口，默认端口为6379，如果你设为 0 ，redis 将不在 socket 上监听任何客户端连接
{% codeblock %}
    port 6379
 {% endcodeblock %}
4. 绑定的主机地址  示例，多个IP用空格隔开:
{% codeblock %}
    bind 127.0.0.1 
 {% endcodeblock %}
5.当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能 
{% codeblock %}
    timeout 300
 {% endcodeblock %}

6. 指定日志记录级别，Redis总共支持四个级别：debug（测试环境）、verbose、notice（生产环境）、warning（仅一些重要的信息），默认为verbose
{% codeblock %}
    loglevel verbose
 {% endcodeblock %}

7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
{% codeblock %}
    logfile stdout
 {% endcodeblock %}

 要想把日志记录到系统日志，就把它改成 yes，
 也可以可选择性的更新其他的syslog 参数以达到你的要求
 syslog-enabled no
 
 设置 syslog 的 identity。
{% codeblock %}

<!-- more -->

 syslog-ident redis
  {% endcodeblock %}
 设置 syslog 的 facility，必须是 USER 或者是 LOCAL0-LOCAL7 之间的值。
 syslog-facility local0
8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
{% codeblock %}
    databases 16
 {% endcodeblock %}


快照备份：

9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
{% codeblock %}
    save <seconds> <changes>

    Redis默认配置文件中提供了三个条件：

    save 900 1

    save 300 10

    save 60 10000
 {% endcodeblock %}
    分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。

   注意：你可以注释掉所有的 save 行来停用保存功能。
   也可以直接一个空字符串来实现停用：


10. 默认情况下，如果 redis 最后一次的后台保存失败，redis 将停止接受写操作，

 这样以一种强硬的方式让用户知道数据不能正确的持久化到磁盘，
 否则就会没人注意到灾难的发生。

 如果后台保存进程重新启动工作了，redis 也将自动的允许写操作。

 然而你要是安装了靠谱的监控，你可能不希望 redis 这样做，那你就改成 no 好了。
{% codeblock %}
stop-writes-on-bgsave-error yes
 {% endcodeblock %}
12. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
{% codeblock %}
    rdbcompression yes
 {% endcodeblock %}
13. 指定本地数据库文件名，默认值为dump.rdb
{% codeblock %}
    dbfilename dump.rdb
 {% endcodeblock %}
13. 指定本地数据库存放目录
{% codeblock %}
    dir ./
 {% endcodeblock %}
主从复制

 主从复制。使用 slaveof 来让一个 redis 实例成为另一个reids 实例的副本。
 注意这个只需要在 slave 上配置。
14. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
{% codeblock %}
    slaveof <masterip> <masterport>
 {% endcodeblock %}
15. 当master服务设置了密码保护时，slav服务连接master的密码
{% codeblock %}
    masterauth <master-password>
 {% endcodeblock %}
16.是否返回同步不及时的时候的信息 
 当一个 slave 与 master 失去联系，或者复制正在进行的时候，
 slave 可能会有两种表现：

 1) 如果为 yes ，slave 仍然会应答客户端请求，但返回的数据可能是过时，
    或者数据可能是空的在第一次同步的时候

 2) 如果为 no ，在你执行除了 info he salveof 之外的其他命令时，
    slave 都将返回一个 "SYNC with master in progress" 的错误，

{% codeblock %}
slave-serve-stale-data yes
  {% endcodeblock %}
17.配置一个 slave 实体是否接受写入操作。
 通过写入操作来存储一些短暂的数据对于一个 slave 实例来说可能是有用的，
 因为相对从 master 重新同步数而言，据数据写入到 slave 会更容易被删除。
 但是如果客户端因为一个错误的配置写入，也可能会导致一些问题。
 从 redis 2.6 版起，默认 slaves 都是只读的。
 注意：只读的 slaves 没有被设计成在 internet 上暴露给不受信任的客户端。
 它仅仅是一个针对误用实例的一个保护层。
slave-read-only yes
 
18.Slaves 在一个预定义的时间间隔内发送 ping 命令到 server 。
 你可以改变这个时间间隔。默认为 10 秒。
 为确认主库是否可用
{% codeblock %}
 repl-ping-slave-period 10
  {% endcodeblock %}
19. 设置主从复制过期时间
 这个值一定要比 repl-ping-slave-period 大

# repl-timeout 60
 
20。  Disable TCP_NODELAY on the slave socket after SYNC?
{% codeblock %}

#
# If you select "yes" Redis will use a smaller number of TCP packets and
# less bandwidth to send data to slaves. But this can add a delay for
# the data to appear on the slave side, up to 40 milliseconds with
# Linux kernels using a default configuration.
#
# If you select "no" the delay for data to appear on the slave side will
# be reduced but more bandwidth will be used for replication.
#
# By default we optimize for low latency, but in very high traffic conditions
# or when the master and slaves are many hops away, turning this to "yes" may
# be a good idea.
repl-disable-tcp-nodelay no
  {% endcodeblock %}
21.设置主从复制容量大小。这个 backlog 是一个用来在 slaves 被断开连接时
 存放 slave 数据的 buffer，所以当一个 slave 想要重新连接，通常不希望全部重新同步，
 只是部分同步就够了，仅仅传递 slave 在断开连接时丢失的这部分数据。
 这个值越大，salve 可以断开连接的时间就越长。

# repl-backlog-size 1mb
 
22.在某些时候，master 不再连接 slaves，backlog 将被释放
{% codeblock %}
# A value of 0 means to never release the backlog.
# 如果设置为 0 ，意味着绝不释放 backlog 。
#
# repl-backlog-ttl 3600
  {% endcodeblock %}
23当 master 不能正常工作的时候，Redis Sentinel 会从 slaves 中选出一个新的 master，
 这个值越小，就越会被优先选中，但是如果是 0 ， 那是意味着这个 slave 不可能被选中。
 默认优先级为 100。
slave-priority 100
安全：
24. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
{% codeblock %}
    requirepass foobared
 {% endcodeblock %}
限制：

25. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
{% codeblock %}
    maxclients 128
 {% endcodeblock %}
26. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
{% codeblock %}
    maxmemory <bytes>
 {% endcodeblock %}
27. 最大内存策略，你有 5 个选择。
{% codeblock %}
# 
# volatile-lru -> 使用 LRU 算法移除包含过期设置的 key 。
# allkeys-lru -> 根据 LRU 算法移除所有的 key 。
# volatile-random -> remove a random key with an expire set：移除随机包含过期设置的key
# allkeys-random -> remove a random key, any key:移除随机所有过期设置的key
# volatile-ttl -> remove the key with the nearest expire time (minor TTL)移除随机最近过期的key
# noeviction -> 不让任何 key 过期，只是给写入操作返回一个错误
#
# The default is:
#
# maxmemory-policy noeviction
 {% endcodeblock %}
28. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
{% codeblock %}
    appendonly no
 {% endcodeblock %}
39. 指定更新日志文件名，默认为appendonly.aof
{% codeblock %}
     appendfilename appendonly.aof
 {% endcodeblock %}
30. 指定更新日志条件，共有3个可选值： 
    no：表示等操作系统进行数据缓存同步到磁盘（快） 
    always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
    everysec：表示每秒同步一次（折衷，默认值）
{% codeblock %}
    appendfsync everysec
 {% endcodeblock %}

31.AOF策略设置为always或者everysec时，后台处理进程(后台保存或者AOF日志重写)会执行大量的I/O操作
在某些Linux配置中会阻止过长的fsync()请求。注意现在没有任何修复，即使fsync在另外一个线程进行处理
为了减缓这个问题，可以设置下面这个参数no-appendfsync-on-rewrite
{% codeblock %}
 no-appendfsync-on-rewrite no
 {% endcodeblock %}
32.AOF 自动重写

当AOF文件增长到一定大小的时候Redis能够调用 BGREWRITEAOF 对日志文件进行重写
它是这样工作的：Redis会记住上次进行些日志后文件的大小(如果从开机以来还没进行过重写，那日子大小在开机的时候确定)
基础大小会同现在的大小进行比较。如果现在的大小比基础大小大制定的百分比，重写功能将启动
同时需要指定一个最小大小用于AOF重写，这个用于阻止即使文件很小但是增长幅度很大也去重写AOF文件的情况
# 设置 percentage 
为0就关闭这个特性
{% codeblock %}
auto-aof-rewrite-percentage 100

auto-aof-rewrite-min-size 64mb
 {% endcodeblock %}
日志：

 Redis Slow Log 记录超过特定执行时间的命令。执行时间不包括I/O计算比如连接客户端，返回结果等，只是命令执行时间

可以通过两个参数设置slow log：一个是告诉Redis执行超过多少时间被记录的参数slowlog-log-slower-than(微妙)， 
另一个是slow log 的长度。当一个新命令被记录的时候最早的命令将被从队列中移除
 下面的时间以微妙微单位，因此1000000代表一分钟。
 
33注意制定一个负数将关闭慢日志，而设置为0将强制每个命令都会记录
{% codeblock %}
slowlog-log-slower-than 10000
 {% endcodeblock %}
34对日志长度没有限制，只是要注意它会消耗内存
{% codeblock %}
# 可以通过 SLOWLOG RESET 
回收被慢日志消耗的内存
slowlog-max-len 1024
 {% endcodeblock %}
VM:
35. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
{% codeblock %}
     vm-enabled no
 {% endcodeblock %}
36. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
{% codeblock %}
     vm-swap-file /tmp/redis.swap
 {% endcodeblock %}
37. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
{% codeblock %}
     vm-max-memory 0
 {% endcodeblock %}
38. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
{% codeblock %}
     vm-page-size 32
 {% endcodeblock %}
39. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
{% codeblock %}
     vm-pages 134217728
 {% endcodeblock %}
40. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
{% codeblock %}
     vm-max-threads 4

 {% endcodeblock %}

lua脚本：

41.执行lua脚本的超时时间
lua-time-limit 5000

集群：
42.启用或停用集群
# cluster-enabled yes
43.集群配置文件
# cluster-config-file nodes-6379.conf
44.节点超时时间
 {% codeblock %}
# cluster-node-timeout 15000
 
# A slave of a failing master will avoid to start a failover if its data
# looks too old.
#
# There is no simple way for a slave to actually have a exact measure of
# its "data age", so the following two checks are performed:
#
# 1) If there are multiple slaves able to failover, they exchange messages
#    in order to try to give an advantage to the slave with the best
#    replication offset (more data from the master processed).
#    Slaves will try to get their rank by offset, and apply to the start
#    of the failover a delay proportional to their rank.
#
# 2) Every single slave computes the time of the last interaction with
#    its master. This can be the last ping or command received (if the master
#    is still in the "connected" state), or the time that elapsed since the
#    disconnection with the master (if the replication link is currently down).
#    If the last interaction is too old, the slave will not try to failover
#    at all.
#
# The point "2" can be tuned by user. Specifically a slave will not perform
# the failover if, since the last interaction with the master, the time
# elapsed is greater than:
#
#   (node-timeout * slave-validity-factor) + repl-ping-slave-period
#
# So for example if node-timeout is 30 seconds, and the slave-validity-factor
# is 10, and assuming a default repl-ping-slave-period of 10 seconds, the
# slave will not try to failover if it was not able to talk with the master
# for longer than 310 seconds.
#
# A large slave-validity-factor may allow slaves with too old data to failover
# a master, while a too small value may prevent the cluster from being able to
# elect a slave at all.
#
# For maximum availability, it is possible to set the slave-validity-factor
# to a value of 0, which means, that slaves will always try to failover the
# master regardless of the last time they interacted with the master.
# (However they'll always try to apply a delay proportional to their
# offset rank).
#
# Zero is the only value able to guarantee that when all the partitions heal
# the cluster will always be able to continue.
#
# cluster-slave-validity-factor 10
 
# Cluster slaves are able to migrate to orphaned masters, that are masters
# that are left without working slaves. This improves the cluster ability
# to resist to failures as otherwise an orphaned master can't be failed over
# in case of failure if it has no working slaves.
#
# Slaves migrate to orphaned masters only if there are still at least a
# given number of other working slaves for their old master. This number
# is the "migration barrier". A migration barrier of 1 means that a slave
# will migrate only if there is at least 1 other working slave for its master
# and so forth. It usually reflects the number of slaves you want for every
# master in your cluster.
#
# Default is 1 (slaves migrate only if their masters remain with at least
# one slave). To disable migration just set it to a very large value.
# A value of 0 can be set but is useful only for debugging and dangerous
# in production.
#
# cluster-migration-barrier 1
 
# In order to setup your cluster make sure to read the documentation
# available at http://redis.io web site.

 {% endcodeblock %}

其它：

41. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
{% codeblock %}
    glueoutputbuf yes
 {% endcodeblock %}
42. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
{% codeblock %}
    hash-max-zipmap-entries 64

    hash-max-zipmap-value 512
 {% endcodeblock %}
43. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
{% codeblock %}
    activerehashing yes
 {% endcodeblock %}
44. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
{% codeblock %}
    include /path/to/local.conf
 {% endcodeblock %}