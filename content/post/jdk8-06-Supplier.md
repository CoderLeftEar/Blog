---
title: "Jdk8-06-Supplier"
date: 2019-08-09T11:09:35+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# Supplier  
---

Supplier：`不接受参数，返回结果，用来创建对象。`  
Supplier是个接口，有一个get()方法，创建完对象后，如要调用需要先调用自身的get()。  


Student类：
```
@Data
@NoArgsConstructor
class Student{
    private String name = "张三";

}
```

使用内部类：
```
Supplier<Student> supplier = new Supplier<Student>() {
    @Override
    public Student get() {
        return new Student();
    }
};
System.out.println(supplier.get().getName());
```

使用Lambda表达式：
```
supplier = () -> new Student();
System.out.println(supplier.get().getName());
```


使用方法引用：
```
supplier = Student::new;
System.out.println(supplier.get().getName());
```


