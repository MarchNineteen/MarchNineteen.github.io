---
title: nginx基础配置
date: 2018-12-03 
desc:
keywords: nginx
categories: [nginx]
---
```
#全局块 start
#配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
########### 每个指令必须有分号结束。#################

#user  nobody; #配置用户或者组，默认为nobody nobody。
worker_processes  1;#允许生成的进程数，默认为1

#制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;#指定nginx进程运行文件存放地址

#全局块 end

#events块 start
#配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等

events {
	accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
	multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
	#use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;	#最大连接数，默认为1024
}

#events块 end

#http块 start
#可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
http {
    include       mime.types;#文件扩展名与文件类型映射表 
    default_type  application/octet-stream;#默认文件类型
	#access_log off; #取消服务日志   
	
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" ' #自定义格式
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;  #combined为日志格式的默认值

    sendfile        on; #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
	#sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
	
    #tcp_nopush     on; 
	#开启或者关闭nginx在FreeBSD上使用TCP_NOPUSH套接字选项， 在Linux上使用TCP_CORK套接字选项。 选项仅在使用sendfile的时候才开启。 #开启此选项允许在Linux和FreeBSD4.*上将响应头和正文的开始部分一起发送；一次性发送整个文件

    #keepalive_timeout  0; #连接超时时间，默认为65s，可以在http，server，location块。
    keepalive_timeout  65;

    #gzip  on;是否开启Gzip 压缩
	#gzip_min_length 1k; 不压缩临界值，大于1K的才压缩，一般不用改
	#gzip_buffers 4 16k;
	#gzip_http_version 1.0; 用了反向代理的话，末端通信是HTTP/1.0，默认是HTTP/1.1
	#gzip_comp_level 2; 压缩级别，1-10，数字越大压缩的越好，时间也越长
	#gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png; #进行压缩的文件类型，缺啥补啥就行了，JavaScript有两种写法，最好都写上吧，总有人抱怨js文件没有压缩，其实多写一种格式就行了
	#gzip_vary off; 跟Squid等缓存服务有关，on的话会在Header里增加"Vary: Accept-Encoding"
	#gzip_disable "MSIE [1-6]\."; IE6对Gzip不怎么友好，不给它Gzip了

	#server块 start
	#配置虚拟主机的相关参数，一个http中可以有多个server。
	
    server {
        listen       8080; #监听端口
        server_name  localhost; #监听地址 

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

		#location块 start 
		#配置请求的路由，以及各种页面的处理情况。
		
        location / {
            root   html; #根目录
            index  index.html index.htm; #设置默认页
        }
		#location块 end 
		
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
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
	
	#server块 end

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
#http块 end
```    
    
