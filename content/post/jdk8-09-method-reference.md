---
title: "Jdk8-09-Method Reference"
date: 2019-09-02T23:31:04+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# method reference
------------------

Student类：  
```
class Student {
    private String name;
    private int score;

    public static int compareStudentByName1(Student s1, Student s2) {
        return s1.getName().compareTo(s2.getName());
    }

    public static int compareStudentByScore1(Student s1, Student s2) {
        return s1.getScore() - s2.getScore();
    }

    public static int compareStudentByName2(Student s1, Student s2) {
        return s1.getName().compareTo(s2.getName());
    }

    public static int compareStudentByScore2(Student s1, Student s2) {
        return s1.getScore() - s2.getScore();
    }
}
```

Student1类：  
```
class Student1 {
    public int compareStudentByName(Student s1, Student s2) {
        return s1.getName().compareTo(s2.getName());
    }

    public int compareStudentByScore(Student s1, Student s2) {
        return s1.getScore() - s2.getScore();
    }
}
```

向Student中添加数据，并转换为List集合：  
```
Student s1 = new Student("zhangsan", 30);
Student s2 = new Student("lisi", 80);
Student s3 = new Student("wangwu", 50);

List<Student> list = Arrays.asList(s1, s2, s3);
```


## `方法引用的四种使用方式：`

### 1.`类名 :: 静态方法名：`

`lambda表达式实现：`比较Student中name，进行排序
```
list.sort((value1, value2) -> Student.compareStudentByName1(value1, value2));
list.forEach(item -> System.out.println(item.getName()));

答案：  
lisi
wangwu
zhangsan
```

`方法引用实现：`
```
list.sort(Student :: compareStudentByName1);
list.forEach(item -> System.out.println(item.getName()));

答案：  
lisi
wangwu
zhangsan
```

### 2.`引用名(对象名) :: 实例方法名：`

`lambda表达式实现：`需要创建对象实例
```
Student1 student = new Student1();

list.sort((value1, value2) -> student.compareStudentByScore(value1, value2));
list.forEach(item -> System.out.println(item.getScore()));

答案：  
30
50
80
```

`方法引用实现：`需要创建对象实例
```
Student1 student = new Student1();

list.sort(student :: compareStudentByScore);
list.forEach(item -> System.out.println(item.getScore()));

答案：  
30
50
80
```

### 3.`类名 :: 实例方法名：`

```
list.sort(Student :: compareStudentByScore2);
list.forEach(item -> System.out.println(item.getScore()));

答案： 
30
50
80
```


### 4.`(构造方法引用)类名 :: new：`


`lambda：`
- `String::new / () -> new String()`（Supplier类型：无参，一个返回）：初始化新创建的对象，使其表示空字符串。
public String() {
&emsp;&emsp;this.value = "".value;
}
```
MethodReferenceTest mf = new MethodReferenceTest();
//lambda
System.out.println(mf.getString1(() -> new String()));
//method reference
System.out.println(mf.getString1(String::new));

public String getString1(Supplier<String> supplier){
    return supplier.get() + "hello";
}

答案：
hello
hello
```

`(构造方法引用)类名 :: new：`
- 在getString2中，`s -> s / String::new`（作用：初始化新创建的对象，初始化新创建的{@code String}对象，使其表示与参数相同的字符序列;换句话说，新创建的字符串是参数字符串的副本。除非需要显式复制{@code original}，否则无需使用此构造函数，因为字符串是不可变的。）参数类型为Function（一个参数，一个返回），直接传入`行为`，然后由function你调用apply(str)方法
public String(String original) {
&emsp;&emsp;this.value = original.value;
&emsp;&emsp;this.hash = original.hash;
}
```
MethodReferenceTest mf = new MethodReferenceTest();
//lambda
System.out.println(mf.getString2("world1", s -> s));
//method reference
System.out.println(mf.getString2("world2", String::new));

public String getString2(String str, Function<String, String> function){
    return function.apply(str);
}

答案：
world1
world2
```


