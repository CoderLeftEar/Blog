---
title: "Jdk8-08-Stream笔记01"
date: 2019-08-10T10:14:41+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# Stream笔记01
---

<!-- TOC -->

- [Stream笔记01](#stream笔记01)
    - [例1：过滤name、age (直接采用Stream -> 通过BiFunction调用apply方法 -> 直接传递行为)](#例1过滤nameage-直接采用stream---通过bifunction调用apply方法---直接传递行为)
    - [例2：将List集合中的字符串转换为大写](#例2将list集合中的字符串转换为大写)
    - [例3：Optional<T> reduce(BinaryOperator<T> accumulator)的使用和数组、List与Stream之间的转换](#例3optionalt-reducebinaryoperatort-accumulator的使用和数组list与stream之间的转换)
    - [例4：range、rangeClose的使用](#例4rangerangeclose的使用)
    - [例5：T reduce(T identity, BinaryOperator<T> accumulator)的使用](#例5t-reducet-identity-binaryoperatort-accumulator的使用)
    - [例6：Stream类中toArray、collect（函数式接口）、toSet无序输出、TreeSet自然排序和joining字符串拼接](#例6stream类中toarraycollect函数式接口toset无序输出treeset自然排序和joining字符串拼接)
    - [例7：map和flatMap的使用](#例7map和flatmap的使用)
    - [例8：generate、iterate-limit、使用具体方法mapToInt、summaryStatistics和顺逆序排列](#例8generateiterate-limit使用具体方法maptointsummarystatistics和顺逆序排列)
    - [例9：串行流和并行流](#例9串行流和并行流)
    - [例10：打印第一个长度为5的字符串的长度](#例10打印第一个长度为5的字符串的长度)
    - [例11：flatMap](#例11flatmap)
    - [例12：互相打招呼（flatMap和map的结合使用）](#例12互相打招呼flatmap和map的结合使用)
    - [例13：分组(groupingBy)和分区(partitioningBy)](#例13分组groupingby和分区partitioningby)

<!-- /TOC -->

## 例1：过滤name、age (直接采用Stream -> 通过BiFunction调用apply方法 -> 直接传递行为)

定义Person类，并进行赋值：  
```
    Person person1 = new Person("张三", 20);
    Person person2 = new Person("李四", 30);
    Person person3 = new Person("王五", 40);

    List<Person> persons = new ArrayList<>();
    persons.add(person1);
    persons.add(person2);
    persons.add(person3);
```

创建PersonTest对象：  

> PersonTest personTest = new PersonTest();  

<br>

调用getUsername方法传入需要过滤的`姓名`和`Person`类集合persons，在getUsername方法中通过转换为Stream流对象，使用filter进行过滤（传入的name和person.getName()进行比较），最后返回List集合。
```
System.out.println("==============过滤得到姓名=============");
List<Person> result1 = personTest.getUsername("张三", persons);
result1.forEach(person -> System.out.println(person.getName()));

//==============过滤得到姓名=============
public List<Person> getUsername(String name, List<Person> persons){
    return persons.stream().
           filter(person -> name.equals(person.getName())).
           collect(Collectors.toList());
}
```

与上述一样，传入参数，在getUserage1方法中，可使用上述相同方法，也可定义一个BiFunction传入age和persons，最后调用apply方法，完成年龄过滤。

```
System.out.println("==============过滤得到年龄=============");
List<Person> result2 = personTest.getUserage1(20, persons);
result2.forEach(person -> System.out.println(person.getAge()));

//==============过滤得到年龄=============
public List<Person> getUserage1(Integer age, List<Person> persons){
    //定义BiFunction，方便调用
    BiFunction<Integer, List<Person>, List<Person>> biFunction = (ageOfPerson, personList) -> {
        return persons.stream().filter(person -> person.getAge() > ageOfPerson).
                collect(Collectors.toList());
    };
    return biFunction.apply(age, persons);
}
```

直接传递行为，可用性更高。

```
System.out.println("==============过滤得到年龄，传参数BiFunction=============");
List<Person> result3 = personTest.getUserage2(30, persons, 
        (ageOfPerson, personList) -> 
                persons.stream().
                        filter(person -> person.getAge() <= ageOfPerson).
                        collect(Collectors.toList()));
result3.forEach(person -> System.out.println(person.getAge()));

//==============过滤得到年龄，传参数BiFunction=============
public List<Person> getUserage2(Integer age,
                                List<Person> persons,
                                BiFunction<Integer, List<Person>, List<Person>> biFunction){
    return biFunction.apply(age, persons);
}
```

答案：  
```
==============过滤得到姓名=============
张三
==============过滤得到年龄=============
30
40
==============过滤得到年龄，传参数BiFunction=============
20
30
```

------------------------------------

## 例2：将List集合中的字符串转换为大写  

List集合中的元素：  
> List<String> list = Arrays.asList("hello", "world");

lambda表达式：list1.addAll(list)是将list对象向list1中添加两次，add是将每个元素转换为大写后添加到list1中。

```
list.forEach(item -> System.out.println(item.toUpperCase()));

或：  
//将list中的数据添加到list1中
List<String> list1 = new ArrayList<>();
//这是将list对象向list1中添加两次
//list.forEach(item -> list1.addAll(list));

//这是将list对象中的数据向list1中添加
list.forEach(item -> list1.add(item.toUpperCase()));
list1.forEach(item -> System.out.println(item));
```

使用Stream流对象，使用map京每个元素进行映射为大写后直接打印。分别采用lambda和method reference进行完成的。

```
//lambda
list.stream().map(item -> item.toUpperCase()).forEach(item -> System.out.println(item));
//方法引用
list.stream().map(String :: toUpperCase).forEach(System.out :: println);
```

-----------------------------------

## 例3：Optional<T> reduce(BinaryOperator<T> accumulator)的使用和数组、List与Stream之间的转换

reduce方法返回Optional，传一个accumulator新的可变容器，s表示新创建的可变容器，str1表示当前流中的元素。
concat方法的作用：将str1追加到s当中。
```
Stream<String> stream1 = Stream.of("zhangsan", "lisi");

//stream1.reduce(String::concat).ifPresent(item -> System.out.println(item));
stream1.reduce((s, str1) -> s.concat(str1)).ifPresent(item -> System.out.println(item));
```

数组转换为List集合，再转换为Stream

```
String[] str = new String[]{"a", "b", "c"};

//Stream.of的源码就是调用的就是Arrays.stream(str);
Stream stream2 = Stream.of(str);
Stream stream3 = Arrays.stream(str);

//转换成List集合
List<String> list = Arrays.asList(str);

//list直接调用stream()返回Stream
Stream stream4 = list.stream();

System.out.println(stream4.collect(Collectors.toList()));
```

答案：
```
zhangsanlisi
[a, b, c]
```

-------------------------------

## 例4：range、rangeClose的使用  

range：左闭右开  
rangeClose：左闭右闭

```
IntStream.of(1, 2, 3, 4, 5).forEach(System.out::println);

System.out.println("====================");
IntStream.range(1, 5).forEach(System.out::println);

System.out.println("====================");
IntStream.rangeClosed(1, 5).forEach(System.out::println);
```

答案：
```
1
2
3
4
5
====================
1
2
3
4
====================
1
2
3
4
5
```

---------------------------------------

## 例5：T reduce(T identity, BinaryOperator<T> accumulator)的使用 

reduce的第一个参数是`累加函数的恒等值`（？？？），第二个参数是新创建的accumulator可变容器。

```
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
System.out.println(list.stream().map(s -> s * 2).reduce(0, Integer::sum));
System.out.println(list.stream().map(s -> s * 2).reduce(0, (a, b) -> a + b));
```

答案：
```
30  
30  
```

-------------------------------------------

## 例6：Stream类中toArray、collect（函数式接口）、toSet无序输出、TreeSet自然排序和joining字符串拼接



> Stream<String> stream = Stream.of("hello", "world", "hahaha", "welcome");

使用`<A> A[] toArray(IntFunction<A[]> generator)`，根据提供长度生成新的数组。

```
        //toArray:生成所需类型和提供长度的新数组的函数
        //lambda
        String[] streamStr1 = stream.toArray(length -> new String[length]);
        //方法引用
//        String[] streamStr1 = stream.toArray(String[]::new);
        Arrays.asList(streamStr1).forEach(System.out::println);

//        String[] streamStr2 = stream.toArray(new IntFunction<String[]>() {
//            @Override
//            public String[] apply(int length) {
//                return new String[value];
//            }
//        });
//        Arrays.asList(streamStr2).forEach(System.out::println);

答案：
hello
world
hahaha
welcome
```

`<R> R collect(Supplier<R> supplier, BiConsumer<R, ? super T> accumulator, BiConsumer<R, R> combiner)`的使用。关于collect中各参数的作用：  
lambda:   
1. 创建一个新的结果容器
2. 操作list集合中的数据，放到新创建的结果容器中
3. 将创建的结果容器合并  

method reference:   
1. 创建一个新的结果容器
2. 操作list集合中的数据，放到新创建的结果容器中
3. 将创建的结果容器合并

```
System.out.println("=============collect---lambda============");
List<String> list1 = stream.
        collect(() -> new ArrayList<>(),
                (theList, item) -> theList.add(item.toUpperCase()),
                (newList, theList) -> newList.addAll(theList));
list1.forEach(System.out::println);

System.out.println("=============collect---method reference============");
List<String> list2 = stream.
        collect(LinkedList::new,
                LinkedList::add,
                LinkedList::addAll);
list2.forEach(System.out::println);

答案：  
=============collect---lambda============
HELLO
WORLD
HAHAHA
WELCOME
=============collect---method reference============
HELLO
WORLD
HAHAHA
WELCOME
```

Collectors中的toSet、toCollection和joining：  
1. 使用Collectors.toSet可进行无序输出
2. Collectors.toCollection：按输入元素顺序将元素累积到一个新的Collection中，再使用TreeSet进行自然排序
3. 使用joining进行字符串拼接

```
System.out.println("=============Set无序输出============");
//1、Collectors自带的toSet();
Set<String> set = stream.collect(Collectors.toSet());
//2、将数据放进TreeSet中
Set<String> set = stream.collect(Collectors.toCollection(TreeSet::new));
//Set<String> set = stream.collect(Collectors.toCollection(() -> new TreeSet<>()));
set.forEach(System.out::println);

System.out.println("=============字符串拼接============");
String set1 = stream.collect(Collectors.joining());
System.out.println(set1);
```

---------------------------------------

## 例7：map和flatMap的使用

map：将每个item进行映射
flatMap：  

- 可将list使用stream()（stream方法在List类的父类Collection中）转换为Stream类型  
- Stream.of(Arrays.asList(1, 2), ...)中包含的是List数据集合，使用flatMap可将所有List集合打平，就像包所有List集合中的数据放在一个List集合中

```
List<String> list = Arrays.asList("hello", "world", "helloworld");

System.out.println("==============flatMap==============");
//lambda
//list = list.stream().collect(() -> new ArrayList<>(),
//                             (list2, item) -> list2.add(item.toUpperCase()),
//                             (list1, list2) -> list1.addAll(list2));
        
//method reference
list.stream().map(String::toUpperCase).
        collect(Collectors.toList()).
        forEach(System.out::println);

System.out.println("==============flatMap==============");
Stream<List<Integer>> stream = Stream.of(Arrays.asList(1, 2), Arrays.asList(2, 3, 4), Arrays.asList(5, 6));
//flatMap是将stream中的三个List集合平铺到一个里面，map起运算作用
stream.flatMap(theList -> theList.stream()).map(item -> item * item).
        collect(Collectors.toList()).forEach(System.out::println);


答案：  
==============flatMap==============
HELLO
WORLD
HELLOWORLD
==============flatMap==============
1
4
4
9
16
25
36
```

-----------------------------------------


## 例8：generate、iterate-limit、使用具体方法mapToInt、summaryStatistics和顺逆序排列


`generate`中是一个Supplier对象(不管是什么，返回一个结果)，不接受参数，返回一个值.

```
Stream<String> stream = Stream.generate(() -> UUID.randomUUID().toString());
//Stream<String> stream = Stream.generate(UUID.randomUUID()::toString);
stream.findFirst().ifPresent(System.out::println);
//stream.findFirst().ifPresent((item) -> System.out.println(item));
```

不使用limit便会无限制的加下去
```
Stream.iterate(1, item -> item + 2).limit(5).forEach(System.out::println);
```


**练习**：找出大于2的元素，然后将每个元素乘以2，然后忽略流中前两个元素，再取流中前两个元素，最后求取出的元素总和。

可使用`Stream.iterate(1, item -> item + 2).limit(6)`。

对于具体的知道类型的元素，使用具体的方法（mapToInt），避免使用map造成自动装箱、拆箱，造成性能损耗。

求最小值，min方法返回的时一个OptionalInt类型，所以需要使用ifPresent。

数据：
> Stream<Integer> stream1 = Stream.of(1, 3, 5, 7, 9, 11);
> //Stream<Integer> stream1 = Stream.iterate(1, item -> item + 2).limit(6);

```
//使用mapToInt，避免使用map造成自动装箱、拆箱，会造成性能损耗
System.out.println(stream1.filter(item -> item > 2).
        mapToInt(item -> item * 2).skip(2).limit(2).sum()
);

//求最小值，min方法返回的时一个OptionalInt类型，所以需要使用ifPresent
stream1.filter(item -> item > 2).mapToInt(item -> item * 2).min().ifPresent(System.out::println);

答案：  
32
6
```

IntStream中的summaryStatistics方法用于描述流元素的各种摘要数据（sum, max, min, avg, count）  
该方法是IntSummaryStatistics类型，用于收集统计信息(如count、min、max、sum和average)的状态对象。

```
IntSummaryStatistics intSummaryStatistics = stream1.
                filter(item -> item > 2).mapToInt(item -> item * 2).
                skip(2).limit(2).summaryStatistics();

System.out.println(intSummaryStatistics.getSum());
System.out.println(intSummaryStatistics.getMax());
System.out.println(intSummaryStatistics.getMin());
System.out.println(intSummaryStatistics.getAverage());
System.out.println(intSummaryStatistics.getCount());

答案：  
32
18
14
16.0
2
```

***关于顺序、逆序排列***

```
List<Integer> list = Arrays.asList(6, 2, 3, 1, 4);

//Collections.sort(list, (o1, o2) -> o2.compareTo(o1));

Collections.sort(list, Integer::compareTo);
System.out.println(list);

答案：  
[6, 4, 3, 2, 1]
[1, 2, 3, 4, 6]
```

------------------------------

## 例9：串行流和并行流  

串行流(`stream()`)：  
```
开始排序：
耗时：4125
5000000
```

并行流(`parallelStream()`)： 
``` 
开始排序：
耗时：2185
5000000
```

`很明显，并行流快，但并不是并行流就一定比串行流快`

```
List<String> list = new ArrayList<>();
//创建UUID并添加到list集合中
for (int i = 0; i < 5000000; i ++){
        list.add(UUID.randomUUID().toString());
}

long startTime = System.currentTimeMillis();

System.out.println("开始排序：");

//返回UUID生成的个数：5000000
//串行流
//long count = list.stream().sorted().count();
//并行流
long count = list.parallelStream().sorted().count();

long endTime = System.currentTimeMillis();

System.out.println("耗时：" + (endTime - startTime));

System.out.println(count);
```

-----------------------------

## 例10：打印第一个长度为5的字符串的长度  

我自己看注释应该看的懂，不写了 `^_^`

```
List<String> list = Arrays.asList("hello1", "world0", "hello world");

//将item直接映射为item.length()，在执行过滤条件是否item长度为5，找到第一个
OptionalInt first = list.stream().mapToInt(item -> item.length())
        .filter(item -> item == 5).findFirst();

//如果不满足filter中的条件，没有长度为5的字符串，什么都不打印
first.ifPresent(System.out::println);

//如果不满足filter中的条件，没有长度为5的字符串，就打印0
System.out.println(first.orElse(0));
```

---------------------------------------------

## 例11：flatMap

`数据来源：`
> List<String> list = Arrays.asList("hello world", "world hello", "welcome hello", "world hello welcome");

使用slip切割后(hello world -> [hello, world])，使用map只能得到到一个二维数组，`[[hello, world], [world, hello], [welcome, hello], [world, hello, welcome]]`；所以遍历时，需要使用Arrays.asList(item)将数组转化为List集合后，直接使用forEach进行遍历。

```
List<String[]> collect = list.stream().
        map(item -> item.split(" ")).
        collect(Collectors.toList());

collect.forEach(item -> Arrays.asList(item).forEach(System.out::println));
```

使用flatMap将数据平铺就完全不一样了  
切割后的字符串会变成数组，再使用Arrays.stream(item)方法，将数组传进去，进行平铺，调用toList方法，将所有的平铺的数据转换为List集合，最后打印。

```
list.stream().map(item -> item.split(" ")).
        //item -> Arrays.stream(item)
        flatMap(Arrays::stream).
        collect(Collectors.toList()).
        forEach(System.out::println);
```

---------------------------------------------

## 例12：互相打招呼（flatMap和map的结合使用）

- 问题：让list1中的数据依次对list2中打招呼  

将list1中的数据平铺，在平铺的过程中，依次再对每个元素进行操作，对list2进行映射(`item2 -> item + " " + item2`)  
直接在平铺过程中，再次对list2进行映射，并使用list1平铺的数据，item与item2直接进行拼接，便可得到想要的结果。

```
List<String> list1 = Arrays.asList("hi", "hello", "你好");
List<String> list2 = Arrays.asList("zhangsan", "lisi", "wangwu");

List<String> collect = list1.stream().
                flatMap(item -> list2.stream().map(item2 -> item + " " + item2)).
                collect(Collectors.toList());

collect.forEach(System.out::println);
```

------------------------------------------

## 例13：分组(groupingBy)和分区(partitioningBy)  

groupingBy：  
- 一个参数：传入Student::getName，便会根据name进行分组
- 两个参数：传入Student::getName, Collectors.counting()，会根据第一个参数进行分组，第二个参数就是那么相同的个数

partitioningBy：  
- 一个参数：类型是`Predicate`，输入一个参数，返回Boolean值，Boolean值就是分区名称
- 两个参数：同groupingBy差不多
> `使用map4.get(true)可直接，取出对于的分区中的所有数据`


```
Student s1 = new Student("zhangsan", 100, 20);
Student s2 = new Student("lisi", 90, 20);
Student s3 = new Student("wangwu", 90, 30);
Student s4 = new Student("zhangsan", 70, 40);

List<Student> students = Arrays.asList(s1, s2, s3, s4);

//此处groupingBy中的参数是Function
//参数是：一个学生，返回姓名
Map<String, List<Student>> map1 = students.stream()
        .collect(Collectors.groupingBy(student -> student.getName())); 
        //Student::getName
System.out.println(map1);

//此处groupingBy中的参数:
//第一个是：Function类型，传入参数一个学生，返回姓名
//第二个是：通过name分组，name相同的个数
Map<String, Long> map2 = students.stream()
        .collect(Collectors.groupingBy(Student::getName, Collectors.counting()));
System.out.println(map2);

//求分组后的name相同的组的平均分
Map<String, Double> map3 = students.stream()
                .collect(Collectors.groupingBy(Student::getName, 
                         Collectors.averagingInt(student -> student.getScore())));
System.out.println(map3);

//分区，符合条件为true，反之。
Map<Boolean, List<Student>> map4 = students.stream()
        .collect(Collectors.partitioningBy(student -> student.getScore() >= 90));
System.out.println(map4);

Map<Boolean, Long> map5 = students.stream()
                .collect(Collectors.partitioningBy(student -> student.getScore() >= 90, Collectors.counting()));
System.out.println(map5);

//获取符合条件的分区
List<Student> students1 = map4.get(true);
System.out.println(students1);
```

`答案：`  
```
{
        lisi=[Student(name=lisi, score=90, age=20)], 
        zhangsan=[
                        Student(name=zhangsan, score=100, age=20), 
                        Student(name=zhangsan, score=70, age=40)
                ], 
        wangwu=[Student(name=wangwu, score=90, age=30)]
}
{lisi=1, zhangsan=2, wangwu=1}
{lisi=90.0, zhangsan=85.0, wangwu=90.0}
{
        false=[Student(name=zhangsan, score=70, age=40)], 
        true=[
                Student(name=zhangsan, score=100, age=20), 
                Student(name=lisi, score=90, age=20), 
                Student(name=wangwu, score=90, age=30)
        ]
}
{false=1, true=3}
[
        Student(name=zhangsan, score=100, age=20),
         Student(name=lisi, score=90, age=20), 
         Student(name=wangwu, score=90, age=30)
]
```


