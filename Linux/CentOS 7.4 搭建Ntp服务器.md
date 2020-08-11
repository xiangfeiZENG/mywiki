# CentOS 7.4 搭建Ntp服务器

采用10.248.63.38为ntp服务器，10.248.63.39,10.248.63.55为客户端

10.248.63.38:

```
[root@mvne-mysql ~]# yum -y install ntp
[root@mvne-mysql ~]# vim /etc/ntp.conf 

# Permit time synchronization with our time source, but do not
# permit the source to query or modify the service on this system.
#restrict default nomodify notrap nopeer noquery
restrict 10.248.63.38 nomodify notrap nopeer noquery


# Hosts on local network are less restricted.
#restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap
restrict 10.258.63.0 mask 255.255.255.0 nomodify notrap


# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
# 如果网络时间同步服务器不可以用，就以本地时间为标准想客户端提供服务
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst
server 127.127.1.0
fudge 127.127.1.0 stratum 10
```

配置解释：
权限的设定主要以 restrict 这个参数来设定，主要的语法为： 
restrict IP地址 mask 子网掩码 参数 
其中 IP 可以是IP地址，也可以是 default ，default 就是指所有的IP 
参数有以下几个： 
ignore　：关闭所有的 NTP 联机服务 
nomodify：客户端不能更改服务端的时间参数，但是客户端可以通过服务端进行网络校时。 
notrust ：客户端除非通过认证，否则该客户端来源将被视为不信任子网 
noquery ：不提供客户端的时间查询 



10.248.63.39,10.248.63.55客户端配置

```
# Permit time synchronization with our time source, but do not
# permit the source to query or modify the service on this system.
#restrict default nomodify notrap nopeer noquery
restrict 10.248.63.39 nomodify notrap nopeer noquery

# Hosts on local network are less restricted.
#restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap
restrict 192.168.63.0 mask 255.255.255.0 nomodify notrap

# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst
server 10.248.63.38
fudge 10.248.63.38 stratum 10
```

启停

```
systemctl start ntpd
systemctl enable ntpd
```

10.248.63.38查看状态：

```
[root@mvne-mysql ~]# ntpstat
synchronised to local net at stratum 11 
   time correct to within 12 ms
   polling server every 64 s
```

10.248.63.39查看状态：

```
[root@mvne-redis ~]# ntpstat
synchronised to NTP server (10.248.63.38) at stratum 12 
   time correct to within 19 ms
   polling server every 256 s
```



