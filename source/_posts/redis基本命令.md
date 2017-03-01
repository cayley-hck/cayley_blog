---
title: redis基本命令
date: 2016-07-03 18:53:23
tags:
---
redi基本命令:
连接redis:
$redis-cli -h host -p port -a password

判断本机是否安装redis服务：使用ping命令

$redis-cli
redis 127.0.0.1:6379>
redis 127.0.0.1:6379> PING

PONG
1 auth password 
验证密码是否正确
2 echo message 
打印字符串
3 ping 
查看服务是否运行
4 quit 
关闭当前连接
5 select index 
切换到指定的数据库

1）redis键命令

1	del key
此命令删除键，如果存在
2	dump key 
该命令返回存储在指定键的值的序列化版本。
3	exists key 
此命令检查该键是否存在。
4	expire key seconds
指定键的过期时间
5	expireat key timestamp 
指定的键过期时间。在这里，时间是在Unix时间戳格式
6	pexpire key milliseconds 
设置键以毫秒为单位到期
7	pexpireat key milliseconds-timestamp 
设置键在Unix时间戳指定为毫秒到期
8	keys pattern 
查找与指定模式匹配的所有键
9	move key db 
移动键到另一个数据库
10	persist key 
移除过期的键
11	pttl key 
以毫秒为单位获取剩余时间的到期键。
12	ttl key 
获取键到期的剩余时间。
13	randomkey
从Redis返回随机键
14	rename key newkey 
更改键的名称
15	renamenx key newkey 
重命名键，如果新的键不存在
16	type key 
返回存储在键的数据类型的值。

2）字符串命令
1 set key value 
此命令用于在指定键设置值
2 get key 
键对应的值。
3	getrange key start end 
得到字符串的子字符串
4 getset key value
设置键的字符串值，并返回旧值
5 getbit key offset
返回存储在键位值的字符串值的偏移
6 mget key1 [key2..]
得到所有的给定键的值
7 stet key offset value
设置或清除该位在存储在键的字符串值偏移
8 setex key seconds value
设置值和到期时间
9 setnt key value
设置键的值，只有当该键不存在
10 strange key offset value
覆盖字符串的一部分从指定键的偏移
11 strlen key
得到存储在键的值的长度
12 mset key value [key value ...]
设置多个键和多个值
13 msetnx key value [key value ...] 
设置多个键多个值，只有在当没有按键的存在时
14 psetex key milliseconds value
设置键的毫秒值和到期时间
15	incr key
增加键的整数值一次
16	incrbykey increment
由给定的数量递增键的整数值
17 incrbyfloat key increment
由给定的数量递增键的浮点值
18 decr key
递减键一次的整数值
19 decr key decrement
由给定数目递减键的整数值
20	append key value
追加值到一个键  字符

3）哈希命令
1 hdel key field2 [field2] 
删除一个或多个哈希字段
2 hexists key field 
判断一个哈希字段存在与否
3 hget key field 
获取存储在指定的键散列字段的值
4 hgetall key 
让所有的字段和值在指定的键存储在一个哈希
5	hincrby  key field increment 
由给定数量增加的哈希字段的整数值
6	hincrbyfloat key field increment 
由给定的递增量哈希字段的浮点值
7	keys key 
获取所有在哈希字段
8	hlen key 
获取哈希字段数
9	hmget key field1 [field2] 
获得所有给定的哈希字段的值
10	hmset key field1 value1 [field2 value2 ] 
设置多个哈希字段的多个值
11 hset key field value 
设置哈希字段的字符串值
12 hsetnx key field value 
设置哈希字段的值，仅当该字段不存在
13 hvals key 
获取在哈希中的所有值
14 hscan key cursor [MATCH pattern] [COUNT count] 
增量迭代哈希字段及相关值

4)列表
1	blpop key1 [key2 ] timeout 
取出并获取列表中的第一个元素，或阻塞，直到有可用
2 brpop key1 [key2 ] timeout 
取出并获取列表中的最后一个元素，或阻塞，直到有可用
3	brpopLpush source destination timeout 
从列表中弹出一个值，它推到另一个列表并返回它;或阻塞，直到有可用
4 lindex key index 
从一个列表其索引获取对应的元素
5	LINSERT key BEFORE|AFTER pivot value 
在列表中的其他元素之后或之前插入一个元素
6 llen key 
获取列表的长度
7	pop key 
获取并取出列表中的第一个元素
8 lpush key value1 [value2] 
在前面加上一个或多个值的列表
9 lpushx key value 
在前面加上一个值列表，仅当列表中存在
10 lrange key start stop 
从一个列表获取各种元素
11 lrem key count value 
时间复杂度中N表示链表中元素的数量。在指定Key关联的链表中，删除前count个值等于value的元素。如果count大于0，从头向尾遍历并删除，如果count小于0，则从尾向头遍历并删除。如果count等于0，则删除链表中所有等于value的元素。如果指定的Key不存在，则直接返回0。
从列表中删除元素
12 lset key index value 
在列表中的索引设置一个元素的值
13	ltrim key start stop 
修剪列表到指定的范围内
14 rpop key 
取出并获取列表中的最后一个元素
15	rpoplpush source destination 
删除最后一个元素的列表，将其附加到另一个列表并返回它
16 rpush key value1 [value2] 
添加一个或多个值到列表
17 rpushx key value 
添加一个值列表，仅当列表中存在


4)集合
1 sadd key member1 [member2] 
向集合添加一个或多个成员
2	scard key 
获取集合的成员数
3	sdiff key1 [key2] 
返回给定所有集合的差集
4	sdiffstore destination key1 [key2] 
返回给定所有集合的差集并存储在 destination 中
5	sinter key1 [key2] 
返回给定所有集合的交集
6 sinterstore destination key1 [key2] 
返回给定所有集合的交集并存储在 destination 中
7	sismember key member 
判断 member 元素是否是集合 key 的成员
8 smembers key 
返回集合中的所有成员
9	smove source destination member 
将 member 元素从 source 集合移动到 destination 集合
10 spop key 
移除并返回集合中的一个随机元素
11	srandmember key [count] 
返回集合中一个或多个随机数
12	srem key member1 [member2] 
移除集合中一个或多个成员
13 sunion key1 [key2] 
返回所有给定集合的并集
14 sunionstore destination key1 [key2] 
所有给定集合的并集存储在 destination 集合中
15	SSCAN key cursor [MATCH pattern] [COUNT count] 
迭代集合中的元素

有序集合：
1 zadd key score1 member1 [score2 member2] 
向有序集合添加一个或多个成员，或者更新已存在成员的分数
2 zcrad key 
获取有序集合的成员数
3  zcount key min max 
计算在有序集合中指定区间分数的成员数
4	zincrby key increment member 
有序集合中对指定成员的分数加上增量 increment
5	zinterstore destination numkeys key [key ...] 
计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中
6	zlexcount  key min max 
在有序集合中计算指定字典区间内成员数量
7	zrange key start stop [WITHSCORES] 
通过索引区间返回有序集合成指定区间内的成员
8	 zrangebylex key min max [LIMIT offset count] 
通过字典区间返回有序集合的成员
9	 zrangebyscore key min max [WITHSCORES] [LIMIT] 
通过分数返回有序集合指定区间内的成员
10	  zrank key member 
返回有序集合中指定成员的索引
11 zrem key member [member ...] 
移除有序集合中的一个或多个成员
12 zremrangebylex    key min max 
移除有序集合中给定的字典区间的所有成员
13	zremrangebyrank  key start stop 
移除有序集合中给定的排名区间的所有成员
14	zremrangebyscore  key min max 
移除有序集合中给定的分数区间的所有成员
15  zrevrange key start stop [WITHSCORES] 
返回有序集中指定区间内的成员，通过索引，分数从高到底
16	zrevrangebyscore  key max min [WITHSCORES] 
返回有序集中指定分数区间内的成员，分数从高到低排序
17  zrevrank key member 
返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序
18	zscore key member 
返回有序集中，成员的分数值
19	zunionstore destination numkeys key [key ...] 
计算给定的一个或多个有序集的并集，并存储在新的 key 中
20	ZSCAN key cursor [MATCH pattern] [COUNT count] 
迭代有序集合中的元素（包括元素成员和元素分值）