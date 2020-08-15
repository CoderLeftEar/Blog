---
title: "Linux安装tomcat、mysql"
date: 2020-08-15T17:14:48+08:00
author: CoderLeftEar
categories: ["Linux"]
tags: ["Linux"]
draft: true
---



# Linux安装tomcat、mysql

## [linux上安装mysql5.7](https://www.cnblogs.com/daemon-/p/9009360.html)

### 1、下载tar包，这里使用wget从官网下载

wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz

### 2、将mysql安装到/usr/local/mysql下

- 解压

> tar -xvf mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz

- 移动

> mv mysql-5.7.22-linux-glibc2.12-x86_64 /usr/local/

- 重命名

mv /usr/local/mysql-5.7.22-linux-glibc2.12-x86_64 /usr/local/mysql

### 3、新建data目录

> mkdir /usr/local/mysql/data

### 4、新建mysql用户、mysql用户组

```shell
# mysql用户组
groupadd mysql
# mysql用户
useradd mysql -g mysql
```

### 5、将/usr/local/mysql的所有者及所属组改为mysql

> chown -R mysql.mysql /usr/local/mysql

### 6、配置

> /usr/local/mysql/bin/mysql_install_db --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data

- 如果出现以下错误：

```
2018-07-14 06:40:32 [WARNING] mysql_install_db is deprecated. Please consider switching to mysqld --initialize
2018-07-14 06:40:32 [ERROR]   Child process: /usr/local/mysql/bin/mysqldterminated prematurely with errno= 32
2018-07-14 06:40:32 [ERROR]   Failed to execute /usr/local/mysql/bin/mysqld --bootstrap --datadir=/usr/local/mysql/data --lc-messages-dir=/usr/local/mysql/share --lc-messages=en_US --basedir=/usr/local/mysql
-- server log begin --

-- server log end --
```

- 则使用以下命令：

```
/usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data --initialize
```

- 如果出现以下错误：

```
/usr/local/mysql/bin/mysqld: error while loading shared libraries: libnuma.so.1: cannot open shared object file: No such file or directory
```

- 则执行以下命令：

> yum -y install numactl

- 完成后继续安装：

```
/usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data --initialize
```

- 编辑/etc/my.cnf

```
[mysqld]
datadir=/usr/local/mysql/data
basedir=/usr/local/mysql
socket=/tmp/mysql.sock
user=mysql
port=3306
character-set-server=utf8# 取消密码验证skip-grant-tables
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# skip-grant-tables
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

### 7、开启服务

- 将mysql加入服务

> cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql

- 开机自启

> chkconfig mysql on

- 开启

> service mysql start

### 8、设置密码

- 登录（由于/etc/my.cnf中设置了取消密码验证，所以此处密码任意）

> /usr/local/mysql/bin/mysql -u root -p

- 操作mysql数据库

> \>>use mysql;

- 修改密码

> \>>update user set authentication_string=password('你的密码') where user='root';

> \>>flush privileges;

> \>>exit;

### 9、将/etc/my.cnf中的skip-grant-tables删除

### 10、登录再次设置密码（不知道为啥如果不再次设置密码就操作不了数据库了）

> /usr/local/mysql/bin/mysql -u root -p

> \>>ALTER USER 'root'@'localhost' IDENTIFIED BY '修改后的密码';

> \>>exit;

### 11、允许远程连接

> /usr/local/mysql/bin/mysql -u root -p

> \>>use mysql;

> \>>update user set host='%' where user = 'root';

> \>>flush privileges;

> \>>eixt;

### 12、添加快捷方式

> ln -s /usr/local/mysql/bin/mysql /usr/bin



## 解决Linux下MySQL登录ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using passwor)问题...

### 两种方案：

#### 一：

1. 修改mysql配置文件：vim /etc/my.cnf

2. 找到[mysqld]下添加 skip-grant-tables 配置

3. 重启服务service mysqld restart

#### 二：

通过安装mysql默认密码来进行登陆，输入 **cat /var/log/mysqld.log  | grep "temporary password"**  获取到默认密码，再进行登陆修改密码（不修改的话会提示"**ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement**"）。

1: SET PASSWORD = PASSWORD('**newpassword**');

2: ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;

3: flush privileges;

除了红色newpassword自己设置以外其他的正常输出就行



## 安装JDK1.8

> unzip -o jdk8.zip -d /data/jdk8 #解压
>
> vi /etc/profile

```
##按 i 插入内容
##在该文件最下面添加
##按 Esc + wq 保存

export JAVA_HOME=/data/jdk8
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

> 重新加载配置：source /etc/profile
>
> java -version ##测试安装是否成功
>
> 提示权限不够，需要增加授权：chmod -R 777 /data/runten/jdk/

## 安装tomcat9

> unzip -o tomcat9.zip -d /data/tomcat9 ##解压tomcat

##### 测试启动tomcat9 （可以跳过）

> cd /data/tomcat9/bin/

##### 配置开机自动启动

> vi /etc/init.d/tomcat9

```
#!/bin/bash
# chkconfig: 345 90 10
# description: Tomcat9 Start Stop Restart
# processname: tomcat9

JAVA_HOME=/data/jdk8
export JAVA_HOME
PATH=$JAVA_HOME/bin:$PATH
export PATH
CATALINA_HOME=/data/tomcat9


RETVAL=0

start(){
  checkrun 
  if [ $RETVAL -eq 0 ]; then 
    echo "-- Starting tomcat..." 
    $CATALINA_HOME/bin/startup.sh
  else 
    echo "-- tomcat already running" 
  fi  
}
kill(){
  checkrun 
  if [ $RETVAL -eq 1 ]; then 
    read -p "-- Do you really want to kill tomcat progress?[no])" answer 
    case $answer in 
        Y|y|YES|yes|Yes) 
           ps ax --width=1000 |grep tomcat|grep "org.apache.catalina.startup.Bootstrap start" | awk '{printf $1 " "}'|xargs kill -9 
        ;;
        *);;
     esac
  else
      echo "-- No tomcat is running..."
  fi
}
status(){
  checkrun 
  if [ $RETVAL -eq 1 ]; then
    echo "-- tomcat already running"
  else
    echo "-- No tomcat is running..."
  fi
}
checkrun(){
  pstatus=`ps ax --width=1000 |grep tomcat| grep "[o]rg.apache.catalina.startup.Bootstrap start" | awk '{printf $1 " "}' | wc | awk '{print $2}'`
  if [ $pstatus -gt 0 ]; then 
    RETVAL=1 
    return $RETVAL 
  else 
    RETVAL=0 
    return $RETVAL 
  fi  
}

case $1 in

start)

start
exit 0

;;

stop)

kill
exit 0

;;

status)

status
exit 0

;;

logs)
cd $CATALINA_HOME/logs
tail -50f catalina.out
;;

esac

exit 0
```

##### 将tomcat9服务加入到系统服务

> chmod +x /etc/rc.d/init.d/tomcat9 ##增加tomcat9服务控制脚本执行权限
>
> chkconfig --add tomcat9

##### tomcat9 控制命令

> service tomcat9 start ##启动tomcat9
>
> service tomcat9 stop ##停止tomcat9
>
> service tomcat9 status ##状态

### 设置端口开放

##### 端口号：80 81 83 9595 9090(tcp&udp)

```
firewall-cmd --add-port=80-86/tcp --permanent  #添加 80-86端口
firewall-cmd --reload  #刷新
```

#### 对准时间

```
yum install ntp  #需要先安装ntp服务器
ntpdate asia.pool.ntp.org 
/sbin/hwclock --systohc
```