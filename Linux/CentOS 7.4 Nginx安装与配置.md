# CentOS 7.4 Nginx安装与配置

nginx是一款轻量级Web服务器，也是一款反向代理服务器。

用途：

1. 可直接支持Rails和PHP的程序
2. 可作为HTTP反向代理服务器
3. 作为负载均衡服务器
4. 作为邮件代理服务器
5. 帮助实现前段动静分离

特点：高稳定、高性能、资源占用少、功能丰富、模块化的结构、支持热部署



环境准备：

```
# 安装gcc
yum install gcc-c++
# 安装pcre、pcre-devel
yum install -y pcre pcre-devel
# 安装zlib
yum install -y zlib zlib-devel
# 安装openssl
yum install -y openssl openssl-devel
```



安装过程：

```
# 下载地址：http://nginx.org/en/download.html
[root@xfei-server nginx]# wget http://nginx.org/download/nginx-1.16.1.tar.gz

# 配置
[root@xfei-server nginx-1.16.1]# ./configure --prefix=/usr/local/nginx

可参照配置如下：
./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi

# 编译及安装
[root@xfei-server nginx-1.16.1]# make
[root@xfei-server nginx-1.16.1]# make install

# 启动
[root@xfei-server nginx]# cd /usr/local/nginx/sbin/
[root@xfei-server sbin]# ./nginx 
# 停止
[root@xfei-server sbin]# ./nginx -s stop

# 开启防火墙
[root@xfei-server conf]# firewall-cmd --add-port=80/tcp --permanent
success
[root@xfei-server conf]# firewall-cmd --reload
success
```

