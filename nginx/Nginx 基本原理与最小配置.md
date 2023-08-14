- [Nginx 基础使用](#nginx-基础使用)
  - [目录结构](#目录结构)
  - [基本运行原理](#基本运行原理)
  - [Nginx配置与应用场景](#nginx配置与应用场景)
    - [最小配置](#最小配置)
      - [worker\_processes](#worker_processes)
      - [events模块下](#events模块下)
        - [worker\_connections](#worker_connections)
      - [http模块下](#http模块下)
        - [include mime.types;](#include-mimetypes)
        - [default\_type  application/octet-stream;](#default_type--applicationoctet-stream)
        - [keepalive\_timeout 65;](#keepalive_timeout-65)
        - [server模块下](#server模块下)
          - [listen 80;](#listen-80)
          - [server\_name localhost;](#server_name-localhost)
          - [location](#location)
          - [root html;](#root-html)
          - [index index.html index.htm;](#index-indexhtml-indexhtm)
          - [error\_page 500 502 503 504 /50x.html;](#error_page-500-502-503-504-50xhtml)


# Nginx 基础使用

## 目录结构

进入Nginx的主目录有如下文件夹

```shell
client_body_temp conf fastcgi_temp html logs proxy_temp sbin scgi_temp uwsgi_temp
```

其中以`_temp`结尾的文件夹是用来存放运行过程中的临时文件了。

其他主要的文件夹是：

+ conf：用来存放配置文件相关
+ html：用来存放静态文件的默认目录 html、css等
+ sbin：nginx的主程序
+ logs：存储各种日志，例如access记录访问的相关记录，error记录报错的记录，nginx.pid记录服务的pid号，即进程id号。

## 基本运行原理

![](https://raw.githubusercontent.com/timerring/picgo/master/picbed/image-20230301203529354.png)

一共有多个进程，其中有一个主进程Master负责读取，校验配置文件。

而子进程Worker则是相应对应的访问等请求。

## Nginx配置与应用场景

首先重点是Nginx的配置文件 `nginx.conf` ，其中有很大一部分的注释配置，这里先关注nginx所需的最小配置。

```shell
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

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

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

### 最小配置

#### worker_processes

`worker_processes 1;` 默认为1，表示开启一个业务进程，对应服务器的物理cpu的内核数，一个cpu内核对应一个worker_processes。当然也可以加大worker_processes 个数，但是对于同一个cpu来说需要调度，效率反而下降了。

#### events模块下

##### worker_connections

`worker_connections 1024;` 单个业务进程可接受连接数。

#### http模块下

##### include mime.types;

`include mime.types;` 引入http mime类型，加在http头中为浏览器指明应该解析的格式。

> 例如：
> 
> + application/octet-stream    bin exe dll;
>   
>   就是指明浏览器，以数据流的方式解析exe等类型，即下载下来。
> 
> + image/jpeg     jpeg jpg;
>   
>   则是直接让浏览器以图像的方式展示。

##### default_type  application/octet-stream;

如果`mime`类型没匹配上，默认使用二进制流的方式传输。

请求信息让操作系统收到，操作系统的网络接口转发请求到Nginx（请求前绑定注册端口）。如果关闭 `sendfile on;` 则Nginx先根据配置文件读取SSD上的文件到应用程序中，然后再发送到操作系统的网络接口（即网卡的驱动程序），这个过程会经过调度，网卡的缓存以及内核的缓存，层层缓存复制。

![](https://raw.githubusercontent.com/timerring/picgo/master/picbed/image-20230301210601793.png)

但是如果开启了`sendfile on;` ，则是直接发送信号，让网络接口读取文件。

![](https://raw.githubusercontent.com/timerring/picgo/master/picbed/image-20230301210612313.png)

##### keepalive_timeout 65;

保持连接超时时间，反向代理阶段会详解。

##### server模块下

nginx可以配置多个server，一个server就是一个主机。

虚拟主机配置

```
server {
    listen 80; 监听端口号
    server_name localhost; 主机名
    location / { 匹配路径
        root html; 文件根目录
        index index.html index.htm; 默认页名称
    }
    error_page 500 502 503 504 /50x.html; 报错编码对应页面
    location = /50x.html {
        root html;
    }
}
```

###### listen 80;

每个主机的监听端口号不同，相互不干扰。这每一个主机也成为虚拟主机（vhost）。

###### server_name localhost;

主机名(必须写能解析的主机名，例如在本机的host文件中定义了localhost是127.0.0.1。或者改为域名也可以)

###### location

匹配路径，用来匹配uri。通常完整的链接叫做url:`http://123.com/456/index.html` 而uri是指`/456/index.html`这一部分。

###### root html;

文件根目录，这里是相对路径。

###### index index.html index.htm;

默认页名称，这里index就是index.html或者index.htm。

###### error_page 500 502 503 504 /50x.html;

报错编码对应页面，通常返回500等错误，会自动跳转到`http://123.com/50x.html` 页面。

而如果没有这个页面，根据下面的逻辑，会自动跳转到root（即html目录）中找该页面。

```
    location = /50x.html {
        root html;
    }
```

![](https://raw.githubusercontent.com/timerring/picgo/master/picbed/image-20230301223951229.png)

Nginx拿到IP地址，从DNS服务器，发起TCP/IP，TCP/IP协议只能传递一些二进制的数据，这些数据以数据流的形式发送给目标服务器。HTTP协议在TCP/IP协议之上，底层的协议TCP/IP里不带约束。但是HTTP协议实现了终止符，请求的数据报文究竟有多长等等信息。另外一种协议https协议，是在http协议的基础之上，额外增加了一层数据安全的这种保障。因为在上网的时候会经历很多的网关，像我们家里的路由器，还有小区网关，服务供应商网关，最后电信联通网关。从区一级的网关，再到市一级的网关，再到全国的，经过加密后安全性更好。
