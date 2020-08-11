## linux防火墙相关

### 1 关闭及开放防火墙

#### 1.1 centos6

关闭防火墙

```
//临时关闭
service iptables stop
//禁止开机启动
chkconfig iptables off
```

#### 1.2 centos7

关闭防火墙

CentOS7版本后防火墙默认使用firewalld，因此在CentOS7中关闭防火墙使用以下命令， systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。

```
//临时关闭
systemctl stop firewalld
//禁止开机启动
[root@localhost ~]# systemctl disable firewalld.service
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.

启动一个服务：systemctl start firewalld.service
关闭一个服务：systemctl stop firewalld.service
重启一个服务：systemctl restart firewalld.service
显示一个服务的状态：systemctl status firewalld.service
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service
查看已启动的服务列表：systemctl list-unit-files|grep enabled
查看启动失败的服务列表：systemctl --failed


//查看帮助： 
firewall-cmd --help
//显示状态
firewall-cmd --state
//永久开放某个端口
firewall-cmd --zone=public --add-port=9421/tcp --permanent
//重新载入
firewall-cmd --reload
//查看
firewall-cmd --zone= public --query-port=80/tcp
//删除
firewall-cmd --zone= public --remove-port=80/tcp --permanent
//查看所有打开的端口 
firewall-cmd --zone=public --list-ports
```



