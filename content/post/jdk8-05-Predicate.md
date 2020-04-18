---
title: "Jdk8-05-Predicate"
date: 2019-08-09T11:09:49+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# Predicate
---

Predicate：`接收一个输入参数，返回一个Boolean值。`  
包括一个返回值是Boolean的test方法、三个默认方法和一个静态isEqual()。  

test里是输入参数：
```
Predicate<String> predicate = P -> P.length() > 5;
System.out.println(predicate.test("Hello World!"));
```

Predicate中的静态方法isEqual：
```
System.out.println(Predicate.isEqual("test").test("tes7t"));
```

and, or和negate使用在filter中：
```
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

Predicate<Integer> p1 = i -> i > 2;
Predicate<Integer> p2 = i -> i < 8;
Predicate<Integer> p3 = i -> i % 2 != 0;

list.stream().filter(p1.and(p2).and(p3.negate())).collect(Collectors.toList()).forEach(System.out::println);
```
答案是：偶数 -- 4, 6