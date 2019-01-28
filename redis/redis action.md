#  Redis实战

Redis是开源的，基于键值对的的存储服务系统。支持多种数据结构，高性能功能丰富。

作者：Salvatore Sanfilippo（antirez）

23000 lines of code。



- 高性能key-value服务器
- 多种数据结构
- 丰富的功能
- 高可用分布式支持

Redis八个特性：

- 速度快：10w ops，存储在内存，C语言编写，单线程。
- 持久化：rdb和aof
- 多种数据结构：主要的有5种
- 支持多种编程语言：java、python、lua、nodejs等
- 功能丰富：发布订阅，事务，lua脚本、事务、pipeline
- 简单：23000 line of code，带着问题看代码，不依赖外部库，单线程模型，
- 主从复制：
- 高可用、分布式：Redis-Sentinel（v2.8）高可用，Redis-Cluster（v3.0）分布式

多种数据结构（衍生）：

- BitMaps：位图
- HyperLogLog：嘲笑内存唯一值计数
- GEO：地理信息定位

典型使用场景：

- 缓存系统：
- 计数器：提供了相应的计数命令
- 消息队列系统：
- 排行榜：提供了有序集合，排行榜。
- 社交网络
- 实时系统：例如使用位图实现过滤器功能

Redis安装

```
$ wget http://download.redis.io/releases/redis-3.0.7.tar.gz
$ sudo tar -xvf redis-3.0.7.tar.gz 
$ sudo ln -s redis-3.0.7 redis
$ cd redis
$ sudo make && make install
```

- Redis-server Redis服务器
- redis-cli Redis命令行客户端
- redis-benchmar 基准测试，性能测试
- redis-check-aof 对aof进行修复
- redis-check-dump rdb修复检查工具
- redis-sentinel Sentinel服务器

三种启动方式

- 最简启动：
- 配置文件启动
- 动态参数启动

生产环境选择配置启动。

redis连接：

`# redis-cli -h host -p port`

Redis客户端返回值

- daemonize：是否是守护进程
- port：对外端口，默认6379，Merz 意大利歌手
- logfile：Redis系统日志
- dir：Redis工作目录





通用命令：

- keys：计算所有的键，一般不在生产环境使用。keys是一个重命令，单线程可能会阻塞其他命令。可以通过热备从节点使用，或用scan。
- dbsize：可以在生产环境使用，redis内置了计数器。O（1）
- exists key：
- del key
- expire key seconds，ttl key可以查看过期时间，persist，去掉key的过期时间。
- type key



数据结构与内部编码：

- string：
  - raw，int，embstr
- hash：
  - hashtable，ziplist
- list：
  - linkedlist，ziplist
- set：
  - hashtable，intset
- zset：
  - skiplist，ziplist

单线程架构

redis在一个瞬间只会执行一条命令。拒绝长（慢）命令，如keys，flushall等。

- 纯内存
- 非阻塞io
- 无线程切换开销



### 字符串：

字符串的value不能大于512M，命令：get、set、del、incr、decr、incrby、decrby。

如：incrby counter 3

场景：缓存，计数器，分布式锁等等。

setnx key value：key不存在，才设置。类似于add

set key value xx：key存在，才设置。类似于update

mset，mget批量操作:省去大量网络操作的时间。

getset key newvalue ：相当于set key newvalue并返回旧的value

append key value ： 将value追加到旧的value

strlen key 返回字符串的长度。

incrbyfload key 3.5

getrange key start end：获取字符串制定下标的所有值

setrange key index value 

### Hash

特点：Mapmap，Small redis，Field不能相同 value可以相同

#### 重要api

hget、hset、hdel

hexists，hlen

hgetall key ： 返回hash key对应所有的field和value

hvals key：返回所有field的valud

hkeys key ：返回所有field

小心使用hgetall

实现用户信息的三种方案：

1. string v1:hashmap序列化。序列化开销大。
2. key值，分散的key-value实现
3. hash：直观，节省空间，可以部分更新，hash编程复杂，无法控制过期时间。

### 列表

有序的，可以重复的，左右两边插入或弹出

#### 重要api

1. 增
   - rpush key value1 value2 ...
   - linsert key before|after value newValue
2. 删除
   - lpop key：从列表左边弹出一个item
   - rpop key
   - lrem key count value 
   - ltrim key start end 按照索引范围修剪列表，做大的列表删除。
3. 查询
   - lrange key start end（包含end）获取列表制定索引范围的item
   - lindex key index。O(N)
   - llen 获取列表的长度
4. 改
   - lset key index newValue

#### 补充命令

blpop key timeout：

lpop的阻塞版本

#### Tips

1. LPUSH + LPOP = STACK
2. LPUSH + RPOP = QUEUE
3. LPUSH + LTRIM = Capped Collection
4. LPUSH + BRPOP = Message Queue

### 集合

无序的，无重复的，支持集合间的操作

#### API

- sadd key element，srem
- scard 计算集合的大小
- sismember
- srandmember user:1:follow count=his 从集合中随机挑count个元素
- spop user:1:follow = sports 从集合中随机弹出一个元素
-  smembers小心使用，返回无序的

- sinter:交集

- sdiff差集

- sunion并集

#### Tips

Sadd = Tagging

Spop/SRandmember = Random item

Sadd + Sinter = Social Graph

### 有序集合









