---
title: "SpringBoot笔记01"
date: 2019-12-10T21:48:06+08:00
author: CoderLeftEar
tags: ["SpringBoot"]
categories: ["SpringBoot"]
draft: true
---

# SpringBoot笔记01

# 一、简介

### 优点：

- 快速创建独立运行的Spring项目以及主流框架集成
- 使用嵌入式的Servlet容器，应用无需打成war包
- starters自动依赖与版本控制
- 大量的自动配置，简化开发，也可以修改默认值
- 无需配置xml，无代码生成，开箱即用
- 准生产环境的运行时应用监控
- 与云计算的天然集成

### 缺点：



## 二、主类注解

### @SpringBootApplication

- @SpringBootConfiguration：
  - @Configuration：表示是一个springboot配置类

- @EnableAutoConfiguration：以前需要配置的，现在自动配置

  - @AutoConfigurationPackage：自动配置包，将主配置类所在包及下面所有包扫描

    - @Import(AutoConfigurationPackages.Registrar.class)：自动扫描包（com.springboot）

  - @Import(AutoConfigurationImportSelector.class)：需要导入那些组件的选择器

    