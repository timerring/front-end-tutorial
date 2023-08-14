- [Nginx 安装部署](#nginx-安装部署)
  - [虚拟机安装CentOS7.4](#虚拟机安装centos74)
  - [Linux配置](#linux配置)
    - [配置上网](#配置上网)
    - [配置静态ip](#配置静态ip)
  - [Nginx的安装](#nginx的安装)
    - [版本区别](#版本区别)
    - [备份克隆](#备份克隆)
  - [安装](#安装)
    - [编译安装](#编译安装)
    - [报错解决](#报错解决)
  - [启动Nginx](#启动nginx)
    - [防火墙](#防火墙)
  - [安装成系统服务](#安装成系统服务)


# Nginx 安装部署

虚拟机：VMware workstation 16
操作系统：CentOS 7.4
下载地址：https://vault.centos.org/centos/7.4.1708/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso

建议电脑配置：

+ 内存：建议8G以上
+ 磁盘：建议使用SSD
+ CPU：4核以上主流即可

## 虚拟机安装CentOS7.4

1 新建虚拟机

2 典型安装

3 选择CentOS镜像

4 存储位置

5 虚拟机磁盘配置 ：注意这里只是分配最大磁盘大小，实际不会在一开始占用那么多，是一个动态的区域，在实际实用中逐渐分配。

6 自定义其他配置：默认即可。

7 安装系统

默认即可，在分区选择时记得点进去确认一下。

![](https://raw.githubusercontent.com/timerring/picgo/master/picbed/image-20230228215727734.png)

接下来继续安装，安装过程中可以设置root密码。安装后重启即可。

## Linux配置

### 配置上网

修改配置网卡配置文件（Linux中所有设置都是通过修改配置文件实现的）

```shell
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

把 `ONBOOT` 修改为yes即可，即重启操作系统时就会重启该网卡。

![](https://raw.githubusercontent.com/timerring/picgo/master/picbed/image-20230228220308619.png)

然后重启网络服务

```shell
systemctl restart network
```

重新测试ping可发现，网络正常。

可以用 `ip addr` 测试当前ip地址。

通常，为了便于连接服务器以及复制粘贴等功能，一般采用XShell作为终端。下载地址：https://www.xshell.com/zh/free-for-home-school/



### 配置静态ip

之前的网络配置是使用dhcp方式分配ip地址，这种方式会在系统每次联网的时候分配一个ip给我们用，系统下次启动的时候ip会变，不方便管理。

配置静态ip首先需要打开网卡配置文件

```shell
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

修改启动协议 `BOOTPROTO=static`

手动配置ip地址

```shell
IPADDR=192.168.44.101
NETMASK=255.255.255.0
GATEWAY=192.168.44.1
DNS1=8.8.8.8
```

> 一些常见的公网DNS服务器：
>
> 阿里
>
> ```
> 223.5.5.5
> 223.6.6.6
> ```
>
> 腾讯
>
> ```
> 119.29.29.29
> 182.254.118.118
> ```
>
> 百度
>
> ```
> 180.76.76.76
> ```
>
> 114DNS
>
> ```
> 114.114.114.114
> 114.114.115.115
> ```
>
> 谷歌
>
> ```
> 8.8.8.8
> 8.8.4.4
> ```

注意，这里是严格区分大小写的。

> 样例：
>
> ```
> TYPE=Ethernet
> PROXY_METHOD=none
> BROWSER_ONLY=no
> BOOTPROTO=static
> DEFROUTE=yes
> IPV4_FAILURE_FATAL=no
> IPV6INIT=yes
> IPV6_AUTOCONF=yes
> IPV6_DEFROUTE=yes
> IPV6_FAILURE_FATAL=no
> IPV6_ADDR_GEN_MODE=stable-privacy
> NAME=ens33
> UUID=10ac735e-0b8f-4b19-9747-ff28b58a1547
> DEVICE=ens33
> ONBOOT=yes
> IPADDR=192.168.44.101
> NETMASK=255.255.255.0
> GATEWAY=192.168.44.1
> DNS1=8.8.8.8
> ```

然后重启网络服务即可。

```shell
systemctl restart network
```

> 这里第一次配置时，网络仍然无法连接，这里我修改了网关与虚拟机的网关保持一致。
>
> 虚拟机网关查看方法：
>
> ```
> 编辑---> 虚拟网络编辑器--->选择VMnet8--->NAT设置--->网关IP
> ```
>
> 然后把上述GATEWAY修改为你的网关IP即可。然后就可以ping通了，仅供参考。

## Nginx的安装

### 版本区别

常用版本分为四大阵营

Nginx开源版：非常纯粹的反向代理，负载均衡。
http://nginx.org/

Nginx plus 商业版
https://www.nginx.com

openresty：以Lua脚本扩展的nginx
http://openresty.org/cn/

Tengine：以C语言扩展的nginx
http://tengine.taobao.org/

### 备份克隆

在安装前，最好先克隆备份，以防安装错误后难以恢复。

首先可以通过 `init 0` 关闭虚拟机。

然后右键虚拟机，选择 管理--->克隆，选择创建连接克隆，这种克隆方法可以比较出两者之间的差异。

## 安装

### 编译安装

首先传输nginx安装包，然后 `tar zxvf nginx-1.21.6.tar.gz`， 进入解压后的文件夹，执行 `./configure`  安装，由于缺少相关的依赖，因此会报错。只需要根据对应的报错安装即可。

```shell
./configure --prefix=/usr/local/nginx #设置前缀，安装到指定的目录下
make
make install
```

### 报错解决

提示

```shell
checking for OS

Linux 3.10.0-693.el7.x86_64 x86_64
checking for C compiler ... not found
./configure: error: C compiler cc is not found
```

安装gcc

```shell
yum install -y gcc
```



提示

```shell
./configure: error: the HTTP rewrite module requires the PCRE library.
You can either disable the module by using --without-http_rewrite_module
option, or install the PCRE library into the system, or build the PCRE library
statically from the source with nginx by using --with-pcre=<path> option.
```

安装perl库

```shell
yum install -y pcre pcre-devel
```

提示：

```shell
./configure: error: the HTTP gzip module requires the zlib library.
You can either disable the module by using --without-http_gzip_module
option, or install the zlib library into the system, or build the zlib library
statically from the source with nginx by using --with-zlib=<path> option.
```

安装zlib库

```shell
yum install -y zlib zlib-devel
```



## 启动Nginx

手动启动的方式，进入安装好的目录`/usr/local/nginx/sbin`  。

```shell
./nginx 启动
./nginx -s stop 快速停止
./nginx -s quit 在退出前完成已经接受的连接请求
./nginx -s reload 重新加载配置
```

### 防火墙

关闭防火墙

```shell
systemctl stop firewalld.service
```

禁止防火墙开机启动

```shell
systemctl disable firewalld.service
```

放行端口

```shell
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

重启防火墙

```shell
firewall-cmd --reload
```

关闭防火墙后启动服务，可以通过浏览器访问ip验证服务是否启动。若成功则如下所示：

![](https://raw.githubusercontent.com/timerring/picgo/master/picbed/image-20230301194912601.png)

## 安装成系统服务

每次手动启动服务过于复杂，可以考虑安装成系统级的服务。

创建服务脚本

```shell
vi /usr/lib/systemd/system/nginx.service
```

服务脚本内容

```shell
[Unit]
Description=nginx - web server
After=network.target remote-fs.target nss-lookup.target
[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s stop
ExecQuit=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true
[Install]
WantedBy=multi-user.target
```

重新加载系统服务

```shell
systemctl daemon-reload
```

启动服务

```shell
systemctl start nginx.service
```

查看服务状态（是否启动）

```shell
systemctl status nginx.service
```

![](https://raw.githubusercontent.com/timerring/picgo/master/picbed/image-20230301195431814.png)

可以看到，服务是已经active的。

开机启动

```shell
systemctl enable nginx.service
```

