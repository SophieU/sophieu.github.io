---
title: Nginx入门教程
date: 2018-08-07
tag: Nginx
categories: 后端学习
---
## 简介
Nginx是一个轻量级的HTTP服务框架
优点：
- 支持海量高并发
- 内存消耗少
- 免费使用可以商业化
- 配置文件简单

## Linux下运维环境搭建
教程默认在Linux操作系统中安装使用，此交使用的是CentOS 7.4
```bash
yum -y install gcc gcc-c++ autoconf pcre-devel make automake
yum -y install wget httpd-tools vim
```
## 目录
- [Nginx环境搭建](#Nginx环境搭建)
- [查询与配置](#查询与配置)
- [操作nginx](#操作nginx)
- [Nginx反向代理的设置](#Nginx反向代理的设置)
- [自定义错误页](#自定义错误页)
- [访问权限设置](#访问权限设置)
- [Nginx设置虚拟主机](#Nginx设置虚拟主机)
- [Nginx适配PC或移动设备](#Nginx适配PC或移动设备)
- [Nginx的Gzip压缩配置](#Nginx的Gzip压缩配置)
- [CentOS下安装Node](#CentOS下安装Node)

## Nginx环境搭建
#### 安装
1. 先安装Nginx源（用于yum安装）
```bash
rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```
2. 再安装Nginx
```bash
yum install -y nginx
```
3. Nginx默认目录:`whereis nginx`

> 以下是Nginx的默认路径：
> (1) Nginx配置路径【常用】：`/etc/nginx/`
> (2) PID目录：/var/run/nginx.pid
> (3) 错误日志：/var/log/nginx/error.log
> (4) 访问日志：/var/log/nginx/access.log
> (5) 默认站点目录：/usr/share/nginx/html
> **事实上，只需知道Nginx配置路径，其他路径均可在/etc/nginx/nginx.conf 以及/etc/nginx/conf.d/default.conf 中查询到。**
4. 查看Nginx版本：`nginx -v`

## 查询与配置
#### 1. 查看Nginx安装目录
```bash
rpm -ql nginx
```
- `rpm`是linux的rpm包管理工具，`-q`代表询问模式，`-l`代表返回列表

#### 2. 配置文件
nginx.conf文件是Nginx总配置文件，在我们搭建服务器时经常调整这个文件。目录一般在：`etc/nginx`
- 如下是一个nginx.conf的一般内容
```bash
#运行用户，默认即是nginx，可以不进行设置
user  nginx;
#Nginx进程，一般设置为和CPU核数一样
worker_processes  1;   
#错误日志存放目录
error_log  /var/log/nginx/error.log warn;
#进程pid存放位置
pid        /var/run/nginx.pid;
events {
    worker_connections  1024; # 单个后台进程的最大并发数
}
http {
    include       /etc/nginx/mime.types;   #文件扩展名与类型映射表
    default_type  application/octet-stream;  #默认文件类型
    #设置日志模式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;   #nginx访问日志存放位置
    sendfile        on;   #开启高效传输模式
    #tcp_nopush     on;    #减少网络报文段的数量
    keepalive_timeout  65;  #保持连接的时间，也叫超时时间
    #gzip  on;  #开启gzip压缩
    include /etc/nginx/conf.d/*.conf; #包含的子配置项位置和文件
```
- 最后一行包含的是子配置项的内容，可以进入到conf.d目录，使用`vim default.conf`进行查看
```bash
# default.conf文件内容
server {
    listen       80;   #配置监听端口
    server_name  localhost;  //配置域名
    #charset koi8-r;     
    #access_log  /var/log/nginx/host.access.log  main;
    # 权限设置
    location / {
        root   /usr/share/nginx/html;     #服务默认启动目录
        index  index.html index.htm;    #默认访问文件
    }
    #error_page  404              /404.html;   # 配置404页面
    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;   #错误状态码的显示页面，配置后需要重启
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
- 上面可见，服务目录放在`/usr/share/nginx/html`下

## 操作nginx
#### 1. 启动nginx
- 通过命令：`nginx`直接启动nginx
```bash
# 普通
nginx
# 常用启动方式
systemctl start nginx.service
```
- 启动后没有任何提示，可以通过下面命令查询服务
```bash
ps aux | grep nginx
```
> 启动nginx服务后，如果是在阿里云或腾讯云服务器上装的，那么通过设置安全组信息（放开80端口）后，就可以通过公网IP进行访问了。
> 若有域名，可以将域名先解析到服务器的公网IP，再通过域名访问更方便

#### 2. 停止Nginx服务
```bash
# 立即停止服务，强制性的
nginx -s stop
# 从容停止服务，完成当前工作后停止
nginx -s quit
# 杀死nginx进程
killall nginx
# 系统服务方式停止
systemctl stop nginx.service
```

#### 3. 重启nginx服务
```bash
# 重启
systemctl restart nginx.service
# 重新载入配置文件【若修改了nginx配置文件，都需要重载一下】
nginx -s reload
# 查看端口号
netstat -tlnp
```
- 一般情况下nginx启动通过80端口提供HTTP访问

## Nginx反向代理的设置
我们现在的web模式基本的都是标准的CS结构，即Client端到Server端。那代理就是在Client端和Server端之间增加一个提供特定功能的服务器，这个服务器就是我们说的代理服务器。
- 关键词：`location`，`proxy_pass`

- **正向代理**：如翻墙工具就是一个典型的正向代理工具。代理服务器proxy代理客户端去访问服务器
![正向代理](http://jspang.com/static/upload/20181024/wxDZWDrFC_o9Xf5e2j-mRc2g.png)

- **反向代理**：现在基本所有大型网站的页面都是用的反向代理。代理服务器proxy代理服务端
    - 客户端发送的请求，想要访问server服务器上的内容。发送的内容被发送到代理服务器上，这个代理服务器再把请求发送到自己设置好的内部服务器上，而用户真实想获得的内容就在这些设置好的服务器上。
![反向代理](http://jspang.com/static/upload/20181024/5AwF_4MfkSxPtmre19xfPLgK.png)

> 通过图片的对比，应该看出一些区别，这里proxy服务器代理的并不是客户端，而是服务器,即向外部客户端提供了一个统一的代理入口，客户端的请求都要先经过这个proxy服务器。具体访问那个服务器server是由Nginx来控制的。再简单点来讲，`一般代理指代理的客户端，反向代理是代理的服务器。`

#### 反向代理的用途和好处
- **安全性**：正向代理的客户端能够在隐藏自身信息的同时访问任意网站，这个给网络安全代理了极大的威胁。因此，我们必须把服务器保护起来，使用反向代理客户端用户只能通过外来网来访问代理服务器，并且用户并不知道自己访问的真实服务器是那一台，可以很好的提供安全保护。
- **功能性**：反向代理的主要用途是为多个服务器提供负债均衡、缓存等功能。负载均衡就是一个网站的内容被部署在若干服务器上，可以把这些机子看成一个集群，那Nginx可以将接收到的客户端请求“均匀地”分配到这个集群中所有的服务器上，从而实现服务器压力的平均分配，也叫负载均衡。

#### 举例
- 如将访问HTTP的首页（假如是：http://mywebsite.com）代理到http://www.baidu.com去（即当访问公网http://mywebsite.com时，会跳转到baidu）
```bash
server {
     listen 80;
    server_name _;
    location / {
            proxy_pass http://www.baidu.com;
    }
}
```
#### 其它反向代理指令

反向代理还有些常用的指令，我在这里给大家列出：
- proxy_set_header :在将客户端请求发送给后端服务器之前，更改来自客户端的请求头信息。
- proxy_connect_timeout:配置Nginx与后端代理服务器尝试建立连接的超时时间。
- proxy_read_timeout : 配置Nginx向后端服务器组发出read请求后，等待相应的超时时间。
- proxy_send_timeout：配置Nginx向后端服务器组发出write请求后，等待相应的超时时间。
- proxy_redirect :用于修改后端服务器返回的响应头中的Location和Refresh。

## 自定义错误页
在上面的配置中可以看到一行：`error_page 500 502 503 504 /50x.html`,即当访问出现500等错误时，就会返回50x.html。nginx对外访问的文件夹为：`/usr/share/nginx/html`。因此，如果要自定义页面或者以后上传网站页面都放在这个目录下。
- 这里推荐使用`WinSCP`来进行FTP文件上传。[下载地址](https://winscp.net/eng/docs/lang:chs)

> 示例：添加一个404错误页
- 首先：准备好一个要显示的404.html
- 配置nginx.conf的errorPage
```bash
# 进入配置目录
cd etc/nginx
# 打开nginx.conf默认配置
vim nginx.conf
# 修改配置
error_page 500 502 503 504 /50x.html;
# 新增的error_page配置。注意加分号
error_page 404 /404.html;
```
- 重启nginx
```bash
nginx -s quit
nginx
```

> 此外，404错误也可以配置指向到另一个网址
```bash
# 修改配置【Esc  :wq保存】
vim nginx.conf
error_page 404 http://baidu.com;
# 保存重启
nginx -s quit
nginx
```

## 访问权限设置
`location`选项为nginx.conf中用于配置权限的属性。
```bash
# 默认的配置
location / {
    root html;
    index index.html index.htm;
    # 新增一项禁止访问(下面禁止某一IP访问)
    deny 181.214.193.17 
    # 允许访问(下面允许所有访问)
    allow all;
}
```
- 注意：上面的配置优先从上到下匹配，若deny在allow前面则先匹配它
```bash
location / {
    deny all;
    # 下面allow这项将不会起作用，因为前一行deny all
    allow 181.124.193.15;
}
```
- `deny`：禁止访问,设置后访问基设置的内容，将出现403 forbidden错误
- `allow`:允许访问
```bash
 # = 用于精确匹配
location =/img { 
    allow all;
}
# 正则匹配 ~
locatin ~\.php$ { # 以.php结尾的都不让访问
    deny all;
}
```
## Nginx设置虚拟主机
虚拟主机是指在一台物理主机服务器上划分出多个磁盘空间，每个磁盘空间都是一个虚拟主机，每台虚拟主机都可以对外提供Web服务，并且互不干扰。在外界看来，虚拟主机就是一台独立的服务器主机，这意味着用户能够利用虚拟主机把多个不同域名的网站部署在同一台服务器上，而不必再为建立一个网站单独购买一台服务器，既解决了维护服务器技术的难题，同时又极大地节省了服务器硬件成本和相关的维护费用。
- 配置虚拟主机可以`基于端口号`、`基于IP`和`基于域名`
#### 1. 基于端口号配置虚拟主机
原理：Nginx监听多个端口，根据不同的端口号，来区分不同的网站。可以直接在`etc/nginx/nginx.conf`文件里配置

- `vim nginx.conf`进入到配置页【先在/usr/share/nginx/html中创建8001文件夹并放入index.html】
```bash
# 新增一个server块
server {
    listen 8001;    # 监听端口号
    server_name localhost;  #配置匹配的域名
    root /usr/share/nginx/html/html8001;  #配置匹配的资源路径
    index index.html;  # 配置首页
}
```
- 保存后重启nginx。
- `curl http://localhost:8001`访问8001端口内容会返回对应html。(或者外部通过： `服务器公网IP：8001` 来访问)

#### 2. 基于IP配置虚拟主机
基于IP和基于端口的配置几乎一样，只是把server_name选项，配置成IP就可以了。
```bash
server{
    listen 80;
    server_name 112.74.164.244;
    root /usr/share/nginx/html/html8001;
    index index.html;
}
```
- 一般买的云服务器默认只有一个公网IP的，所以这种方法暂时用不到。

#### 3. 基于域名配置虚拟主机
实际应用中，访问网站都是用的域名，而一台服务器上可以提供对多个网站的访问，而不同网站的域名需要进行配置。
在nginx.conf中，配置server，其中server_name对应的就是你的域名，

- 例：设置不同的域名（或一个域名下多个二级域名）的访问。
    - `sub.mysite.com `: 这个域名映射到html下某一文件夹8001下的首页。
    - `mysite.com`:这个域名映射到默认的Nginx首页位置。
    - 前提需要解析好对应的域名，使域名对应主机IP
```bash
# 配置nginx.conf
server {
    listen  80;
    server_name   mysite.com;
    location / {
        root  /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
server {
    listen  80;
    server_name  sub.mysite.com;
    location / {
        root  /usr/share/nginx/html/8001;
        index  index.html index.htm;
    }
}
```

## Nginx适配PC或移动设备
现在很多网站都是有了PC端和H5站点的，因为这样就可以根据客户设备的不同，显示出体验更好的，不同的页面了。虽然用*自适应*前端样式也可以实现，但从复杂性和易用性上都不如分开编写的好，如淘宝，京东这些大型网站就都没有采用自适应，而是用分开制作的方式。
- **Nginx适配PC或移动设备的作用在于: Nginx会根据访问对向是PC还是移动设备，而程现不同的访问地址，对应着不同端的显示界面。**（例如，电脑进入淘宝时，返回的是PC网页 www.taobao.com，若开启开发者模式的手机端调试，刷新后，网站会变成 h5.m.taobao.com）。
- 关键点：Nginx提供了`if`语句，并有`$http_user_agent`变量作为客户端的userAgent
```bash
server {
    listen 80;
    server_name  www.kingdou.com;
    location / {
        root  /usr/share/nginx/pc;
        if ($http_user_agent ~* 'Android|webOS|iPhone|iPod|BlackBerry') {
                root    /usr/share/nginx/mobile;
        }
        index   index.html;
    }
}
```

## Nginx的Gzip压缩配置
Gzip是网页的一种网页压缩技术，经过gzip压缩后，页面大小可以变为原来的30%甚至更小。更小的网页会让用户浏览的体验更好，速度更快。gzip网页压缩的实现需要浏览器和服务器的支持。
![gzip](http://jspang.com/static/upload/20181028/Kt1ecAV6mSyPKRL1JG9Tot_Q.png)
从上图可以清楚的明白，gzip是需要服务器和浏览器同事支持的。当浏览器支持gzip压缩时，会在请求消息中包含Accept-Encoding:gzip,这样Nginx就会向浏览器发送经过gzip后的内容，同时在相应信息头中加入Content-Encoding:gzip，声明这是gzip后的内容，告知浏览器要先解压后才能解析输出。
#### gzip的配置项
- `gzip` : 用于开启或关闭gzip模块
- `gzip_buffers`: 设置系统获取几个单位的缓存用于存储gzip的压缩结果数据流。
- `gzip_comp_level`: gzip压缩比，压缩级别是1-9，1的压缩级别最低，9的压缩级别最高。压缩级别越高压缩率越大，压缩时间越长。
- `gzip_disable`: 通过该指令对一些特定的User-Agent不使用压缩功能。
- `gzip_min_length` :设置允许压缩的页面最小字节数，页面字节数从相应消息头的Content-length中进行获取。
- `gzip_http_version`: 识别HTTP协议版本，其值可以是1.1.或1.0.
- `gzip_proxied `: 用于设置启用或禁用从代理服务器上收到相应内容gzip压缩。
- `gzip_vary`:用于在响应消息头中添加Vary：Accept-Encoding,使代理服务器根据请求头中的Accept-Encoding识别是否启用gzip压缩。
#### 示例
```
http {
    ...
    gzip  on;
    gzip_types  text/plain  application/javascript  text/css;
    ... 
}
```
- `gzip on`是启用gizp模块，`gzip_types`是用于在客户端访问网页时，对文本、JavaScript 和CSS文件进行压缩输出。
- 浏览器开发者模式中，可以看到HTTP响应头信息中`Content-Encoding`为gzip类型。
