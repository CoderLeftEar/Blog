---
title: "Jdk8-03-Function"
date: 2019-08-09T11:09:07+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# Function  
---

Function：`接收一个参数返回一个结果。`  
BiFunction：`接收两个参数返回一个结果。`  

创建对象：  
> FunctionDemo functionDemo = new FunctionDemo();  

<br>

在内部使用：  
```  
System.out.println(functionDemo.convert(2, value -> { return value;}));
System.out.println(functionDemo.convert(3, value -> { return ((int)value * (int)value);}));

public Object convert1(Object obj, Function<Object, Object> function){
    return function.apply(obj);
}
```

将lambda抽取出来,加法：  
```
Function<Object, Object> function = value -> ((int)value + (int)value);
Object obj = functionDemo.convert1(5, function);
System.out.println(obj);

public Object convert1(Object obj, Function<Object, Object> function){
    return function.apply(obj);
}

```
compose的使用：  
```
System.out.println(functionDemo.convert2(5, value -> (int)value + 1, value -> (int)value * (int)value));

public Object convert2(Object obj, Function<Object, Object> function1, Function<Object, Object> function2){
    //compose原理
    //return function2.apply(function1.apply(obj));
    //调用compose方法
    return function2.compose(function1).apply(obj);
}
```

andThen的使用：
```  
System.out.println(functionDemo.convert3(4, value -> (int)value + 1, value -> (int)value * (int)value));

public Object convert3(Object obj, Function<Object, Object> function1, Function<Object, Object> function2){
    //compose原理
    //return function1.apply(function2.apply(obj));
    //调用compose方法
    return function1.compose(function2).apply(obj);
}
```

Function的identity方法使用：  
```
System.out.println(functionDemo.convert5("hello world", value -> value));

public Object convert5(Object obj, Function<Object, Object> function){
    return Function.identity().apply(obj);
}
```

BiFunction的apply方法使用：  
```
System.out.println(functionDemo.convert4(2, 3, (value1, value2) -> (int)value1 + (int)value2));

public Object convert4(Object obj1, Object obj2, BiFunction<Object, Object, Object> biFunction){
    return biFunction.apply(obj1, obj2);
}
```

BiFunction的andThen：  
```
System.out.println(functionDemo.convert6(2, 3, value -> (int)value * (int)value, (value1, value2) -> (int)value1 + (int)value2));

public Object convert6(Object obj1, Object obj2, Function<Object, Object> biFunction1, BiFunction<Object, Object, Object> biFunction2){
    //BiFunction的andThen原理
    return biFunction1.apply(biFunction2.apply(obj1, obj2));
}
```


