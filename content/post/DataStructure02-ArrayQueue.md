---
title: "DataStructure02-ArrayQueue"
date: 2019-09-04T08:14:13+08:00
author: CoderLeftEar
tags: ["DataStructure"]
categories: ["DataStructure"]
draft: true
---

# DataStructure02-ArrayQueue（使用数组模拟队列）
---------------------------

## 在ArrayQueue类中，定义数组队列中的方法
```
class ArrayQueue {
    private int maxSize; //数组最大容量
    private int front; //队列头
    private int rear; //队列尾
    private int[] arr; //该数据用于存放数据，模拟队列

    public ArrayQueue(int maxSize) {
        this.maxSize = maxSize; //指定队列最大值
        arr = new int[this.maxSize]; //指定存放数据的数组的大小
        front = -1; //指向队列头部，指向队列头的前一个位置
        rear = -1; //指向队列尾，指向队列尾的数据，即最后一个数据
    }

    //判断队列是否满
    public boolean isFull() {
        return rear == maxSize;
    }

    //判断队列是否为空
    public boolean isEmpty() {
        return front == rear;
    }

    //添加数据到队列
    public void add (int value) {
        //队列是否满
        if(isFull()){
            System.out.println("队列已满！");
            return;
        }
        //rear向后移动一位
        rear ++;
        arr[rear] = value;
    }

    //获取队列数据, 数据出队列
    public int get () {
        //队列是否为空
        if(isEmpty()){
            //应为取数据有返回值，所有抛异常
            throw new RuntimeException("队列为空！");
        }
        front ++;
        return arr[front];
    }

    //显示队列所有数据
    public void showQueue () {
        //是否为空
        if(isEmpty()) {
            System.out.println("队列为空！");
            return;
        }
        //遍历
        for (int i = front + 1; i < maxSize; i ++) {
            System.out.println(arr[i]);
        }
    }

    //显示队列的头部
    public int headQueue () {
        //是否为空
        if(isEmpty()){
            throw new RuntimeException("队列为空！");
        }
        return arr[front + 1];
    }
}
```

## 主方法的调用

```
//创建实例对象，队列大小为3
ArrayQueue array = new ArrayQueue(3);

char k; //接收用户输入
Scanner scanner = new Scanner(System.in);
boolean loop = true;

while (loop) {
    System.out.println("a(add): 添加数据");
    System.out.println("g(get): 取出数据");
    System.out.println("s(showQueue): 显示所有数据");
    System.out.println("h(headQueue): 显示头部数据");
    System.out.println("e(exit): 退出循环");

    //接收用户输入的操作数
    k = scanner.next().charAt(0);

    switch (k) {
        case 'a' :
            System.out.print("输入添加的数据：");
            int value = scanner.nextInt();
            array.add(value);
            break;
        case 'g' :
            try {
                int res = array.get();
                System.out.println("取出的数据：" + res);
            } catch (Exception e) {
                System.out.println(e.getMessage());
            }
            break;
        case 's' :
            array.showQueue();
            break;
        case 'h' :
            try {
                int res = array.headQueue();
                System.out.println("队列头部数据：" + res);
            } catch (Exception e) {
                System.out.println(e.getMessage());
            }
            break;
        case 'e' :
            loop = false;
            break;
        default:
            break;
    }
}
```





