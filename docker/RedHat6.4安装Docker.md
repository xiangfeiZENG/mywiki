# RedHat6.4安装Docker

标签（空格分隔）： Docker

---

步骤：
1.安装epel
```
rpm -Uvh http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm 
```
2.安装docker
```
[root@localhost ~]# yum -y install docker-io
```

3.启动docker
```
[root@localhost ~]# service docker start
Starting docker:        [确定]
```

4.设置docker开机启动
```
[root@localhost ~]# service docker enable
用法：/etc/init.d/docker {start|stop|status|restart|condrestart|try-restart|reload|force-reload}
```

5.启动报错如下
```
[root@localhost ~]# docker -d
WARN[0000] You are running linux kernel version 2.6.32-358.el6.x86_64, which might be unstable running docker. Please upgrade your kernel to 3.10.0. 
INFO[0000] Listening for HTTP on unix (/var/run/docker.sock) 
docker: relocation error: docker: symbol dm_task_get_info_with_deferred_remove, version Base not defined in file libdevmapper.so.1.02 with link time reference
```
内核需要升级，升级方法：






