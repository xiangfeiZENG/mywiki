# Redis安装

单机版本redis安装过程

```
# 准备安装包：
wget http://download.redis.io/releases/redis-4.0.11.tar.gz
# 准备编译环境
[root@xfei-server redis]# yum install gcc-c++
# 解压、编译
tar -xvf redis-4.0.11.tar.gz
cd redis-4.0.11
make
# 安装
[root@xfei-server redis-4.0.11]# make install PREFIX=/usr/local/redis
[root@xfei-server redis]# cp ~/software/redis/redis-4.0.11/redis.conf /usr/local/redis/
# 修改配置文件
[root@xfei-server redis]# vim /usr/local/redis/redis.conf 
daemonize yes
# 启动
[root@xfei-server bin]# ./redis-server ../redis.conf 

# 修改防火墙策略
[root@xfei-server bin]# firewall-cmd --add-port=6379/tcp --permanent
[root@xfei-server bin]# firewall-cmd --reload


```



