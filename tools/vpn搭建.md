# Vpn 搭建



1. 防火墙设置  ，端口是ssh端口。

```
[root@ference ~]# systemctl start firewalld
[root /]# firewall-cmd --add-port=26144/tcp --permanent
[root /]# firewall-cmd --add-port=26144/udp --permanent
[root /]# firewall-cmd --reload

[root@ference ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources: 
  services: dhcpv6-client ssh
  ports: 26144/udp 26144/tcp
  protocols: 
  masquerade: no
  forward-ports: 
  sourceports: 
  icmp-blocks: 
  rich rules: 
```





2. 搭建ssserver服务器

   ```
   yum install python-setuptools 
   easy_install pip
   pip install shadowsocks
   ```

   

3. 编写配置文件

   ```
   [root@ference ~]# cat /etc/shadowsocks.json 
   {
       "server":"173.242.122.**",
       "server_port":AAAA,
       "local_address":"127.0.0.1",
       "local_port":AAAA,
       "password":"******",
       "timeout":600,
       "method":"aes-256-cfb",
       "auth":true
   }
   ```

   

4. 添加防火墙

   ```
   [root@ference ~]# firewall-cmd --add-port AAAA/tcp --permanent
   success
   [root@ference ~]# firewall-cmd --add-port AAAA/udp --permanent
   success
   [root@ference ~]# firewall-cmd --reload
   success
   ```

   

5. 启动

   ```
   [root@ference ~]# /usr/bin/ssserver -c /etc/shadowsocks.json -d start
   INFO: loading config from /etc/shadowsocks.json
   2019-04-10 04:42:10 INFO     loading libcrypto from libcrypto.so.10
   started
   ```

   

6. 客户端配置

   下载Shadowsocks，并按配置文件完成配置即可配置

   