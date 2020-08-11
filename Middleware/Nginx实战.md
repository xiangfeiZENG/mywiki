# Nginx实战

[TOC]



安装必要依赖

```
sudo yum -y install gcc gcc-c++ autoconf pcre pcre-devel make automake

可选安装一些工具包：
sudo yum -y install wget httpd-tools vim
```





## Nginx基础

### 简述

Nginx是一个开源且高性能、可靠的HTTP中间件、代理服务。

常见的HTTP服务

- HTTPD-Apache基金会
- IIS-微软
- GWS-Google

### 为什么选择Nginx

#### IO多路复用epoll

![image-20191116214027505](/Users/zengxiangfei/Documents/mywiki/netty/images/image-20191116214027505.png)

![image-20191116214359434](/Users/zengxiangfei/Documents/mywiki/netty/images/image-20191116214359434.png)

多个描述符的I/O操作都能在一个线程内并发交替的顺序完成，这就叫I/O多路复用，这里的"复用"指的是复用同一个线程。

- 什么是epoll

  IO多路复用的实现方式select、poll、epoll

  - 什么是select

    ![image-20191116215303155](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191116215303155.png)

    缺点：

    1. 能够监视文件描述符的数量存在最大限制
    2. 线性扫描效率低下

  - epoll模型
    1. 每当FD就绪，采用系统的回调函数直接将fd放入，效率更高。
    2. 最大连接无限制

#### 轻量级

- 功能模块少
- 代码模块化



#### CPU亲和（affinity）

为什么需要CPU亲和

![image-20191116220921394](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191116220921394.png)

是一种把CPU核心和Nginx工作进程**绑定**方式，把每个worker进程固定在一个cpu上执行，减少切换cpu的cache miss，获得更好的性能。



#### Sendfile

![image-20191116221148430](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191116221148430.png)

![image-20191116221243879](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191116221243879.png)





### 快速安装nginx

安装：

`sudo yum install nginx --downloadonly --downloaddir=/opt/download` 

`rpm -Uvh nginx-1.16.1-1.el7.ngx.x86_64.rpm` 



安装目录：

```
[xfei-centos@xfei-centos download]$ rpm -ql nginx
-- 配置文件 Nginx日志轮转，用于logratate服务日志切割
/etc/logrotate.d/nginx   
-- 目录、配置文件 ， Nginx主配置文件
/etc/nginx
/etc/nginx/conf.d
/etc/nginx/nginx.conf
/etc/nginx/conf.d/default.conf   
-- 配置文件 cgi配置相关，fastcgi配置
/etc/nginx/fastcgi_params
/etc/nginx/scgi_params
/etc/nginx/uwsgi_params   
-- 配置文件，编码转换映射转化文件
/etc/nginx/koi-utf
/etc/nginx/koi-win
/etc/nginx/win-utf    
--配置文件，设置http协议的Conten-Type与扩展名对应关系
/etc/nginx/mime.types  
-- 目录，nginx模块目录
/etc/nginx/modules
/usr/lib64/nginx/modules
-- 配置文件，用于配置出系统守护进程管理器管理方式
/etc/sysconfig/nginx
/etc/sysconfig/nginx-debug
/usr/lib/systemd/system/nginx-debug.service
/usr/lib/systemd/system/nginx.service 

/usr/lib64/nginx
/usr/libexec/initscripts/legacy-actions/nginx
/usr/libexec/initscripts/legacy-actions/nginx/check-reload
/usr/libexec/initscripts/legacy-actions/nginx/upgrade
-- 命令 Nginx服务的启动管理的终端命令
/usr/sbin/nginx
/usr/sbin/nginx-debug
-- 文件、目录，Nginx的手册和帮助稳定
/usr/share/doc/nginx-1.16.1
/usr/share/doc/nginx-1.16.1/COPYRIGHT
/usr/share/man/man8/nginx.8.gz

/usr/share/nginx
/usr/share/nginx/html
/usr/share/nginx/html/50x.html
/usr/share/nginx/html/index.html
-- 目录，Nginx的缓存目录
/var/cache/nginx
-- 目录，Nginx的日志目录
/var/log/nginx
```



编译参数

```
[xfei-centos@xfei-centos ~]$ nginx -V
nginx version: nginx/1.16.1
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC) 
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie'
[xfei-centos@xfei-centos ~]$ 
```

```

```



![image-20191117210149727](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191117210149727.png)

![image-20191117210233607](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191117210233607.png)

![image-20191117210308721](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191117210308721.png)

![image-20191117210408035](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191117210408035.png)

![image-20191117210501093](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191117210501093.png)



nginx基本配置语法

```
[xfei-centos@xfei-centos ~]$ vim /etc/nginx/nginx.conf 


user  nginx;   -- 设置nginx服务的系统使用用户
worker_processes  1;  -- 工作进程数，一般和cpu的核心数保持一致

error_log  /var/log/nginx/error.log warn;  -- nginx的错误日志
pid        /var/run/nginx.pid;  -- nginx服务启动时候pid


events {
    worker_connections  1024;  -- 每个进程允许最大连接数，可以调到65535，一般调节到1w左右
    use                        -- 工作进程数，定义使用的内核模型（epoll？）
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}



[xfei-centos@xfei-centos conf.d]$ cat default.conf 
server {
    listen       80;
    server_name  localhost;   -- 如有有域名，这里填入域名

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

```





HTTP请求

`curl -v http://www.imooc.com >/dev/null`

```
[xfei-centos@xfei-centos ~]$ curl -v www.imooc.com >/dev/null
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* About to connect() to www.imooc.com port 80 (#0)
*   Trying 115.182.41.180...
* Connected to www.imooc.com (115.182.41.180) port 80 (#0)
> GET / HTTP/1.1
> User-Agent: curl/7.29.0
> Host: www.imooc.com
> Accept: */*
> 
< HTTP/1.1 301 Moved Permanently
< Server: nginx
< Date: Mon, 18 Nov 2019 14:14:09 GMT
< Content-Type: text/html
< Content-Length: 178
< Connection: keep-alive
< Location: https://www.imooc.com
< 
{ [data not shown]
100   178  100   178    0     0    846      0 --:--:-- --:--:-- --:--:--   851
* Connection #0 to host www.imooc.com left intact
```



Nginx日志

包括：error.log access_log

```
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
                      
   
```

Nginx变量：

![image-20191118223553760](/Users/zengxiangfei/Documents/mywiki/nginx/images/image-20191118223553760.png)



Nginx模块

nginx官方模块

stub_status

编译选项: `—with-http_stub_status_module`

主要用于展示nginx当前处理连接的一个状态，Nginx的客户端状态

http_stub_status_module配置方法

```
Syntax: stub_status;
Default: --
Context: server,location

具体配置：
    location /mystatus {
        stub_status;
    }

展示：
Active connections: 2 
server accepts handled requests
 2 2 1   -- nginx接收的握手的次数 处理的连接数 宗的请求数
Reading: 0 Writing: 1 Waiting: 1 
```



random_index_module

```
--with-http_random_index_module
目录中选择一个随机主页
```



ngx_http_sub_module

HTTP内容替换

```
Syntax:	sub_filter string replacement;
Default:	—
Context:	http, server, location

location / {
    sub_filter '<a href="http://127.0.0.1:8080/'  '<a href="https://$host/';
    sub_filter '<img src="http://127.0.0.1:8080/' '<img src="https://$host/';
    sub_filter_once on;
}
```



nginx第三方模块





## Nginx架构

### 常见问题





### Nginx中间件性能优化







### Nginx与安全







### 新版本特性





### 中间件架构设计















## Nginx场景实践

### 静态资源WEB服务





### 代理服务







### 负载均衡







### 缓存服务







## Nginx深度学习

### 动静分离





### rewrite规则





### 进阶模块配置





### HTTPS服务





### Nginx与LUA开发







