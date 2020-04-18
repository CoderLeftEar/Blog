---
title: "JVM笔记04-Java字节码01"
date: 2019-09-16T15:48:32+08:00
author: CoderLeftEar
tags: ["JVM"]
categories: ["JVM"]
draft: true
---

# JVM笔记04-Java字节码01

-------------------------------

<!-- TOC -->

- [JVM笔记04-Java字节码01](#jvm笔记04-java字节码01)
    - [Java字节码常量池](#java字节码常量池)
        - [<center>Class文件结构常量池中11种数据类型</center>](#centerclass文件结构常量池中11种数据类型center)
    - [Java字节码结构](#java字节码结构)
    - [Access_Flag 访问标志](#access_flag-访问标志)
    - [This Clsaa Name](#this-clsaa-name)
    - [Super Class Name](#super-class-name)
    - [Interfaces](#interfaces)
    - [Fields](#fields)
    - [Method](#method)
    - [Attributes](#attributes)
    - [助记符（monitorenter、monitorexit）含义](#助记符monitorentermonitorexit含义)
    - [Java字节码默认有一个`this对象`参数、异常处理方式](#java字节码默认有一个this对象参数异常处理方式)

<!-- /TOC -->


```
CAFEBABE0000003400180A00040014090003001507001607001701000161010001490100063C696E69743E010003282956010004436F646501000F4C696E654E756D6265725461626C650100124C6F63616C5661726961626C655461626C650100047468697301001E4C636F6D2F77616E672F6A766D2F62797465636F64652F44656D6F30313B01000467657441010003282949010004736574410100042849295601000A536F7572636546696C6501000B44656D6F30312E6A6176610C000700080C0005000601001C636F6D2F77616E672F6A766D2F62797465636F64652F44656D6F30310100106A6176612F6C616E672F4F626A65637400210003000400000001000200050006000000030001000700080001000900000038000200010000000A2AB700012A04B50002B100000002000A0000000A00020000000700040008000B0000000C00010000000A000C000D00000001000E000F000100090000002F00010001000000052AB40002AC00000002000A0000000600010000000B000B0000000C000100000005000C000D0000000100100011000100090000003E00020002000000062A1BB50002B100000002000A0000000A00020000000F00050010000B00000016000200000006000C000D00000000000600050006000100010012000000020013
```


## Java字节码常量池


1. 使用javap -verbose命令分析一个字节码文件时，将会分析该字节的魔数、版本号、常量池、类信息、类的构造方法、类中的方法信息、类变量与成员变量等信息。

2. 魔数：所有.class字节码文件的前4个字节都是魔数，魔数为固定值：`0xCAFABABE(CA FE BA BE)`。
3. 魔数之后的4个字节为版本信息，后两个字节表示major version（主版本号），这里的版本号为`00 00 00 34`，换算成十进制，表示次版本号为0，主版本为52，所以，该文件的版本号为：1.8.0。可以通过java -version命令来验证这一点。
4. 常量池（constant pool）：紧接着主版本号之后就是常量池入口。一个Java类定义的很多信息都是由常量池来维护和描述的，可以将常量池看作是Class文件的资源仓库，比如说Java类中定义的方法与变量信息，都是存储在常量池中。常量池中主要存储两类常量：字面量和符号引用。
    - 字面量如文本字符，Java中声明为final的常量值等。
    - 符号引用如类和接口的全局限定名，字段的名称和描述符，方法的名称和描述符等。
5. 常量池的总体结构：Java类所对应的常量池主要由常量池数量和常量池数组（常量表）这两部分组成。常量池数量紧跟在主版本后面，占据两个字节；常量池数组则紧跟在常量池数量之后。常量池数组与一般数组不同的是，常量池数组中的元素类型和结构都是不同的；但是，每一个元素的第一个数据都是一个u1类型，该字节是一个标志位，占据1个字节。JVM在解析常量池时，会根据这个u1类型来获取元素的具体类型。值的注意的是，常量池数组中元素个数 = 常量池数（`00 18`） - 1 （其中0暂时不使用），目的是满足某些常量池索引值的数据在特定情况下需要表达`不引用任何一个常量池`的含义；根本原因在于，索引为0也是一个常量（保留常量），只不过它不位于常量表中，这个常量就对应null值，所以，常量池的索引从1开始而非0开始。

###<center>Class文件结构常量池中11种数据类型</center>

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile01.jpg)


6. 在JVM规范中，每个变量/字段就对应有描述信息，描述信息主要的作用是描述字段的数据类型、方法的参数列表（包括数量、类型与顺序）与返回值。根据描述符规则，基本数据类型和代表无返回值的void类型都用一个大写字符表示，对象类型则使用字符L加对象的全限定名称来表示，为了压缩字节码文件的体积，对于基本数据类型，JVM都只是用一个大写字母表示，如下表示：`B - byte，S - short，I - int，J - long，F - float，D - double，C - char，Z - boolean，V - void，L - 对象类型，如Ljava/lang/String`;
7. 对于数组类型来说，每一个维度使用一个前置的`[`来表示，`int[]`被记录为`[I`，`String[][]`被记录为`[[Ljava/lang/String`;
8. 用描述符描述方法时，按照先参照列表，后返回值的顺序来描述。参数列表按照参数的严格顺序放在一组`()`之内，如方法：`String getNickname(int id, String name)`的描述符为：`(I, Ljava/lang/String)Ljava/lang/String`;

-----------------------------
```
0A 0004 0014
09 0003 0015 
07 0016 
07 0017 
01 0001 61
01 0001 49
01 0006 3C 69 6E 69 74 3E
01 0003 28 29 56
01 0004 43 6F 64 65
01 000F 4C 69 6E 65 4E 75 6D 62 65 72 54 61 62 6C 65
01 0012 4C 6F 63 61 6C 56 61 72 69 61 62 6C 65 54 61 62 6C 65
01 0004 74 68 69 73
01 001E 4C 63 6F 6D 2F 77 61 6E 67 2F 6A 76 6D 2F 62 79 74 65 63 6F 64 65 2F 44 65 6D 6F 30 31 3B
01 0004 67 65 74 41
01 0003 28 29 49
01 0004 73 65 74 41
01 0004 28 49 29 56
01 000A 53 6F 75 72 63 65 46 69 6C 65
01 000B 44 65 6D 6F 30 31 2E 6A 61 76 61
0C 0007 0008
0C 0005 0006
01 001C 63 6F 6D 2F 77 61 6E 67 2F 6A 76 6D 2F 62 79 74 65 63 6F 64 65 2F 44 65 6D 6F 30 31
01 0010 6A 61 76 61 2F 6C 61 6E 67 2F 4F 62 6A 65 63 74
```

```
 #1 = Methodref          #4.#20         // java/lang/Object."<init>":()V
 #2 = Fieldref           #3.#21         // com/wang/jvm/bytecode/Demo01.a:I
 #3 = Class              #22            // com/wang/jvm/bytecode/Demo01
 #4 = Class              #23            // java/lang/Object
 #5 = Utf8               a
 #6 = Utf8               I
 #7 = Utf8               <init>
 #8 = Utf8               ()V
 #9 = Utf8               Code
#10 = Utf8               LineNumberTable
#11 = Utf8               LocalVariableTable
#12 = Utf8               this
#13 = Utf8               Lcom/wang/jvm/bytecode/Demo01;
#14 = Utf8               getA
#15 = Utf8               ()I
#16 = Utf8               setA
#17 = Utf8               (I)V
#18 = Utf8               SourceFile
#19 = Utf8               Demo01.java
#20 = NameAndType        #7:#8          // "<init>":()V
#21 = NameAndType        #5:#6          // a:I
#22 = Utf8               com/wang/jvm/bytecode/Demo01
#23 = Utf8               java/lang/Object
```

-----------------------------

## Java字节码结构

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile02.jpg)


![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile03.jpg)

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile04.jpg)


Class字节码中有两种数据类型
- 字节码数据直接量：这是基本的数据类型。共细分为u1、u2、u4、u8四种，分别代表连续的1个字节、2个字节、4个字节、8个字节组成的整体数据。
- 表（数组）：表是由多个基本数据或其他表，按照既定顺序组成的大的数据集合。表示有结构的，它的结构体现在：组成表的成分所在的位置和顺序都是已经严格定义好的。

上面的表中描述了11种数据类型的机构，在jdk7后又增加了3种（CONSTANT_MethodHandle_info, CONSTANT_MethodType_info, CONSTANT_InvokeDynamic_info）。

----------------------------------

## Access_Flag 访问标志

```
access_flags（类访问控制权限）: 0021: 由图可知，0x0001、0x0020组成0x0021(0x0002是ACC_PRIVATE)
```

访问标志信息包括该Class文件是类还是接口，是够被定义成public，是否是abstract，如果是类，是否被声明成final。通过上面的源代码，我们知道该文件是类并且是public。

`0x0002是ACC_PRIVATE`
![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile05.jpg)

-------------------------------

## This Clsaa Name
```
this_class（类名）: 0003
```

-------------------------------

## Super Class Name
```
super_class（父类名）: 0004
```

-------------------------------

## Interfaces
```
interfaces_count（接口个数）: 00
interfaces（接口名）: 00
```

-------------------------------

## Fields

```
fields_count（域个数）: 0001
fields（域的表）: 
    access_flags: 0002
    name_index: 0005
    descriptor_index: 0006
    attributes_count: 0000
    attributes: 

name_index和descriptor_index需要对应idea中的常量池查看（就是常量池中第五个和第六个）。
```

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile06.jpg)

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile07.jpg)

------------------------------------

## Method

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile09.jpg)


```
三个方法：0003

First: {
    access_flags: 0001
    name_index: 0007
    descriptor_index: 0008
    attributes_count: 0001

    Code结构：
    attribute_name_index: 0009
    attribute_length(Code长度): 00000038
    max_stack: 0002
    max_locals: 0001
    code_length: 0000000A
    code[code_length]: 2A B7 00 01 2A 04 B5 00 02 B1
    {
        2A:         aload_0
        B7-(00 01): invokespecial #1 <java/lang/Object.<init>>
        2A:         aload_0
        04:         iconst_1
        B5-(00 02): putfield #2 <com/wang/jvm/bytecode/Demo01.a>
        B1:         return
    }
    exception_table_length: 0000

    attributes_count: 0002
    attribute_info: {
        attribute_name_index: 000A
        attribute_length: 0000000A
        info: 
            LineNumberTable: {
                attribute_name_index: 000A
                attribute_count: 0000000A
                line_number_table_length: 0002
                {
                    start_pc: 0000 000C
                    line_number: 0004 000E
                }
            }
            LocalVariableTable: {
                attribute_name_index: 000B
                attribute_length: 0000000C
                lcoalVaritable_count: 0001
                {
                    start_pc: 0000
                    end_pc_length: 000A
                }
                localVariable_index: 000C
                descriptor_index: 000D
                stackmactable(校验检查): 0000
            }
    }
}
```

Code结构：

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile11.jpg)

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile10.jpg)


![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile08.jpg)




- attribute_length: attribute所包含的字节数，不包含attribute_name_index和attribute_length。
- max_stack: 表示这个方法运行的任何时刻所能到达的操作数栈的最大深度。
- max_loacls: 表示方法执行期间所创建的局部变量的数目，包含用来传入参数的局部变量。
- code_length: 表示该方法所包含的字节码（该方法被调用时，虚拟机所执行的字节码）的字节数以及具体的指令码（助记符）。
- exception_table: 这里存放的时处理异常的信息(每个exception_table由start_pc、end_pc、handler_pc、catch_type组成)。
- start_pc和end_pc: 表示code数组中的从start_pc到end_pc（包含前者，不包含后者）的指令抛出的异常会有这个表项来处理。
- handler_pc: 表示处理异常的代码的开始处。
- catch_type: 表示会被处理的异常类型，它指向常量池里的一个异常类，当catch_type为0时，表示会处理所有异常。



LineNumberTable: 

- start_pc: 字节码行号

  ![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile14.jpg)

- line_number: 源代码行号


![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile12.JPG)


LocalVariableTable: 
```
LocalVariableTable_attribute {
    u2 attribute_name_index;
    u4 attribute_length;
    u2 lcoalVaritable_count;
    {
        u2 start_pc;
        u2 end_pc_length;
    }
    u2 localVariable_index;
    u2 descriptor_index;
    u2 stackmactable(校验检查);
}
```

----------------------------------

## Attributes

最后结尾部分：
```
attribute_info {
    u2 attribute_count: 0001
    u2 attribute_index: 0012
    u4 attribute_length: 00000002
    u2 sourceFile_name: 0013
}
```

--------------------------------------



------------------------

关于synchronized的相关使用：忘了！！！

当代码中存在静态变量时，就会出现`<clinit>`，统一的对静态变量进行初始化。



## 助记符（monitorenter、monitorexit）含义

**monitorenter：**

objectref必须是引用类型。
每个对象都与一个monitor相关联。当且仅当monitor有所有者时，monitor才被锁定。执行monitorenter的线程试图获得与objectref关联的monitor的所有权，如下所示:

- 如果与objectref关联的monitor的条目数为零，则线程将进入monitor并将其条目数设置为1。然后线程是monitor的所有者。
- 如果线程已经拥有与objectref关联的monitor，那么它将重新进入monitor，增加其条目数。

- 如果另一个线程已经拥有与objectref关联的monitor，则线程将阻塞，直到monitor的条目计数为零，然后再次尝试获得所有权。

**monitorexit：**

- objectref必须是引用类型。
- 执行monitorexit的线程必须是与objectref引用的实例相关联的monitor的所有者。

- 线程减少与objectref关联的monitor的条目计数。如果结果是条目计数的值为零，则线程退出monitor，不再是其所有者。其他阻塞进入monitor的线程可以尝试这样做。



## Java字节码默认有一个`this对象`参数、异常处理方式

- Java字节码层面，只要是方法，那么第一个参数就是`this`
- 在Java源代码中finally只有一个，但是jvm采用的是在每个catch语句后都添加相同的finally语句。

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/classFile13.jpg)







