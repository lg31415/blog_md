title: Linux常用命令
date: 2015-07-12 15:03:57
tags: Linux
categories:
---

### Mysql rpm 安装包安装顺序

+ `rpm -ivh perl-DBI-1.609-4.el6.i686.rpm`
+ `rpm -ivh perl-DBD-MySQL-4.013-3.el6.i686.rpm`
+ `rpm -Uvh openssl-1.0.1e-16.el6_5.14.i686.rpm`
+ `rpm -Uvh mysql-libs-5.1.73-3.el6_5.i686.rpm`
+ `rpm -ivh mysql-5.1.73-3.el6_5.i686.rpm mysql-server-5.1.73-3.el6_5.i686.rpm`


va
```ssh
JAVA_HOME=/usr/local/soft/java/jdk1.7.0_55
PATH=$JAVA_HOME/bin:$PATH
```
#### 主机名 
```ssh
//修改主机名
vim /etc/sysconfig/network
//修改主机名 IP 映射
vim /etc/hosts
//主机名生效
sudo hostname
```


### yum安装软件
#### scp
```ssh
    yum install openssh-clients
    
    mkdir /usr/local/app/tomcat
    #安装顺序
    openssh-5.3p1-104.el6_6.1.i686                   
    libedit-2.11-4.20080712cvs.1.el6.i686           
    openssh-clients-5.3p1-104.el6_6.1.i686          
    openssh-server-5.3p1-104.el6_6.1.i686
```

## 淘宝TFS安装
+ Linux 64位
```ssh
 ./configure --prefix=/usr/local/soft/tb/ --disable-readline --without-tcmalloc 
```
### Hosts映射
```
192.168.137.101       c1
192.168.137.102       c2
192.168.137.103       c3
192.168.137.104       c4
```


### CentOS-1
#### MAC:
```
eth0    08:00:27:3F:48:85       HostOnly
IP:192.168.137.101
eth1    08:00:27:31:6F:43
```
### CentOS-2
#### MAC:
```
eth0    08:00:27:A2:B9:68       HostOnly
```
### CentOS-3
#### MAC:
```
eth0    08:00:27:16:DB:38       HostOnly
```
### CentOS-4
#### MAC:
```
eth0    08:00:27:4C:AA:E5       HostOnly
```

