title: mysql命令
date: 2015-07-12 20:26:32
tags: mysql
categories:
---

### linux安装mysql设置
+ `/usr/bin/mysqladmin -u root password '123'`
    + 设置mysql用户名/密码 
+ `mysql`状态/开启/停止
    + `service mysqld status`
    + `service mysqld start`
    + `service mysqld stop`
+ 任意主机以用户root和密码mypwd连接到mysql服务器
```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123' WITH GRANT OPTION;
mysql> flush privileges;
```
+ IP为`192.168.1.102`的主机以用户myuser和密码mypwd连接到mysql服务器
```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.102' IDENTIFIED BY 'mypwd' WITH GRANT OPTION;
mysql> flush privileges;
```
