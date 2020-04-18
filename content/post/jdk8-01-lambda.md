---
title: "Jdk8-01-Lambda"
date: 2019-08-09T11:08:54+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# Lambda  
---

关于函数式接口：
- 如果一个接口只有一个抽象方法，那么该接口就是一个函数式接口  
- 如果我们在某个接口上声明了FunctionalInterface注解，那么编译器就会按照函数式接口的定义来要求该接口  
- 如果某个接口只有一个抽象方法，但我们并没有给改接口声明FunctionalInterface注解，那么编译器依旧会将该接口看作是函数式接口  

<br>

> 如果函数式接口中存在Object类中的的方法时，不会算入总方法数，会看作基础Object类  


案例：  
```
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
```
```
System.out.println("--------foreach遍历--------");
//foreach遍历
for (Integer num : list) {
    System.out.println(num);
}
```
```
System.out.println("--------for循环--------");
//for循环
for(int i = 0; i < list.size(); i ++){
    System.out.println(list.get(i));
}
```
```
System.out.println("--------内部类--------");
list.forEach(new Consumer<Integer>() {
    @Override
    public void accept(Integer integer) {
        System.out.println(integer);
    }
});
```
```
System.out.println("--------Lambda--------");
//Lambda
list.forEach(i -> System.out.println(i));
```

