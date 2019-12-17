# VMware中RedHat设置静态IP,解决克隆后网卡启动失败问题

标签（空格分隔）： Linux

[TOC]

---

由于学习和测试需要，在Vm中搭建了多台虚拟机，但是vm中设置的是DHCP,每次重启后ip都会变，通过设置静态IP解决。

---

## 设置静态IP

### 首先关闭VMware中的DHCP
Edit->Virtual Network Editor  
选择VMnet8，去掉Use local DHCP service to distribute IP address to VMs选项。点击NAT Settings查看一下GATEWAY地址：192.168.91.2

### 配置RedHat的静态ip
涉及到三个配置文件：
> /etc/sysconfig/network 
> /etc/sysconfig/network-scripts/ifcfg-eth0 
> /etc/resolv.conf

首先修改/etc/sysconfig/network如下：
```
NETWORKING=yes 
HOSTNAME=localhost.localdomain 
GATEWAY=192.168.91.2
```
指定网关地址：
然后修改/etc/sysconfig/network-scripts/ifcfg-eth0：
```
DEVICE="eth0"
#BOOTPROTO="dhcp"
BOOTPROTO="static" IPADDR=192.168.91.100
NETMASK=255.255.255.0
HWADDR="00:0C:29:56:8F:AD"
IPV6INIT="no"
NM_CONTROLLED="yes"
ONBOOT="yes"
TYPE="Ethernet"
UUID="ba48a4c0-f33d-4e05-98bd-248b01691c20"
DNS1=192.168.91.2
```
注意：这里DNS1是必须要设置的否则无法进行域名解析。

最后配置/etc/resolv.conf。（这里其实不需要修改，上面的dns设置后系统会自动加载这个配置文件）
```
nameserver 192.168.91.2
```

## 解决克隆后网卡启动失败问题
在设置完静态ip后，对虚拟机进行克隆，发现克隆后虚拟网卡启不来，具体报错如下：
> Bringing up interface eth0:  Error: No suitable device found: no device found for connection 'System eth0'.  

原因：虚拟机分配给操作系统的虚拟网卡MAC地址是不一样的。第一个系统的网卡MAC地址记录在了`/etc/udev/rules.d/70-persistent-net.rules`，命名为`eth0`。新克隆分配的系统的网卡MAC地址也记录在了该文件当中，与之前的系统一样，因此有了冲突。

问题解决方法：

* 删除克隆后这个系统中的`/etc/udev/rules.d/70-persistent-net.rules`文件，重启后系统会重新生成一个新的虚拟网卡MAC地址，然后把这个文件里`NAME="eth1"`的eth1改成eth0
* 同时也要修改`/etc/sysconfig /network-scripts/ifcfg-eth0`里的mac地址和`ATTR{address}`文件mac地址，并与新的虚拟网卡MAC地址保持一致。
如:将原文件的
```
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="00:0c:29:f4:95:e3", ATTR{type}=="1", KERNEL=="eth*", NAME="eth1"

改为 SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="00:0c:29:5c:65:b7", ATTR{type}=="1", KERNEL=="eth*", NAME="eth0"
```
重启问题解决：`service network restart`

另外，在安装一个64位linux时出现以下问题：
```
Bringing up interface eth0: ERROR: Unknown connection: 0d23...(uuid).
```
将UUID那一行注释掉即解决。

另外每次启动ech0不起效果。
在：`/etc/rc.d/rc.local/`中添加：
```
service network restart
```
在系统启动的时候重启下网卡。




