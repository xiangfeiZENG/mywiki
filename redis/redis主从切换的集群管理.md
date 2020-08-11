# redis主从切换的集群管理

标签（空格分隔）： Redis

[TOC]

---

搭建了Redis集群，并实现了主从自动切换。

此处使用了三台主机：
192.168.77.31；192.168.77.34；192.168.77.36；



## Redis 安装

下载Redis源码，此处使用的版本是2.8.17。

创建redis运行目录：
/app/airedis
/app/airedis/bin
/app/airedis/config
/app/airedis/log

安装Redis:
```shell
[airedis@gboss05 ~]$ tar -xvf redis-2.8.17.tar.gz
[airedis@gboss05 ~]$ cd redis-2.8.17
[airedis@gboss05 redis-2.8.17]$ make CFLAGS="-march=i686" //后面的参数是32位系统所需要的
```

安装成功后将redis-server、redis-cli、redis-sentinel、redis-check-aof、redis-benchmark拷贝至bin目录下。

将redis安装目录下的redis.conf拷贝至/app/airds/config目录下。















