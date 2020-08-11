# Redis Cluster 安装

[TOC]

Redis Cluster安装有两种方式，原生安装和`edis-trib.rb`方式安装。



## 原生安装

了解命令方式可以了解redis集群的原理。

1. 节点配置

   ```
   [xfei-centos@xfei-centos conf]$ cat redis-7000.conf 
   daemonize yes
   pidfile /var/run/redis.pid
   port 7000
   tcp-backlog 511
   timeout 0
   tcp-keepalive 0
   loglevel notice
   logfile "7000.log"
   databases 16
   save 900 1
   save 300 10
   save 60 10000
   stop-writes-on-bgsave-error yes
   rdbcompression yes
   rdbchecksum yes
   dbfilename dump-7000.rdb
   dir "/usr/local/redis/data"
   slave-serve-stale-data yes
   slave-read-only yes
   repl-diskless-sync no
   repl-diskless-sync-delay 5
   repl-disable-tcp-nodelay no
   slave-priority 100
   appendonly no
   appendfilename "appendonly.aof"
   appendfsync everysec
   no-appendfsync-on-rewrite no
   auto-aof-rewrite-percentage 100
   auto-aof-rewrite-min-size 64mb
   aof-load-truncated yes
   lua-time-limit 5000
   slowlog-log-slower-than 10000
   slowlog-max-len 128
   latency-monitor-threshold 0
   notify-keyspace-events ""
   hash-max-ziplist-entries 512
   hash-max-ziplist-value 64
   list-max-ziplist-entries 512
   list-max-ziplist-value 64
   set-max-intset-entries 512
   zset-max-ziplist-entries 128
   zset-max-ziplist-value 64
   hll-sparse-max-bytes 3000
   activerehashing yes
   client-output-buffer-limit normal 0 0 0
   client-output-buffer-limit slave 256mb 64mb 60
   client-output-buffer-limit pubsub 32mb 8mb 60
   hz 10
   aof-rewrite-incremental-fsync yes
   
   # cluster 
   cluster-enabled yes
   cluster-config-file nodes-7000.conf
   ```

   通过命令配置其余节点：

   ```
   sed 's/7000/7001/g' redis-7000.conf > redis-7001.conf
   sed 's/7000/7002/g' redis-7000.conf > redis-7002.conf
   sed 's/7000/7003/g' redis-7000.conf > redis-7003.conf
   sed 's/7000/7004/g' redis-7000.conf > redis-7004.conf
   sed 's/7000/7005/g' redis-7000.conf > redis-7005.conf
   ```

   

2. 启动节点

   ```
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7000.conf
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7001.conf 
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7002.conf 
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7003.conf 
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7004.conf 
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7005.conf 
   ```

   查看状态：

   ```
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7000 cluster info
   cluster_state:fail
   cluster_slots_assigned:16384
   cluster_slots_ok:5462
   cluster_slots_pfail:10922
   cluster_slots_fail:0
   cluster_known_nodes:6
   cluster_size:3
   cluster_current_epoch:5
   cluster_my_epoch:2
   cluster_stats_messages_sent:2445
   cluster_stats_messages_received:0
   ```

   

3. 节点握手

   ```
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7000 cluster meet 127.0.0.1 7001
   OK
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7000 cluster info
   cluster_state:ok
   cluster_slots_assigned:16384
   cluster_slots_ok:16384
   cluster_slots_pfail:0
   cluster_slots_fail:0
   cluster_known_nodes:6
   cluster_size:3
   cluster_current_epoch:5
   cluster_my_epoch:2
   cluster_stats_messages_sent:8348
   cluster_stats_messages_received:488
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7001 cluster info
   cluster_state:ok
   cluster_slots_assigned:16384
   cluster_slots_ok:16384
   cluster_slots_pfail:0
   cluster_slots_fail:0
   cluster_known_nodes:6
   cluster_size:3
   cluster_current_epoch:5
   cluster_my_epoch:1
   cluster_stats_messages_sent:909
   cluster_stats_messages_received:509
   ```

   握手其他资源

   ```
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7000 cluster meet 127.0.0.1 7002
   OK
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7000 cluster meet 127.0.0.1 7003
   OK
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7000 cluster meet 127.0.0.1 7004
   OK
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7000 cluster meet 127.0.0.1 7005
   ```

   

4. 指定槽

   ```
   [xfei-centos@xfei-centos bin]$ ./redis-cli -h 127.0.0.1 -p 7000 cluster addslots {0..5461}
   [xfei-centos@xfei-centos bin]$ ./redis-cli -h 127.0.0.1 -p 7001 cluster addslots {5462..10922}
   [xfei-centos@xfei-centos bin]$ ./redis-cli -h 127.0.0.1 -p 7000 cluster addslots {10923..16383}
   ```

   

5. 主从分配

   ```
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7003 cluster replicate 9f3755cb8c7e6467e711089830d0cd7963b1409e
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7004 cluster replicate 288a09821c1a8876027bc218ac4ea52b14e9dbb9
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7005 cluster replicate d9dd7eb791f153474571a682cc5ee451230a6639
   ```

   

6. 显示结果

   ```
   [xfei-centos@xfei-centos bin]$ ./redis-cli -p 7000 cluster nodes
   49b528eb00aac4fbdc417b73e945598a61fb21db 127.0.0.1:7003 slave 9f3755cb8c7e6467e711089830d0cd7963b1409e 0 1565923810712 4 connected
   288a09821c1a8876027bc218ac4ea52b14e9dbb9 127.0.0.1:7001 master - 0 1565923812737 1 connected 5462-10922
   d9dd7eb791f153474571a682cc5ee451230a6639 127.0.0.1:7002 master - 0 1565923811724 0 connected 10923-16383
   71c1dc8b4e2c455f5cbe6b1043f66b7213ee6e03 127.0.0.1:7005 slave d9dd7eb791f153474571a682cc5ee451230a6639 0 1565923813749 5 connected
   9f3755cb8c7e6467e711089830d0cd7963b1409e 127.0.0.1:7000 myself,master - 0 0 2 connected 0-5461
   4bcbd1d505fe2087f8dd5380474753e44060aeca 127.0.0.1:7004 slave 288a09821c1a8876027bc218ac4ea52b14e9dbb9 0 1565923806658 3 connected
   ```

7. 验证结果

   ```
   [xfei-centos@xfei-centos bin]$ ./redis-cli -c -p 7000
   127.0.0.1:7000> set hello world
   OK
   127.0.0.1:7000> get hello
   "world"
   ```

   

## 官方手动安装方式

1. 节点配置如上。

2. 启动节点

   ```
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7000.conf 
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7001.conf 
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7002.conf 
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7003.conf 
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7004.conf 
   [xfei-centos@xfei-centos bin]$ sudo ./redis-server ../conf/redis-7005.conf 
   ```

3. 执行安装

   To create your cluster for Redis 5 with `redis-cli` simply type:

   ```
   redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 \
   127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 \
   --cluster-replicas 1
   ```

   Using `redis-trib.rb` for Redis 4 or 3 type:

   ```
   ./redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 \
   127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005
   ```

   执行具体命令：

   ```
   [xfei-centos@xfei-centos bin]$ sudo ./redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005
   /usr/bin/env: ruby: 没有那个文件或目录
   
   因为redis-trib.rb依赖ruby环境
   需要安装ruby环境
   [xfei-centos@xfei-centos bin]$ sudo yum install ruby
   
   再次报错
   [xfei-centos@xfei-centos bin]$ sudo ./redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005
   /usr/share/rubygems/rubygems/core_ext/kernel_require.rb:55:in `require': cannot load such file -- redis (LoadError)
           from /usr/share/rubygems/rubygems/core_ext/kernel_require.rb:55:in `require'
           from ./redis-trib.rb:25:in `<main>'
           
   原因是：没装redis的第三方接口！既然ruby程序要访问redis数据库，总得有个连接接口。对于ruby这么安装client包就行了
   这里需要通过gem install redis 才行。
   [xfei-centos@xfei-centos bin]$ sudo gem install redis
   
   再次报错：
   [xfei-centos@xfei-centos bin]$ sudo gem install redis
   Fetching: redis-4.1.2.gem (100%)
   ERROR:  Error installing redis:
           redis requires Ruby version >= 2.3.0.
   原因是使用的centos默认使用2.2.0
   
   这里不建议使用sudo去执行。环境变量会有问题。
   ```

   