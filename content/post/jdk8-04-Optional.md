---
title: "Jdk8-04-Optional"
date: 2019-08-09T11:10:02+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# Optional  
---

Optional：主要解决空指针异常。使用of()、ofNullable()创建包含值的optional。  
of()：传入null，会报空指针异常。
ofNullable()：可以穿null，不会报异常。


> Optional<String> optional = Optional.ofNullable("hello");

使用isPresent方法判断optional是否为空，和传统做法差不多，不推荐使用。
```
if(optional.isPresent()) {
    System.out.println(optional.get());
}
```
推荐使用此种方法
```
optional.ifPresent(item -> System.out.println(item));
```
---

orElse：如果optional中有值，返回该值，否则返回orElse中的值。  
orElseGet：如果optional中有值，返回该值，它会执行作为参数传入Supplier函数式接口，并返回结果。  

```
System.out.println(optional.orElse("world"));
System.out.println(optional.orElseGet(() -> "hahaha"));
```

---
---

雇员和公司类：
```
@Data
@AllArgsConstructor
class Employee{
    private String name;
}

@Data
@AllArgsConstructor
class Company{
    private String name;
    private List<Employee> employees;

    public Company(String name) {
        this.name = name;
    }
}
```

传统方法 --> 判断Company中的employee是否有值
```
Employee emp1 = new Employee("zhangsan");
Employee emp2 = new Employee("lisi");
Company c = new Company("haha");

//员工集合
List<Employee> employees = Arrays.asList(emp1, emp2);
c.setEmployees(employees);

//获取Company中的employee的值
List<Employee> list = c.getEmployees();
if(null != list) {
    System.out.println(list);
} else {
    //new ArrayList<Employee>(); ---> 打印一个空的List
    //System.out.println(new ArrayList<Employee>());
    //返回空的元素列表
    System.out.println(Collections.emptyList());
}
```

函数式编程：  
```
//构造容器,将要获取的值的对象（所在类）放进去
Optional<Company> optional1 = Optional.ofNullable(c);
//使用map映射xxxxxxxxxxx
System.out.println(optional1.map(value -> value.getEmployees()).orElse(Collections.emptyList()));  
//方法引用
System.out.println(optional1.map(Company::getEmployees).orElse(Collections.emptyList()));
```



