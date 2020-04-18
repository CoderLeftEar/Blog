---
title: "JVM笔记05-Java字节码02"
date: 2019-09-27T09:13:21+08:00
author: CoderLeftEar
tags: ["JVM"]
categories: ["JVM"]
draft: true
---

# JVM笔记05-Java字节码02

----------------------------


<!-- TOC -->

- [JVM笔记05-Java字节码02](#jvm笔记05-java字节码02)
    - [栈帧（stack frame）](#栈帧stack-frame)
    - [符号引用、直接引用](#符号引用直接引用)
    - [invokevirtual](#invokevirtual)
        - [方法的静态分派：](#方法的静态分派)
        - [方法的动态分派：](#方法的动态分派)
    - [虚方法（类加载的连接阶段）](#虚方法类加载的连接阶段)
    - [基于栈、寄存器的指令集对比](#基于栈寄存器的指令集对比)
        - [例：栈指令集案例吧！](#例栈指令集案例吧)

<!-- /TOC -->


## 栈帧（stack frame）

---------------

栈帧是一种用于帮助jvm执行方法调用和方法执行的数据结构。

栈帧本身是一种数据结构，封装了方法的局部变量表、动态链接信息、方法返回地址以及操作数栈等信息。

**slot(变量槽)**：存贮局部变量的最小单位。可复用：10个局部变量不一定有10个slot存贮。

按下面的例子来说：5个局部变量，会有3个slot存贮。

```java
public void test () {
    int a = 1;
    if (a < 5) {
        int b = 2;
        int c = 3;
    }
    int d = 4;
    int e = 5;
}
```



## 符号引用、直接引用

- 有些符号引用是在类加载阶段或是第一次使用时就会转化为直接引用，这种转换及叫做静态解析；

- 另外一些符号引用则是在每次运行期转换为直接引用，这种转换叫做动态链接，这体现为Java的多态性。

从Java字节码来说，a.sleep()每次调用的都是Animal类的sleep()方法，但会有一个`invokevirtual`指令会在运行期间检测a真正指向的对象。

```java
Animal a = new Dog();
a.sleep();
a = new Cat();
a.slepp();
```

----

1. invokeinterface：调用接口中的方法，实际上是在运行期决定的，决定到底调用实现该接口的哪个对象的特定方法。
2. invokestatic：调用静态方法。
3. invokespecial：调用自己的私有方法、构造方法（`<init>`）以及父类方法。
4. invokevirtual：调用虚方法，运行期动态查找的过程。
5. invokedynamic：动态调用方法。



**静态解析的4中情形：**

1. 静态方法
2. 父类方法
3. 构造方法
4. 私有方法（无法被重写）

以上4类方法称作非虚方法，它们是在类加载阶段就可以将符号引用转换为直接引用的。



## invokevirtual

### 方法的静态分派：

Grandpa g1 = new Parent();

以上代码，g1的静态类型是Grandpa，而g1的实际类型（真正指向的类型）时Parent。

**结论：**变量的静态变量时不会发生改变的，而变量的实际类型则是可以发生改变的（多态的一种体现），实际类型是在运行期才能确定的。



方法重载是一种静态的行为，编译器就可以完全确定。

> 是由同一个对象调用的。

调用test()方法时，传递的参数是Grandpa类型(静态类型)，所以不会调用真正的g1指向的类型的参数的test()方法。

```java
public class Test02 {
	//参数已确定是Grandpa类型
    public void test (Grandpa grandpa) {
        System.out.println("Grandpa");
    }
    
    public void test (Parent parent) {
        System.out.println("Parent");
    }

    public void test (Son son) {
        System.out.println("Son");
    }

    public static void main(String[] args) {
        Grandpa g1 = new Parent();
        Grandpa g2 = new Son();

        Test02 test02 = new Test02();

        test02.test(g1);
        test02.test(g2);
    }
}

class Grandpa {}

class Parent extends Grandpa {}

class Son extends Parent {}

结果：
Grandpa
Grandpa
```



### 方法的动态分派：

方法重写是一种动态的行为。

> 是由不同对象调用的。

方法的动态分派涉及到一个重要概念：方法接受者

**`invokevirtual`**字节码指令的多态查找流程：

- **1.**寻找到操作数栈顶的第一个这个元素所指向的这个对象的实际类型
- **2.**在第一步的实际类型中查找是不是包含名为test()的方法
  - 如果找到了，并且权限校验通过，然后直接调用特定的方法，流程结束；
  - 找不到，就从子类到父类依次查找，一直到能够找到匹配的特定的对象的方法；
  - 如果一直都找不到，就会抛异常。

- **2.**如果在这个类型当中找到了与常量池中描述符和名称都相同的方法，并且具备相应的访问权限，那么直接返回目标方法的直接引用。

比较方法重载（overload）与方法重写（override），我们可以得到这样一个结论：方法重载是静态的，是编译期行为；方法重写是动态的，是运行期的。

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile15.jpg)



<font color='red' size=5>方法是由谁调用的是区分的重要依据：重载：相同对象；重写：不同对象</font>



## 虚方法（类加载的连接阶段）

-----------------

针对于方法调用动态分派的过程，虚拟机会在类的方法区建立一个虚方发表的数据结构（virtual method table, vtable）

针对于invokeinterface指令来说，虚拟机会建立一个叫做接口方法表的数据结构（interface method table, itable）

vtable中的每一项标识的是每一个方法入口的实际的调用地址。



- 子类继承父类，被继承没有被重写的方法。子类不会复制一份父类的方法到子类中，而是直接指向父类中的方法。

- 子类和父类中的两个方法（相同还是不相同）描述符一样，那么它们的索引（是`地址`吗？）也是一样的。



**例：**

```java
public class Parent {
    void test1();
}

public class Child {
    void test1();
    void test2();
}

Parent child = new Child();
child.test2();

结果：
编译期就报错，在字节码中有invokevirtual指令，那么它指向的是.../Parent.test2。
如果程序正常执行，那么便会根据动态分派执行，从invokevirtual中依次查找子类父类中的 test2()。
```



## 基于栈、寄存器的指令集对比

-----------

现代JVM在执行Java代码的时候，通常都会见解释执行和编译执行结合使用。

解释执行：通过解释器来读取字节码，遇到相应的指令就去执行该指令。

编译执行：通过及时编译器（Just In Time，JIT）将字节码转化为本地机器码来执行，现代JVM会根据代码热点来生成相应的本地代码。

基于栈的指令集和基于寄存器的指令集之间的关系：

1. JVM执行指令时所采用的方式就是基于栈的指令集。
2. 基于栈顶指令集只要操作就是入栈和出栈操作。
3. 基于栈的指令集优势在于不同平台的可以可移植性；而基于寄存器的指令集使用硬件架构紧密联系的，无法做到可移植性。
4. 栈缺点在于完成相同的操作，比寄存器的指令集数量要多；基于栈的指令集是在内存中完成操作单，而寄存器是直接由CPU来执行的，它是在告诉缓冲区中进行执行的，速度要快很多。
5. 虚拟机可以采用一些优化手段，但总体来说，基于栈的指令集的执行速度要慢一些。



**例：**减法：2-1

基于栈的指令集：

1. iconst_1 ：除数
2. iconst_2 ：被除数
3. isub ：将栈中的上面两个数据弹出来之后，进行运算，再将运算后的结果压入栈中。
4. istore_0 ：将运算结果1放到局部变量表的第一个位置。

基于寄存器的指令集：

- 调用mov操作：将`2`放到一个寄存器当中。

- 调用sub减法操作：后面跟着一个`参数1`，然后再将运算结果`1`放到寄存器当中。



### 例：栈指令集案例吧！

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile16.jpg)

1. iconst_1：将`1`推送到操作数栈顶。
2. istore_1：将操作数栈顶的数据存放到局部变量表索引为1的位置（索引为0的位置存放的是`this`变量，istore 4的作用相同，将数据放到局部变量表索引为4的位置，jvm只定义到了istore_0 ~ istore_3，这4个用完之后可以使用istore <`index`>表示）。
3. iload_1：将局部变量表的索引为1的数据推送到栈顶。
4. iload_2：将局部变量表的索引为2的数据推送到栈顶。
5. iadd：pop出两个操作数value1、value2，进行add操作value1 + value2 = result；再将result推入栈顶。
6. ireturn：将操作数栈顶的result弹出，作为最终返回值；如果操作数栈还有值，全部丢弃。


