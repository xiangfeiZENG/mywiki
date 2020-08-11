# CentOS 7.4 图片服务器安装与配置

采用nginx + vsftp方式搭建图片服务器。

提前安装好nginx与vsftp



nginx配置：

```
# 在配置文件“}”前加上
[root@xfei-server vhost]# vim nginx.conf
# added by mvne
include vhost/*.conf
[root@xfei-server conf]# cat vhost/img.yellowmobile.com.conf 
server {
    listen 8000;
    autoindex off;
    server_name 172.17.67.251;
    access_log /usr/local/nginx/logs/access.log combined;
    index index.html index.htm index.jsp index.php;
    #error_page 404 /404.html;
    if ( $query_string ~* ".*[\;'\<\>].*" ){
        return 404;
    }

    location ~ /mvne_portal/dist/view/* {
        deny all;
    }


    location /imgserver {
        root /var/ftp/mvne;
        add_header Access-Control-Allow-Origin *;
    }
}

```

访问路径：http://39.96.55.21:8000/imgserver/test.png 可以访问图片。



本质上是通过ftp上传图片，通过nginx访问图片。

