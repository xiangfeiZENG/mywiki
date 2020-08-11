# CentOS 7.4 防火墙相关



CentOS防火墙服务为firewalld

常用命令

```
# 查看防火墙状态
[root@iz2zeh073jqg8w4d7cceekz ~]# systemctl status firewalld
# 启动防火墙
[root@iz2zeh073jqg8w4d7cceekz ~]# systemctl start firewalld
# 设置开启启动
[root@iz2zeh073jqg8w4d7cceekz lib]# systemctl enable firewalld
Created symlink from /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service to /usr/lib/systemd/system/firewalld.service.
Created symlink from /etc/systemd/system/multi-user.target.wants/firewalld.service to /usr/lib/systemd/system/firewalld.service.

# 查看防火墙情况
[root@iz2zeh073jqg8w4d7cceekz ~]# firewall-cmd --list-all
public
  target: default
  icmp-block-inversion: no
  interfaces: 
  sources: 
  services: ssh dhcpv6-client
  ports: 
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
  
# 命令方式添加端口
[root@iz2zeh073jqg8w4d7cceekz ~]# firewall-cmd --add-port=8080/tcp --permanent
1、firewall-cmd：是Linux提供的操作firewall的一个工具；
2、--permanent：表示设置为持久；
3、--add-port：标识添加的端口；
4、--zone=public firewall中有Zone的概念，可以将具体的端口制定到具体的zone配置文件中。
5、--add-service=service 添加某个服务

```





配置文件

系统服务(常用软件）配置文件：

```
[root@iz2zeh073jqg8w4d7cceekz services]# ls /usr/lib/firewalld/services/
```

用户配置目录：

```
[root@iz2zeh073jqg8w4d7cceekz firewalld]# ls /etc/firewalld/
firewalld.conf  helpers  icmptypes  ipsets  lockdown-whitelist.xml  services  zones
```

