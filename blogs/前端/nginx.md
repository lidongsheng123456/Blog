---
title: nginx
date: 2025/03/07
tags:
   - 部署
categories:
   - 前端
---

## 因为之前一直使用宝塔Linux面板通过nginx图形化界面部署，没有了解过原生操作不了解部署流程再加上竞赛需要手动在nginx部署，特意了解一下

## 部署vue项目

### 一.将打包好的dist文件夹放到nginx的html文件夹下

### 二.根据nginx.conf配置，要设置多个不同目录的HTML根路径，可以通过以下两种方式实现：

1. 方案一：基于URL路径区分

```
server {
# ...原有配置保持不变...

    # 默认路径（保留原有配置）
    location / {
        root   html/sky;
        index  index.html index.htm;
    }

    # 新增dist1目录配置
    location /dist1/ {
        root   html;  # 实际路径为 html/dist1
        index  index.html index.htm;
        try_files $uri $uri/ /dist1/index.html;
    }

    # 新增dist2目录配置
    location /dist2/ {
        root   html;  # 实际路径为 html/dist2
        index  index.html index.htm;
        try_files $uri $uri/ /dist2/index.html;
    }
}
```

2. 方案二：基于不同主机id或域名区分

```
# 新增server块
server {
    listen       8088;
    server_name  localhost;  # 修改为实际域名

    location / {
        root   html/dist1;
        index  index.html index.htm;
        # 这个指令确保当请求的文件或目录不存在时，Nginx会返回 index.html 文件，从而使前端路由能够正常工作。
        try_files $uri $uri/ /index.html;
    }
    
    # 反向代理,处理管理端发送的请求
    location /prod-api/ {
        proxy_pass http://localhost:9090/;
    }
}

# 再新增一个server块
server {
    listen       8089;
    server_name  localhost;  # 修改为实际域名

    location / {
        root   html/dist2;
        index  index.html index.htm;
        # 这个指令确保当请求的文件或目录不存在时，Nginx会返回 index.html 文件，从而使前端路由能够正常工作。
        try_files $uri $uri/ /index.html;
    }
    
    # 反向代理,处理管理端发送的请求
    location /prod-api/ {
        proxy_pass http://localhost:9090/;
    }
}
```

3. 完整文件

```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
	
	map $http_upgrade $connection_upgrade{
		default upgrade;
		'' close;
	}

	upstream webservers{
	  server 127.0.0.1:8080 weight=90 ;
	  #server 127.0.0.1:8088 weight=10 ;
	}

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html/sky;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # 反向代理,处理管理端发送的请求
        location /api/ {
			proxy_pass   http://localhost:8080/admin/;
            #proxy_pass   http://webservers/admin/;
        }
		
		# 反向代理,处理用户端发送的请求
        location /user/ {
            proxy_pass   http://webservers/user/;
        }
		
		# WebSocket
		location /ws/ {
            proxy_pass   http://webservers/ws/;
			proxy_http_version 1.1;
			proxy_read_timeout 3600s;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "$connection_upgrade";
        }


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

    # 新增server块
    server {
        listen       8088;
        server_name  localhost;  # 修改为实际域名

        location / {
            root   html/dist1;
            index  index.html index.htm;
            try_files $uri $uri/ /index.html;
        }

         # 反向代理,处理管理端发送的请求
        location /prod-api/ {
			proxy_pass http://localhost:9090/;
        }
    }

    # 再新增一个server块
    # server {
    #     listen       80;
    #     server_name  localhost;  # 修改为实际域名

    #     location / {
    #         root   html/dist2;
    #         index  index.html index.htm;
    #         try_files $uri $uri/ /index.html;
    #     }
    # }


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
```

## 总结

1. 开发阶段，Vue项目通常运行在本地开发服务器（如webpack-dev-server或vite），
   这时候使用devServer的代理功能是为了解决跨域问题，将API请求转发到后端服务。
   而生产环境中，前端代码会被构建为静态文件，通过Nginx等Web服务器托管，此时需要Nginx来处理API请求的转发，
   因为此时前端代码已经不再运行在开发服务器上，而是由Nginx直接提供静态文件服务。

2. 一个 server 块就代表一个独立的 Web 服务，但它的具体行为取决于 listen 和 server_name 的配置。
