---
title: "001-大话设计模式-单例模式"
date: 2019-07-02T18:11:32+08:00
author: CoderLeftEar
categories: ["设计模式"]
tags: ["设计模式"]
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
| isolation        | 事务的隔离度，默认值采用 DEFAULT。                           |
| timeout          | 事务的超时时间，默认值为-1。如果超过该时间限制但事务还没有完成，则自动回滚事务。 |
| read-only        | 指定事务是否为只读事务，默认值为 false；为了忽略那些不需要事 |
| rollback-for     | 用于指定能够触发事务回滚的异常类型，如果有多个异常类型需要指定，各类型之间可以通过逗号分隔。 |
| no-rollback- for | 抛出 no-rollback-for 指定的异常类型，不回滚事务。            |





## 什么是事务传播行为？

事务传播行为用来描述由某一个事务传播行为修饰的方法被嵌套进另一个方法的时事务如何传播。



## Spring中七种事务传播行为：

| 事务传播行为类型          | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| PROPAGATION_REQUIRED      | 如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是最常见的选择。 |
| PROPAGATION_SUPPORTS      | 支持当前事务，如果当前没有事务，就以非事务方式执行。         |
| PROPAGATION_MANDATORY     | 使用当前的事务，如果当前没有事务，就抛出异常。               |
| PROPAGATION_REQUIRES_NEW  | 新建事务，如果当前存在事务，把当前事务挂起。                 |
| PROPAGATION_NOT_SUPPORTED | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。   |
| PROPAGATION_NEVER         | 以非事务方式执行，如果当前存在事务，则抛出异常。             |
| PROPAGATION_NESTED        | 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。 |

#### 保证同一个事务中：

- PROPAGATION_REQUIRED 支持当前事务，如果不存在 就新建一个(默认)
- PROPAGATION_SUPPORTS 支持当前事务，如果不存在，就不使用事务
- PROPAGATION_MANDATORY 支持当前事务，如果不存在，抛出异常

#### 保证没有在同一个事务中：

- PROPAGATION_REQUIRES_NEW 如果有事务存在，挂起当前事务，创建一个新的事务
- PROPAGATION_NOT_SUPPORTED 以非事务方式运行，如果有事务存在，挂起当前事务
- PROPAGATION_NEVER 以非事务方式运行，如果有事务存在，抛出异常
- PROPAGATION_NESTED 如果当前事务存在，则嵌套事务执行



