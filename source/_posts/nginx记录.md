title: nginx记录
author: John Doe
date: 2019-10-08 22:36:40
tags:
---
### 1.nginx 创建一个域名指向 静态资源html

http://www.test.com/

需要在nginx头上添加用户组，并且把静态资源的文件夹赋予用户权限

sudo chown -R root:wheel /Users/zhousteady/Downloads/test/examples

```
user root wheel;
worker_processes  1;
.
.
.

server {
      listen 80;
      server_name www.test.com;
      index index.html;
  
      root /Users/zhousteady/Downloads/test/examples/;

      location / {
        proxy_redirect     off;
        proxy_set_header   Host $host;
        proxy_set_header   X-Forwarded-Host $host;
        proxy_set_header   X-Forwarded-Server $host;

        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_buffering    on;
      }

      location = /favicon.ico {
        return 204;
        access_log     off;
        log_not_found  off;
      }

      location ~* "(.+)-(\w+)\.\w+$" {
        error_page 404 = @notfound;
      }

      location @notfound {
        rewrite ^(.+)-(\w+)(\.\w+)$ $1$3 break;
      }

}
```

###  antd 线上静态资源和url 转发

http请求 静态资源文件，需要让 运维配置 前置的nginx  , 类似：   请注意publicPath: '/ued_admin_static/', 为webpack静态资源打包前缀


```
location ~ ^/ued_admin_static/{
           add_header Cache-Control "no-cache, max-age=0, must-revalidate";
           rewrite ^/ued_admin_static/(.*) /$1 break;
           proxy_pass http://cd_ued_ftrade_admin;
        }


        location ~ ^/ued_admin/{
           add_header Cache-Control "no-cache, max-age=0, must-revalidate";
           rewrite ^/ued_admin/(.*) /index.html break;
           index index.html;
           proxy_pass http://cd_ued_ftrade_admin;
        }
```        