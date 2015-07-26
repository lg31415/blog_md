title: LVS-HA
date: 2015-07-21 23:13:56
tags: 负载均衡
categories: 负载均衡
---
## 使用keepalived实现LVS的DR模式热备
### 一、说明：
+ 在《LVS之一：三种工作模式的优缺点比较（NAT/TUN/DR）》文章讲述了LVS的三种转发模式对比，我们先就用LVS的DR模式来实现Web应用的负载均衡。为了防止LVS服务器自身的单点故障导致整个Web应用无法提供服务，因此还得利用Keepalived实现lvs的高可用性，keepalived主要使用VRRP协议来保存链路的高可用性。而VRRP(Virtual Router Redundancy Protocol)协议本身是用于实现路由器冗余的协议。
LVS-DR模式的原理如下（借用别人的图）：

即客户端访问VIP（域名解析到VIP），LVS接收情况后根据负载均衡调度算法，转发请求到真实服务器，真实服务器接收到客户端请求后，将处理结果直接返回给客户端。

### 二、网络拓扑及IP规划：

#### 说明：
+ 1、  LVS服务器和WEB服务器必须在同一网段；
+ 2、  多台Web服务器间使用NFS服务器等共享存储存放用户上传附件，以保障数据的一致性（如上传图片）。下文讨论
+ IP地址规划：
```ssh
    VIP              192.168.18.60
    LVS-MASTER     192.168.18.51
    LVS-BACKUP     192.168.18.52
    NFS   Server       192.168.18.20
    MySQL               192.168.18.18
    WEB1        192.168.18.61
    WEB2        192.168.18.62
```

### 三、安装LVS+Keepalived：
1. 使用yum在线安装：
```
    [root@lvs1 ~]# yum –y install keepalived
```
2. 检查安装结果：
```ssh
    [root@lvs1 ~]# rpm -qa |grep keepalived
    keepalived-1.2.7-3.el6.x86

```
3. 服务配置：
```ssh
    [root@lvs1 ~]# chkconfig keepalived on   ##设置为开机启动
    [root@lvs1 ~]# service keepalived start   ##立即启动keepalived服务
```

### 四、配置LVS服务器：
1.  备份配置文件：
```ssh
[root@lvs1 ~]# cp /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.bak
```
2.  修改配置文件，具体如下：
+ 原始的配置文件包含了三种模式的配置，而这里我们只需要用到DR模式，因此最终保留配置如下：
```ssh
[root@lvs1 ~]#  vim /etc/keepalived/keepalived.conf
global_defs {                       ##全局配置部分
#   notification_email {             ##下面几行均为全局通知配置，可以实现出现问题后报警，但功能有限，因此注释掉，并采用Nagios监视lvs运行情况
#       admin@toxingwang.com
#   }
#   notification_email_from master@toxingwang.com
#   smtp_server smtp.exmail.qq.com
#   smtp_connect_timeout 30
    router_id LVS_MASTER             ##设置lvs的id，在一个网络内应该是唯一的
}

vrrp_instance VI_1 {            ##设置vrrp组，唯一且同一LVS服务器组要相同
    state MASTER             ##备份LVS服务器设置为BACKUP
    interface eth0             # #设置对外服务的接口
    virtual_router_id 51        ##设置虚拟路由标识
    priority 100                   #设置优先级，数值越大，优先级越高，backup设置为99，这样就能实现当master宕机后自动将backup变为master，而当原master恢复正常时，则现在的master再次变为backup。
    advert_int 1            ##设置同步时间间隔
    authentication {         ##设置验证类型和密码，master和buckup一定要设置一样
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {          ##设置VIP，可以多个，每个占一行
        192.168.18.60
    }
}

virtual_server 192.168.18.60 80 {
    delay_loop 6            ##健康检查时间间隔，单位s
    lb_algo wrr             ##负载均衡调度算法设置为加权轮叫
    lb_kind DR                              ##负载均衡转发规则
    nat_mask 255.255.255.0   ##网络掩码，DR模式要保障真实服务器和lvs在同一网段
    persistence_timeout 50    ##会话保持时间，单位s
    protocol TCP                           ##协议
    real_server 192.168.18.61 80 {      ##真实服务器配置，80表示端口
        weight 3                              ##权重
        TCP_CHECK {                       ##服务器检测方式设置
        connect_timeout 5    ##连接超时时间
        nb_get_retry 3
        delay_before_retry 3
        connect_port 80
        }
    }
    real_server 192.168.18.62 80 {
        weight 3
        TCP_CHECK {
        connect_timeout 10
        nb_get_retry 3
        delay_before_retry 3
        connect_port 80
        }
    }
}

```
作为高可用的备份lvs服务器配置只需在上述配置中的master修改backup,priority 100修改priority 99即可，其他保持相同。
3.  分别在两台lvs服务器上重启服务：
```ssh
    [root@lvs1 ~]# service keepalived start
```
__注：__由于keepalived配置文件有语法错误也能启动，因此看到启动了lvs服务，不代表配置文件没有错误，如果遇到lvs不能正常转发，及时跟踪日志进行处理。

##### 日志跟踪方法：
1. 开两个`ssh`窗口连接到`lvs`服务器，第一个窗口运行如下命令：
```ssh
    [root@lvs1 ~]# tail -f /var/log/messages
```
2. 第二个窗口重新启动`keepalived`服务，同时观察窗口1中日志的变化，然后根据日志提示解决即可。
### 五、WEB服务器IP绑定配置：
+ 在两台WEB服务器安装并配置（非本文重点，不做web安装配置讲解），建立测试网页
```ssh
    [root@lvs1 ~]# cd /var/www/html
    [root@lvs1 ~]# touch index.html
    [root@lvs1 ~]# vim index.html
```
输入I am http:本机IP

先使用实际IP进行访问测试，能正常访问后，进行如下操作：


+ 在前面的文章中介绍DR模式时提到：负载均衡器也只是分发请求，应答包通过单独的路由方法返回给客户端。但实际上客户端访问时，IP都是指向的负载均衡器的ip（也就是LVS的VIP），如何能让真是服务器处理IP头为VIP的请求，这就需要做下面的操作，将VIP绑定到真实服务器的lo网口（回环），为了防止IP广播产生IP冲突，还需关闭IP广播，具体如下：
+ 1. 编辑开机启动脚本：
```ssh
  [root@web1 ~]# vim /etc/init.d/realserver
#!/bin/bash
#chkconfig: 2345 79 20
#description:realserver
SNS_VIP=192.168.18.60
. /etc/rc.d/init.d/functions
case "$1" in
start)
ifconfig lo:0 $SNS_VIP netmask 255.255.255.255 broadcast $SNS_VIP
/sbin/route add -host $SNS_VIP dev lo:0
echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
sysctl -p >/dev/null 2>&1
echo "RealServer Start OK"
;;
stop)
ifconfig lo:0 down
route del $SNS_VIP >/dev/null 2>&1
echo "0" >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo "0" >/proc/sys/net/ipv4/conf/lo/arp_announce
echo "0" >/proc/sys/net/ipv4/conf/all/arp_ignore
echo "0" >/proc/sys/net/ipv4/conf/all/arp_announce
echo "RealServer Stoped"
;;
*)
echo "Usage: $0 {start|stop}"
exit 1
esac
exit 0
```

##### 脚本说明：
```
arp_ignore: 定义接收到ARP请求时的响应级别；
0：只要本地配置的有相应地址，就给予响应；默认；
1：仅在请求的目标地址配置在到达的接口上的时候，才给予响应；
arp_announce：定义将自己地址向外通告时的通告级别；
0：将本地任何接口上的任何地址向外通告；默认；
1：试图仅向目标网络通告与其网络匹配的地址；
2：仅向与本地接口上地址匹配的网络进行通告；
```
2. 设置脚本开机启动并立即启动：
```ssh
    [root@web1 ~]# chkconfig realserver on
    [root@web1 ~]# service realserver start

    RealServer Start OK
```
3. 检测IP配置：
```ssh
[root@web1 ~]# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:50:56:B3:54:5A
inet addr:192.168.18.61  Bcast:192.168.18.255  Mask:255.255.255.0
inet6 addr: fe80::250:56ff:feb3:545a/64 Scope:Link
UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
RX packets:98940 errors:4 dropped:4 overruns:0 frame:0
TX packets:82037 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:1000
RX bytes:23077218 (22.0 MiB)  TX bytes:16043349 (15.3 MiB)
Interrupt:18 Base address:0x2000
 
lo        Link encap:Local Loopback
inet addr:127.0.0.1  Mask:255.0.0.0
inet6 addr: ::1/128 Scope:Host
UP LOOPBACK RUNNING  MTU:16436  Metric:1
RX packets:300 errors:0 dropped:0 overruns:0 frame:0
TX packets:300 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:0
RX bytes:528420 (516.0 KiB)  TX bytes:528420 (516.0 KiB)
 
lo:0      Link encap:Local Loopback
inet addr:192.168.18.60  Mask:255.255.255.255
UP LOOPBACK RUNNING  MTU:16436  Metric:1
```
4. 测试LVS：
在lvs master服务器上运行如下命令：
```ssh
    [root@lvs1 ~]# watch -n 1 ipvsadm -ln
```
在多台客户端通过浏览器访问VIP或域名（域名需解析到VIP），看能否获取到正确的页面，且同时观察上述窗口的变化。

 

