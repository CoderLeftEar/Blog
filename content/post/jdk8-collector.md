---
title: "Jdk8-Collector"
date: 2019-08-16T17:35:01+08:00
author: CoderLeftEar
tags: ["jdk8"]
categories: ["jdk8"]
draft: true
---

# Jdk8-Collector
---

collect: 收集器  
Collector作为collect方法的参数  

Collector是一个接口，是一个可变的汇聚操作，该操作将输入元素累积到一个可变的结果容器中（例：ArrayList可变，其中的元素是可以增加的），可选的将累计的结果转换为一个最终的表示形式，在所有元素处理完毕之后。支持串行合并行两种方式执行。  

汇聚操作：将元素累积到Collection中，使用StringBuilder，计算关于sum、min、max或average等元素的汇总信息，计算“数据透视表”摘要（如“卖方最大成交金额”等）。Collectors提供了许多常见的可变汇聚的实现。Collectors本身实际上是一个工厂。  

Collector有四个函数指定，将条目累积到一个可变的结果容器中，并且可选的对结果进行最终的转换：  

`Collector<T, A, R>`
T: 流中每一个元素的类型。  
A: 中间生成的结果容器的类型，例：ArrayList<T>类型。  
R: 汇聚操作单结果类型。  

```
Supplier<A> supplier()：创建并返回一个新的结果容器。  

BiConsumer<A, T> accumulator()：将一个新的数据元素（流中的每个元素）折叠到可变结果容器当中。  

BinaryOperator<A> combiner()：  
1.将两个结果容器合并为一个。  
2.接受两个部分结果并将其合并的函数。组合器函数可以将状态从一个参数折叠到另一个参数并返回该参数（集合A，B，将B全部放到A中），或者返回一个新的结果容器（集合A，B，将A、B放到新的集合C中）。
BinaryOperator<A> combiner()：从中间积累类型执行最后的转换到最终结果类型  

Function<A, R> finisher()：操作A（结果容器），返回R。
```

---

为了确保串行与并行操作结果的等价性，Collector函数需要满足的两个条件：identity（同一性）与associativity（结合性）。  



**同一性**
```
{@code a} must be equivalent to {@code combiner.apply(a, supplier.get())}.  
```
> a == combiner.applu(a, supplier.get());  

相当于：  

> combiner == (List<String> list1, List<String> list2) -> {list1.addAll(list2), return list1;}  


**结合性**

串行：调用BiConsumer的双参，无返回指的accept()方法，将输入元素t1，t2放到a1中，最后强转给r1。
```
A a1 = supplier.get();
accumulator.accept(a1, t1);
accumulator.accept(a1, t2);
R r1 = finisher.apply(a1);
```
并行：调用BiConsumer的双参，无返回指的accept()方法，将输入元素t1，t2放到并行放到a2，a3中，最后调用BiFunction将a2，a3合并，最后强转给r2。
```
A a2 = supplier.get();
accumulator.accept(a2, t1);
A a3 = supplier.get();
accumulator.accept(a3, t2);
R r2 = finisher.apply(combiner.apply(a2, a3));
```

---

---

---

```
CONCURRENT: 收集器，`并行流`
 1.使用：可以支持多个线程同时操作同一个结果容器，会造成异常。
    此异常可能由检测到对象的并发修改的方法引发，而这种修改是不允许的。
    与UNORDERED无序结合使用。
 2.不使用：多个线程操作多个结果容器。

UNORDERED: 不保存输入元素的顺序。

IDENTITY_FINISH: finisher函数，必须要从A到R成功完成。 
```


```
public static <T, U, A, R>
    Collector<T, ?, R> mapping(`Function<? super T, ? extends U> mapper`,
                               Collector<? super U, A, R> downstream) {
        BiConsumer<A, ? super U> downstreamAccumulator = downstream.accumulator();
        return new CollectorImpl<>(downstream.supplier(),
                                    //mapper.apply(t),传入t返回mapper.apply(t)
                                   (r, t) -> downstreamAccumulator.accept(r, mapper.apply(t)),
                                   downstream.combiner(), downstream.finisher(),
                                   downstream.characteristics());
    }

```


groupingBy(Function<? super T, ? extends K> classifier)  
return groupingBy(classifier, toList());

groupingBy(Function<? super T, ? extends K> classifier, Collector<? super T, A, D> downstream)  
return groupingBy(classifier, HashMap::new, downstream);

groupingBy(Function<? super T, ? extends K> classifier, Supplier<M> mapFactory, Collector<? super T, A, D> downstream)  



