---
title: Nginx+PM2+node简单配置
date: 2019-02-15
tag: Nginx
categories: 后端学习
---
记录一些常用的简单Nginx配置，如：反向代理、负载均衡、HTTPS配置、解决跨域问题等
<!--more-->
## 一个最简单的反向代理配置方式
```bash
server {
　　listen 80;
　　server_name www.kingdou.fun;

　　location / {
　　　　 proxy_pass http://127.0.0.1:3000;
　　}
}
```
使用PM2永动机启动Node.js项目，再使用nginx做反向代理，简直完美。

因为node.js程序监听的是服务器端口，使用nginx做反向代理，就可以任意配置你的二级域名来访问你的程序

## 简单的负载均衡配置方式
```bash
upstream b.com { 
      ip_hash;
      server  192.168.5.150:80; 
      server  192.168.5.151:80;
      server  192.168.5.151:8080; 
}
```

## 简单的Https配置
```bash
server{
        listen 443 ssl;
        server_name xxx.xxx.com;
        ssl_certificate /root/certs/test.crt;
        ssl_certificate_key /root/certs/test.key;
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout 5m;
        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;
        location /{
                proxy_pass http://localhost:3000;
                proxy_set_header Host $host;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
                proxy_read_timeout 86400;
        }
}
```

## Nginx解决跨域问题
```bash
server{
        listen  80;
        server_name test.abc.com;

        location / {
            proxy_pass http://127.0.0.1:8080;

            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Headers Content-Type;
            add_header Access-Control-Allow-Methods POST;
            add_header Access-Control-Allow-Methods GET;
        }

}
```