# VMBox桥接网络设置

标签（空格分隔）： Linux

---

1. vmbox选择桥接方式，注意选择正确的桥接网卡。
2. 查看宿主机器的ip，gateway和dns。
3. 按如下配置。
```
DEVICE=eth0
HWADDR=08:00:27:1E:11:1B
TYPE=Ethernet
UUID=a244ba24-b31a-4b3c-b89e-d02a82c25838
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
#BOOTPROTO=dhcp

IPADDR=192.168.79.252
NETMASK=255.255.255.0
GATEWAY=192.168.64.254
DNS1=192.168.2.2
```




