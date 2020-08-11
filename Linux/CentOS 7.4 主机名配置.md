# CentOS 7.4 主机名配置

主机名：

静态主机名（Static），系统启动时从`/etc/hostname`自动初始化主机名。

灵活主机名（Pretty），允许使用自由形式（包括特殊/空白字符）的主机名，以展示给终端用户。

瞬态主机名（transient），是在系统运行时临时分配的主机名，例如，通过DHCP或mDNS服务器分配。



一旦修改了静态主机名，/etc/hostname将自动更新。然而，/etc/hosts/不会保存所做修改，所以需要在更新主机名后手动保存/etc/hosts，之后重启系统。



修改过程：

```
# 查看主机名设置状态
[root@iz2zeh073jqg8w4d7cceekz ~]# hostnamectl status
   Static hostname: iz2zeh073jqg8w4d7cceekz
   Pretty hostname: iZ2zeh073jqg8w4d7cceekZ
         Icon name: computer-vm
           Chassis: vm
        Machine ID: f0f31005fb5a436d88e3c6cbf54e25aa
           Boot ID: 409b868d5c5d4e8193dabce2e16b944a
    Virtualization: kvm
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-693.2.2.el7.x86_64
      Architecture: x86-64
      
 
[root@iz2zeh073jqg8w4d7cceekz ~]# hostnamectl set-hostname xfei-server --static
[root@iz2zeh073jqg8w4d7cceekz ~]# hostname
xfei-server
[root@iz2zeh073jqg8w4d7cceekz ~]# cat /etc/hostname
xfei-server
[root@iz2zeh073jqg8w4d7cceekz ~]# cat /etc/sysconfig/network
# Created by anaconda
NETWORKING_IPV6=no
PEERNTP=no
[root@iz2zeh073jqg8w4d7cceekz ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

# 修改/etc/hosts
[root@iz2zeh073jqg8w4d7cceekz ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
127.0.0.1   xfei-server

# 重启系统
[root@iz2zeh073jqg8w4d7cceekz ~]# reboot

```



需要更改配置文件生效，修`/etc/sysconfig/network`里的 HOSTNAME=主机名(可自定义)，重启生效。

