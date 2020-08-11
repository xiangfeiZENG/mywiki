## Centos7.x下mongodb安装

#### 参考手册

英文安装手册见：

https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/

在线安装直接使用rpm安装，本文主要是离线安装。

#### 下载tar.gz包

下载地址：https://www.mongodb.com/download-center/community?jmp=docs

#### 安装过程

解压文件：

```shell
cd /opt/mcb/
tar -zxvf mongodb-linux-*-4.0.5.tgz

mcbadm@xnhsh-test04(mvne_portal):/opt/mcb/mongodb>ls
bin  conf  data  GNU-AGPL-3.0  LICENSE-Community.txt  log  mongod.pid  MPL-2  README  THIRD-PARTY-NOTICES
mcbadm@xnhsh-test04(mvne_portal):/opt/mcb/mongodb>pwd
/opt/mcb/mongodb
```

配置conf配置文件：

```shell
# mongod.conf

# for documentation of all options,see:
# http://docs.mongodb.org/manual/reference/configuration-options/

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /opt/mcb/mongodb/log/mongod.log

# where and how to store data.
storage:
  dbPath: /opt/mcb/mongodb/data/db
  journal:
    enabled: true

# how the process runs
processManagement:
  fork: true
  pidFilePath: /opt/mcb/mongodb/mongod.pid

# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1,192.168.127.44
```

此时采用配置文件启动：

```shell
/opt/mcb/mongodb/bin/mongod -f /opt/mcb/mongodb/conf/mongod.conf
```

#### 配置为系统服务

配置过程：

```shell
mcbadm@xnhsh-test04(mvne_portal):/opt/mcb/mongodb>cat /usr/lib/systemd/system/mongod.service 
[Install]
[Unit]
Description=mongodb service add by xfei
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
User=mcbadm
Group=mcbadm
ExecStart=/opt/mcb/mongodb/bin/mongod -f /opt/mcb/mongodb/conf/mongod.conf               
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
```

设置开机启动：

```shell
mcbadm@xnhsh-test04(mvne_portal):/opt/mcb/mongodb>sudo systemctl enable mongod.service 
Created symlink from /etc/systemd/system/multi-user.target.wants/mongod.service to /usr/lib/systemd/system/mongod.service.
```

启动：

```shell
mcbadm@xnhsh-test04(mvne_portal):/opt/mcb/mongodb>sudo systemctl start mongod.service
```

#### 防火墙及selinux配置

参考英文安装手册。