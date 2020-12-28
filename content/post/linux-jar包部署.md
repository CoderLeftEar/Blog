---
title: "jar部署"
date: 2020-12-28T11:14:48+08:00
author: CoderLeftEar
categories: ["Linux"]
tags: ["Linux"]
draft: true
---



# jar部署

## Linux中修改jar包中的配置文件

 **打包以及修改jar包**

```
cd genesys_data_etl
mvn clean package -Poffline -Dmaven.test.skip=true
日志如下：
[INFO] --- maven-jar-plugin:2.6:jar (default-jar) @ genesys_data_etl ---
[INFO] Building jar: /Users/xx/IdeaProjects/genesys_data_etl/target/genesys_data_etl-0.0.1-0SNAPSHOT.jar
生成jar包
此时可以通过命令
java -jar genesys_data_etl-0.0.1-SNAPSHOT.jar 
运行jar包。
```


但是要修改jar包中的配置文件怎么办呢？

### **方式一 通过vim命令直接修改保存jar。超方便。**


1.通过vim命令直接编辑jar
vim xxx.jar 该命令首先会列出全部文件，可以通过输入/abc来搜索，定位到对应的abc文件后回车进入配置文件内进行编辑，:wq保存。



### **方式二 通过jar命令替换jar包中的文件(也可新增)**

1.列出jar包中的文件清单
jar tf genesys_data_etl-0.0.1-SNAPSHOT.jar



2.提取出内部jar包的指定文件
jar xf genesys_data_etl-0.0.1-SNAPSHOT.jar BOOT-INF/classes/realtime/t_ivr_data_bj.json


3.然后可以修改文件
vim BOOT-INF/classes/realtime/t_ivr_data_bj.json


4.更新配置文件到内部jar包.(存在覆盖，不存在就新增)
jar uf genesys_data_etl-0.0.1-SNAPSHOT.jar BOOT-INF/classes/realtime/t_ivr_data_bj.json      


4.1更新内部jar包到jar文件
jar uf genesys_data_etl-0.0.1-SNAPSHOT.jar 内部jar包.jar     


5.可以查看验证是否已经更改
vim genesys_data_etl-0.0.1-SNAPSHOT.jar



### **方式三 解压jar包，修改后重新打包jar**

1.解压
unzip genesys_data_etl-0.0.1-SNAPSHOT.jar 
2.移除jar包,最好备份
rm genesys_data_etl-0.0.1-SNAPSHOT.jar
3.重新打包
 jar -cfM0 new-genesys_data_etl-0.0.1-SNAPSHOT.jar *
 或者
 jar -cvfm0 genesys_data_etl-0.0.1-SNAPSHOT.jar ./META-INF/MANIFEST.MF ./
4.运行
java -jar new-genesys_data_etl-0.0.1-SNAPSHOT.jar


jar命令参数:
-c 创建新的存档
-f 指定存档文件名
-M 不配置配置清单，这样还可以使用maven生成的配置清单也就是MANIFEST.MF
-0 不进行压缩,如果压缩会有问题
-m 指定清单文件
-t 列出归档目录
-x 从档案中提取指定的 (或所有) 文件 
-u 更新现有的归档文件 
-v 在标准输出中生成详细输出 


Linux下如何在不解压jar包查看或修改配置文件
https://jingyan.baidu.com/article/91f5db1b1b66a41c7e05e36c.html
更新jar包里的配置文件
https://www.cnblogs.com/dayou123123/p/6845432.html
修改jar包中的配置文件
https://blog.csdn.net/young_kim1/article/details/50482398  