---
title: "Jdk8-10-Stream笔记02"
date: 2019-09-03T09:26:35+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# Stream笔记02
----------------

## 1.

添加数据到List集合中：
```
Student s1 = new Student("zhangsan", 80);
Student s2 = new Student("lisi", 90);
Student s3 = new Student("wangwu", 100);
Student s4 = new Student("zhaoliu", 90);
Student s5 = new Student("zhaoliu", 50);

List<Student> students = Arrays.asList(s1, s2, s3, s4, s5);
```

`分数最小值的学生：`
```
students.stream().
//                min(Comparator.comparingInt(student -> student.getScore())).ifPresent(System.out::println);
                collect(Collectors.minBy(
                        Comparator.comparingInt(student -> student.getScore())
                )).ifPresent(System.out::println);

结果：
Student(name=zhaoliu, score=50)
```

`获得分数最大值的学生：`
```
students.stream().
        collect(Collectors.maxBy(
                Comparator.comparingInt(student -> student.getScore())
        )).ifPresent(System.out::println);
        
结果：
Student(name=wangwu, score=100)
```

`平均分：`
```
System.out.println(students.stream().
        collect(Collectors.averagingDouble(Student::getScore)));

结果：
82.0
```

`总分`
```
System.out.println(students.stream().
        collect(Collectors.summingDouble(Student::getScore)));

结果：
410.0
```

`统计信息`
```
DoubleSummaryStatistics summaryStatistics = students.stream().
        collect(Collectors.summarizingDouble(Student::getScore));
System.out.println(summaryStatistics);

结果：
DoubleSummaryStatistics{count=5, sum=410.000000, min=50.000000, average=82.000000, max=100.000000}
```

`joining拼接：`

- 将每一个学生直接映射成学生姓名, 直接拼接, 不添加任何连接符号
- 将每个学生姓名拼接并用','连接
- 当Collectors.joining()是三个参数时：
    - 分隔符
    - 前缀
    - 后缀

```
System.out.println(students.stream().map(Student::getName).
        collect(Collectors.joining()));
System.out.println(students.stream().map(Student::getName).
        collect(Collectors.joining(",")));
System.out.println(students.stream().map(Student::getName).
        collect(Collectors.joining(",", "<begin> ", " <end>")));

结果：
zhangsanlisiwangwuzhaoliuzhaoliu
zhangsan,lisi,wangwu,zhaoliu,zhaoliu
<begin> zhangsan,lisi,wangwu,zhaoliu,zhaoliu <end>
```

`分区：先根据分数，再根据姓名`











