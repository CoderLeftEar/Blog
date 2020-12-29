---
title: "Mysql问题"
date: 2020-05-30T17:14:48+08:00
author: CoderLeftEar
categories: ["Mysql"]
tags: ["Mysql"]
draft: true
---

# MySQL问题

## Tomcat -> shutdown.sh

```
java.net.ConnectException: Connection refused
	at java.net.PlainSocketImpl.socketConnect(Native Method)
	at java.net.PlainSocketImpl.doConnect(PlainSocketImpl.java:333)
	at java.net.PlainSocketImpl.connectToAddress(PlainSocketImpl.java:195)
	at java.net.PlainSocketImpl.connect(PlainSocketImpl.java:182)
	at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:366)
	at java.net.Socket.connect(Socket.java:529)
	at java.net.Socket.connect(Socket.java:478)
	at java.net.Socket.<init>(Socket.java:375)
	at java.net.Socket.<init>(Socket.java:189)
	at org.apache.catalina.startup.Catalina.stopServer(Catalina.java:498)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
	at java.lang.reflect.Method.invoke(Method.java:597)
	at org.apache.catalina.startup.Bootstrap.stopServer(Bootstrap.java:370)
	at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:457)
```

原因是tomcat内存太小了，需要给tomcat增加虚拟内存。

### 在linux系统里修改tomcat内存的方法

修改/opt/tomcat/bin/catalina.sh这个文件； 

```
# OS specific support.  $var _must_ be set to either true or false.
cygwin=false
```

这两行之行插入一行，最终修改为以下这样：

```
# OS specific support.  $var _must_ be set to either true or false.
JAVA_OPTS="-Xms512m -Xmx1024m -Xss2048K -XX:PermSize=256m -XX:MaxPermSize=512m" 
cygwin=false
```

重启tomcat即可。

## Centos6、Centos7开启关闭防火墙

### Centos6防火墙：

```
查询防火墙状态: service iptables status
停止防火墙: service iptables stop
启动防火墙: service iptables start
重启防火墙: service iptables restart
永久关闭防火墙: chkconfig iptables off
永久关闭后启用: chkconfig iptables on
```

### Centos6开放端口：不是永久的

```
//开启8080端口防火墙
sudo iptables -I INPUT -p tcp -m tcp --dport 8080 -j ACCEPT
 
//保存设置
sudo service iptables save
```

### Centos7防火墙：

```
永久关闭防火墙：systemctl disable firewalld
开启防火墙：systemctl start firewalld
关闭防火墙：systemctl stop firewalld
重启防火墙：systemctl reload firewalld
查询防火墙状态：systemctl status firewalld
```

### Centos7开放端口

```
//开启8080端口
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent 
// 查看
firewall-cmd --list-ports 
//重新启动防火墙
sudo firewall-cmd --reload
```

注意：在开启某个端口的防火墙时，如果没有“--permanent”标识，则标识它不是永久开启的，在服务器重启之后会恢复之前的设置。

## Mysql开放远程连接权限

```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;
```

## Mysql用户授权管理

https://www.cnblogs.com/stfei/p/9184320.html

### 创建用户

1. 进入mysql数据库下

```
mysql> use mysql
Database changed
```

2. 对新用户增删改

```
1.创建用户:
# 指定ip：192.118.1.1的mjj用户登录
create user 'mjj'@'192.118.1.1' identified by '123';
# 指定ip：192.118.1.开头的mjj用户登录
create user 'mjj'@'192.118.1.%' identified by '123';
# 指定任何ip的mjj用户登录
create use 'mjj'@'%' identified by '123';

2.删除用户
drop user '用户名'@'IP地址';


3.修改用户
rename user '用户名'@'IP地址' to '新用户名'@'IP地址';

4.修改密码
set password for '用户名'@'IP地址'=Password('新密码');
```

3. 对当前用户授权管理

```
#查看权限
show grants for '用户'@'IP地址'

#授权
grant select ,insert,update on db1.t1 to "mjj"@'%';

# 表示有所有的权限，除了grant这个命令，这个命令是root才有的。
grant all privileges  on db1.t1 to "mjj"@'%';

#取消权限
取消来自远程服务器的mjj用户对数据库db1的所有表的所有权限

revoke all on db1.* from 'mjj'@"%";  

取消来自远程服务器的mjj用户所有数据库的所有的表的权限
revoke all privileges on '*' from 'mjj'@'%';
```

4. Mysql备份

```
# 备份：数据表结构+数据
mysqdump -u root db1 > db1.sql -p


# 备份：数据表结构
mysqdump -u root -d db1 > db1.sql -p

#导入现有的数据到某个数据库
#1.先创建一个新的数据库
create database db10;
# 2.将已有的数据库文件导入到db10数据库中
mysqdump -u root -d db10 < db1.sql -p
```

