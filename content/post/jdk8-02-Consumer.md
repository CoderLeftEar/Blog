---
title: "Jdk8-02-Consumer"
date: 2019-08-09T11:10:57+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# Consumer  
---

Consumer：`接收一个参数，无返回值`。

```
List<String> list = Arrays.asList("a", "b", "c", "d", "e");  
Consumer<String> consumer = item -> System.out.println(item);  
list.forEach(consumer);  
```

