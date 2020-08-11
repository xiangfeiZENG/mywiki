# MySQL57 安装过程

## 安装过程

Install MySQL by the following command (for Fedora, replace yum in the command with dnf):

```
shell> sudo yum install mysql-community-server
```

This installs the package for MySQL server (mysql-community-server) and also packages for
the components required to run the server, including packages for the client (mysql-communityclient), the common error messages and character sets for client and server (mysql-communitycommon), and the shared client libraries (mysql-community-libs).  



mysql57 关联包：

```
[root@xfei-server software]# ls mysql57/
libaio-0.3.109-13.el7.x86_64.rpm                mysql-community-libs-5.7.29-1.el7.x86_64.rpm         postfix-2.10.1-7.el7.x86_64.rpm
mysql-community-client-5.7.29-1.el7.x86_64.rpm  mysql-community-libs-compat-5.7.29-1.el7.x86_64.rpm
mysql-community-common-5.7.29-1.el7.x86_64.rpm  mysql-community-server-5.7.29-1.el7.x86_64.rpm
```

安装步骤

```
rpm -ivh libaio-0.3.109-13.el7.x86_64.rpm 
rpm -ivh postfix-2.10.1-7.el7.x86_64.rpm 
yum install mysql-community-*.rpm

Dependencies Resolved

=============================================================================================================================================================
 Package                                  Arch                Version                    Repository                                                     Size
=============================================================================================================================================================
Installing:
 mysql-community-client                   x86_64              5.7.29-1.el7               /mysql-community-client-5.7.29-1.el7.x86_64                   103 M
 mysql-community-common                   x86_64              5.7.29-1.el7               /mysql-community-common-5.7.29-1.el7.x86_64                   2.8 M
 mysql-community-libs                     x86_64              5.7.29-1.el7               /mysql-community-libs-5.7.29-1.el7.x86_64                     9.7 M
     replacing  mariadb-libs.x86_64 1:5.5.56-2.el7
 mysql-community-libs-compat              x86_64              5.7.29-1.el7               /mysql-community-libs-compat-5.7.29-1.el7.x86_64              6.2 M
     replacing  mariadb-libs.x86_64 1:5.5.56-2.el7
 mysql-community-server                   x86_64              5.7.29-1.el7               /mysql-community-server-5.7.29-1.el7.x86_64                   765 M

Transaction Summary
```



## 启动和管理服务

```
# 查看状态
[root@xfei-server mysql57]# systemctl status mysqld
# 启动服务
[root@xfei-server mysql57]# systemctl start mysqld
# 开机自启动
[root@xfei-server mysql57]# systemctl enable mysqld
# 查看临时root密码
[root@xfei-server mysql57]# grep 'temporary password' /var/log/mysqld.log
2020-02-09T13:51:20.564775Z 1 [Note] A temporary password is generated for root@localhost: noQVqBYdx7_i
# 修改root密码
[root@xfei-server mysql57]# mysql -uroot -p
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';

# 添加防火墙策略
[root@xfei-server software]# firewall-cmd --add-service=mysql --permanent
[root@xfei-server software]# firewall-cmd --reload

```



## 远程root访问

If the following error occurs when you try to connect from a host other than the one on which the MySQL
server is running, it means that there is no row in the user table with a Host value that matches the
client host:  

```
Host ... is not allowed to connect to this MySQL server
```

If you do not know the IP address or host name of the machine from which you are connecting, you
should put a row with '%' as the Host column value in the user table. After trying to connect from the
client machine, use a SELECT USER() query to see how you really did connect. Then change the '%'
in the user table row to the actual host name that shows up in the log. Otherwise, your system is left
insecure because it permits connections from any host for the given user name.  

设置过程：

```
mysql> select user,host from user;
+---------------+-----------+
| user          | host      |
+---------------+-----------+
| mysql.session | localhost |
| mysql.sys     | localhost |
| root          | localhost |
+---------------+-----------+
3 rows in set (0.00 sec)

mysql> select user();
+----------------+
| user()         |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)


mysql> update user set host='%' where user='root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```



## 创建数据库和数据库用户

创建数据库和用户：

```
# 创建数据库
CREATE DATABASE IF NOT EXISTS mvne_crm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
# 创建用户
CREATE USER IF NOT EXISTS crm_user IDENTIFIED BY '1qaz@WSX';
# 为用户赋权
mysql> GRANT ALL ON mvne_crm.* TO 'crm_user'@'%';
# 刷新
mysql> flush privileges;
```

