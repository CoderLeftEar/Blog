---
title: "Spring事务、传播行为"
date: 2019-07-02T18:11:32+08:00
author: CoderLeftEar
categories: ["Mysql"]
tags: ["Mysql"]
draft: true
---

# Spring事务、传播行为

![](http://5b0988e595225.cdn.sohucs.com/q_70,c_zoom,w_640/images/20200408/9a2179111b634cbd9730b9b99f9d7cfc.jpeg)



## Spring事务的几种实现方式

- 编程式事务管理对基于 POJO 的应用来说是唯一选择。我们需要在代码中调用beginTransaction()、commit()、rollback()等事务管理相关的方法，这就是编程式事务管理。
- 基于 TransactionProxyFactoryBean的声明式事务管理
- 基于 @Transactional 的声明式事务管理
- 基于Aspectj AOP配置事务

https://blog.csdn.net/mufeng633/article/details/88552245 



## Spring Boot的事务管理注解

 https://blog.csdn.net/u010963948/article/details/79208328 

在主程序类上标注：

开启事务管理器：

- @EnableTransactionManagement  

xml配置方式：

- <tx:annotation-driven />

开启后在需要使用事务的类或方法上标注@Transactional即可。 

存在多个事务管理器 ：

- 使用value具体指定使用哪个事务管理器：@Transactional(value="xxxxx")



## 注解@Transaction 用法

也可标注在类上，该类中的所有公共方法都配置相同的事务属性。

| 属性名           | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| value            | 当在配置文件中有多个 TransactionManager , 可以用该属性指定选择哪个事务管理器。 |
| propagation      | 事务的传播行为，默认值为 REQUIRED。                          |
| isolation        | 事务的隔离级别，默认值采用 DEFAULT。                         |
| timeout          | 事务的超时时间，默认值为-1。强制回滚前最多可以等待的时间。   |
| read-only        | 指定事务是否为只读事务，默认值为 false；为了忽略那些不需要事 |
| rollback-for     | 用于指定能够触发事务回滚的异常类型，如果有多个异常类型需要指定，各类型之间可以通过逗号分隔。 |
| no-rollback- for | 抛出 no-rollback-for 指定的异常类型，不回滚事务。            |

### propagation --- 七种事务传播行为：

| 事务传播行为类型 | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| REQUIRED         | 默认。使用调用者的事务。                                     |
| REQUIRES_NEW     | 不使用调用者的事务，挂起当前事务，新建事务，在内部使用事务。 |
| MANDATORY        | 使用当前的事务，如果当前没有事务，就抛出异常。               |
| SUPPORTS         | 支持当前事务，如果当前没有事务，就以非事务方式执行。         |
| NOT_SUPPORTED    | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。   |
| NEVER            | 以非事务方式执行，如果当前存在事务，则抛出异常。             |
| NESTED           | 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，新建事务并运行。 |





