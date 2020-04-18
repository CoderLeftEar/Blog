---
title: "Jdk8-07-BinaryOperator"
date: 2019-08-10T09:57:17+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# BinaryOperator  
---

BinaryOperator：是一个继承了BiFunction的接口，本身有两个比较大小的方法。minBy(); 和 maxBy();

> BinaryOperatorTest binaryOperatorTest = new BinaryOperatorTest();  

> `<T>` BinaryOperator<T> minBy(Comparator<? super T> comparator) 

四则运算
```
BinaryOperator<Integer> binaryOperator = (value1, value2) -> value1 * value2;
//封装在方法中，传参
System.out.println(binaryOperatorTest.operatorFun(3, 4, binaryOperator));
//直接调用apply方法调用
System.out.println(binaryOperator.apply(3, 4));

//四则运算
public Integer operatorFun(int a, int b, BinaryOperator<Integer> binaryOperator){
    return binaryOperator.apply(a, b);
}

答案：12，12
```

大小比较：第三个参数传一个lambda，比较长度还是大小，写对应的lambda
```
System.out.println(binaryOperatorTest.operatorFunMin("abbbbbb", "bbb", (value1, value2) -> value1.compareTo(value2)));
System.out.println(binaryOperatorTest.operatorFunMin("abbbbbb", "bbb", (value1, value2) -> value1.length() - value2.length()));

public String operatorFunMin(String a, String b, Comparator<String> comparator){
    return BinaryOperator.minBy(comparator).apply(a, b);
}

答案：abbbbbb，bbb
```