---
title: "Linux开机自启设置"
date: 2020-09-21T19:45:48+08:00
author: CoderLeftEar
categories: ["Linux"]
tags: ["Linux"]
draft: true
---

# Linux开机自启设置

## 一、添加开机自启服务

在[CentOS](http://www.linuxidc.com/topicnews.aspx?tid=14) 7中添加开机自启服务非常方便，只需要两条命令(以Jenkins为例)：

```
systemctl enable jenkins.service #设置jenkins服务为自启动服务
sysstemctl start  jenkins.service #启动jenkins服务
```

## 二、添加开机自启脚本

在centos7中增加脚本有两种常用的方法，以脚本autostart.sh为例：

```shell
#!/bin/bash
#description:开机自启脚本
/usr/local/tomcat/bin/startup.sh  #启动tomcat
```

### 方法一

1、赋予脚本可执行权限（/opt/script/autostart.sh是你的脚本路径）

```
chmod +x /opt/script/autostart.sh
```

2、打开/etc/rc.d/rc.local或/etc/rc.local文件，在末尾增加如下内容

```
su - user -c '/opt/script/autostart.sh'
```

3、在centos7中，/etc/rc.d/rc.local的权限被降低了，所以需要执行如下命令赋予其可执行权限

```
chmod +x /etc/rc.d/rc.local
```

### 方法二

1、将脚本移动到/etc/rc.d/init.d目录下

```
mv  /opt/script/autostart.sh /etc/rc.d/init.d
```

2、增加脚本的可执行权限

```
chmod +x  /etc/rc.d/init.d/autostart.sh
```

3、添加脚本到开机自动启动项目中

```
cd /etc/rc.d/init.d
chkconfig --add autostart.sh
chkconfig autostart.sh on
```