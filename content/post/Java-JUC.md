---
title: "Java-JUC"
date: 2019-10-16T09:22:04+08:00
author: CoderLeftEar
categories: ["JUC"]
tags: ["JUC"]
draft: true
---



# Java-JUC

---

## 什么是进程、线程？

#### 1、进程：

进程是一个具有一定独立功能的程序关于某个数据集合的一次运行活动。它是操作系统动态执行的基本单元，在传统的操作系统中，进程既是基本分配单元，也是基本执行单元。（一个QQ.exe的运行就是一个进程，word程序进程）。

#### 2、线程：

通常一个进程中可以包含若干个线程，当然一个进程中至少一个线程，不然没有存在意义。线程可以利用进程所拥有的资源，在引入线程的操作系统中，通常都是把进程作为分配资源的基本单位，而把线程作为独立运行和独立调度的基本单位，由于线程比进程更小，基本上不拥有系统资源，故对它的调度所付出的开销就会小得多，能更高效的提高系统多个程序间并发执行的程度。（QQ中和每个人聊天就是每一个线程，word中检查拼写错误的线程）。



## 什么是并发、并行？

#### 1、并发：

多个线程同一个时间点争取同一个资源。

#### 2、并行

同一个时间点多个任务同时执行。

多项工作一起执行，之后再汇总。



## Ticket

### 实现多线程的方式：

1. 继承Thread类,重写run()方法
2. 实现Runnable接口,实现run()方法
3. 实现Callable接口，实现call()方法
4. 通过线程池创建线程



### 高内聚高耦合：

#### 高内聚：

空调的制冷和制热，是空调自带的，内聚在空调中的。

#### 低耦合：

例：前后端分离，互相依赖最小。



多线程与操作系统有关，多线程`不是`.start();后立刻启动，而是进入就绪状态，等待操作系统和CPU调用run方法的时候，开始启动。

### Thread.State

1. NEW：新建
2. RUNNABLE：就绪，可运行
3. BLOCKED：阻塞
4. WAITING：等待，不见不散
5. TIMED_WAITING：等待，超时不见
6. TERMINATED：终结



wait：放开锁去睡

sleep：握着锁去睡



## 函数式接口：

只能有一个普通方法，可以有多个defalt方法，多个静态方法，多个Object中的方法。



### 线程 -> 操作(对外暴露的方法) -> 资源类

#### 通过Lock接口进行上锁和解锁：

```java
public class ThreadDemo02 {

    public static void main(String[] args) {
        final Ticket ticket = new Ticket();

//        new Thread(new Runnable() {
//            @Override
//            public void run() {
//                for (int i = 0; i < 4000; i ++) {
//                    ticket.saleTicket();
//                }
//            }
//        }, "A..").start();

        new Thread(() -> {
            for (int i = 0; i < 400; i ++) {
                ticket.saleTicket();
            }
        }, ".B.").start();

        new Thread(() -> {
            for (int i = 0; i < 400; i ++) {
                ticket.saleTicket();
            }
        }, "..C").start();
    }
}

class Ticket02 {
    private int num = 300;
    private Lock lock = new ReentrantLock();

    public void saleTicket() {
        lock.lock();
        try {
            if (num > 0) {
                System.out.println(Thread.currentThread().getName() + "\t卖出第" + (num --) + "张票\t还剩：" + num + "张票");
            }
        } finally {
            lock.unlock();
        }
    }
}
```



## 判断、干活、通知（生产者、消费者问题）

**使用等待、唤醒实现交替**

**多线程交互中（买票不需要交互，票卖光为止；有需求才叫交互，我要一个蛋糕，你给我做一个），必须要防止多线程的虚假唤醒，也即（多线程判断只用while，不能用if）**

**原因：**

- 四个线程，使用if的话，当num = 1时，第一个执行`增加方法`线程会进入if判断，并且wait，当第二个抢到的线程也是`增加方法`，num = 1，进入if判断，也会等待，当第三个线程是减法操作，执行完毕后，执行notifyAll，使两个增加的线程停止等待，那么就一下子 ++ 两次了，num = 2。
- 两个线程的话，要么增加，要么减少，不会发生错乱问题。

```java
// 增加方法
public synchronized void increment() throws Exception {
    //if (num != 0) {
    //    this.wait();
    //}
    while (num != 0) {
        this.wait();
    }
    // 干活
    System.out.println(Thread.currentThread().getName() + "\t..." + (++ num));
    // 通知
    this.notifyAll();
}
```



## synchronized、Lock

- 使用synchronized对方法加锁

- 使用Lock lock = new ReentrantLock(); lock.lock(); lock.unlock();



## 等待唤醒机制

- 在类中使用Object中的wait、notify、notifyAll使线程进行等待、唤醒

- 还可使用Condition condition = lock.newCondition()创建的对象中的await、signal、signalAll方法代替



## Condition

精准控制、精准唤醒

```java
// 标志位
private int num = 1;

private Lock lock = new ReentrantLock();
// 创建三个Condition类，实行精准控制
private Condition condition1 = lock.newCondition();
private Condition condition2 = lock.newCondition();
private Condition condition3 = lock.newCondition();

public void print5() {
    lock.lock();
    try {
        // 判断
        if (num != 1) {
            condition1.await();
        }
        // 干活
        for (int i = 1; i <= 5; i ++) {
            System.out.println(Thread.currentThread().getName() + "---" + i);
        }
        // 修改标志位
        num = 2;
        // 通知
        condition2.signal();

    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        lock.unlock();
    }
}
```



## Lock8

```java
1，标准访问，请问先打印邮件还是短信？（邮件）2、邮件方法暂停4秒钟，先打印邮件还是短信？（邮件）
3、新增一个普通方法hello()，先打印邮件、短信还是hello？（hello）
4、两部手机，先打印邮件还是短信？（短信）
5、两个静态同步方法，同一部手机，先打印邮件还是短信？（邮件）
6、两个静态同步方法，2部手机，先打印邮件还是短信？（邮件）
7、一个普通同步方法，一个静态同步方法，1部手机，先打印邮件还是短信？（短信）
8、一个普通同步方法，一个静态同步方法，2部手机，先打印邮件还是短信？（短信）
```

- 若使用在一个类的普通方法上，它锁的并不是这个方法，而是这个类所在的类。同一个时间段只允许一个线程进入这个类。
- 加个普通方法后发现和同步锁无关。
- 换两个对象后，两把锁，情况改变。
- 都换成静态同步方法后，锁的是class文件
- 一半一半的情况，两把锁，单独的。



## 线程不安全

### List

```java
List<String> list = new CopyOnWriteArrayList<>();
List<String> list = Collections.synchronizedList(new ArrayList<>());
List<String> list = new Vector<>();
```

异常：CurrentModificationException

写时赋复制：   

CopyOnWrite容器即写时复制的容器。往一个容器中添加元素的时候，不直接往当前容器Object[]添加，而是先将当前容器Object[]进行Copy，复制出一个新的Object[] newElements,然后新的容器Object[] newElements里添加元素，添加完元素之后，再将原容器的引用指向新的容器 setArray(newElements); 这样做的好处是可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。

**CopyOnWriteArrayList中add方法源码：**

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

ArrayList扩容为原来一半

HashMap扩容为原来一倍



## Callable

Callable的使用：

两个线程调用两次，但只会打印一次结果，缓存原因。

```java
public class J08_CallableDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask futureTask = new FutureTask(new MyCallable());

        new Thread(futureTask, "A...").start();
        new Thread(futureTask, "B...").start();
        
        System.out.println(futureTask.get());
        System.out.println(Thread.currentThread().getName() + "运算结束");

    }
}

class MyCallable implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        System.out.println("... callable");
        try { TimeUnit.SECONDS.sleep(4);} catch (InterruptedException e) { e.printStackTrace();}
        return 1024;
    }
}
```



## JUC辅助类

### CountDownLatch（离开教室）

CountDownLatch主要有两个方法，当一个或多个线程调用await方法时，这些线程会阻塞。其他线程调用countDown方法会将计数器减1（调用countDown方法的线程不会阻塞），当计数器变为0时，因await方法阻塞的线程会被唤醒，继续执行。

```java
CountDownLatch countDownLatch = new CountDownLatch(6);
for (int i = 1; i <= 6; i++) {
    new Thread(() -> {
        try { TimeUnit.MILLISECONDS.sleep(400);} catch (InterruptedException e) { e.printStackTrace();}
        System.out.println(Thread.currentThread().getName());
        //每调用一次，6减1
        countDownLatch.countDown();
    }, i + "").start();
}
//等待6变为0，释放，此方法会阻塞线程
countDownLatch.await();
System.out.println("完毕！");
```



### CyclicBarrier（七龙珠）

```java
//执行此线程时，必须有5个线程已执行完毕
CyclicBarrier cyclicBarrier = new CyclicBarrier(5, () -> System.out.println("完毕"));
for (int i = 1; i <= 5; i++) {
    new Thread(() -> {
        System.out.println(Thread.currentThread().getName());
        try {
            //没执行5个线程，就会一直阻塞中
            cyclicBarrier.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (BrokenBarrierException e) {
            e.printStackTrace();
        }
    }, i + "").start();
}
```



### Semaphore（抢车位）

信号量（指定的车位个数）：一个是用于多个资源的互斥使用，另一个用于并发线程数的控制。

```java
Semaphore semaphore = new Semaphore(3);
for (int i = 1; i <= 5; i++) {
    new Thread(() -> {
        try {
            // 资源数减1
            semaphore.acquire();
            System.out.println(Thread.currentThread().getName() + "...进来了");
            try { TimeUnit.SECONDS.sleep(2);} catch (InterruptedException e) { e.printStackTrace();}
            System.out.println(Thread.currentThread().getName() + "...离开了");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 复原
            semaphore.release();
        }
    }, i + "").start();
}
```



## ReadWriteLock

写数据是独占，注重并发性，有且仅有一个能进来

读数据不需要管并发性，多少人读可以

```java
public class J10_ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();

        for (int i = 1; i <= 5; i++) {
            final int count = i;
            new Thread(() -> {
                myCache.put(count + "", count + "");
            }, i + "").start();
        }

        for (int i = 1; i <= 5; i++) {
            final int count = i;
            new Thread(() -> {
                myCache.get(count + "");
            }, i + "").start();
        }

    }

}

class MyCache {
    private Map<String, Object> map = new HashMap<>();
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    public void put(String key, Object value) {
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t...开始写入");
            try { TimeUnit.MILLISECONDS.sleep(400);} catch (InterruptedException e) { e.printStackTrace();}
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "\t写入成功...");
        } finally {
            readWriteLock.writeLock().unlock();
        }
    }

    public void get(String key) {
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t...开始读取");
            try { TimeUnit.MILLISECONDS.sleep(400);} catch (InterruptedException e) { e.printStackTrace();}
            Object value = map.get(key);
            System.out.println(Thread.currentThread().getName() + "\t读取成功...");
        } finally {
            readWriteLock.readLock().unlock();
        }
    }

}
```



## BlockingQueue

![](https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/BlockingQueue.jpg)

```java
BlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);
        /**
         * add、remove、element
         * 队满报异常：IllegalStateException
         * 队空报异常：NoSushElementException
         */
//        blockingQueue.add("a");
//        blockingQueue.add("a");
//        blockingQueue.add("a");
////        blockingQueue.add("a");
//        System.out.println(blockingQueue.remove());
//        System.out.println(blockingQueue.remove());
//        System.out.println(blockingQueue.remove());
////        System.out.println(blockingQueue.remove());


        /**
         * offer、poll、peek
         * 插入失败：false
         * 移除没有：null
         */
//        System.out.println(blockingQueue.offer("a"));
//        System.out.println(blockingQueue.offer("a"));
//        System.out.println(blockingQueue.offer("a"));
////        System.out.println(blockingQueue.offer("a"));
//        System.out.println(blockingQueue.poll());
//        System.out.println(blockingQueue.poll());
//        System.out.println(blockingQueue.poll());
//        System.out.println(blockingQueue.poll());


        /**
         * put、take
         * 队满继续put时：会一直阻塞线程直到put数据或者中断退出
         * 队空继续take时：会一直阻塞线程直到可用
         */
//        blockingQueue.put("a");
//        blockingQueue.put("a");
//        blockingQueue.put("a");
////        blockingQueue.put("a");
//        System.out.println(blockingQueue.take());
//        System.out.println(blockingQueue.take());
//        System.out.println(blockingQueue.take());
////        System.out.println(blockingQueue.take());


        /**
         * 与第二个用法差不多，超时退出
         */
        blockingQueue.offer("a", 3L, TimeUnit.SECONDS);
        blockingQueue.offer("a", 3L, TimeUnit.SECONDS);
        blockingQueue.offer("a", 3L, TimeUnit.SECONDS);
//        blockingQueue.offer("a", 3L, TimeUnit.SECONDS);
        System.out.println(blockingQueue.poll(3L, TimeUnit.SECONDS));
        System.out.println(blockingQueue.poll(3L, TimeUnit.SECONDS));
        System.out.println(blockingQueue.poll(3L, TimeUnit.SECONDS));
        System.out.println(blockingQueue.poll(3L, TimeUnit.SECONDS));

    }
```



## 值、引用传递

- 基本类型：
- 自己定义的类（引用类型）：

- str指向堆中方法区的abc，调用方法时，方法中的变量str执行堆中方法区的xxx，是两个str分别指向abc和xxx，打印的是str是main方法中的str = "abc"。

```java
String str = "abc";
test.test(str);
sout(str);

public void test(String str) {
    str = "xxx";
}
```



## 线程池

**优势：**

线程池做的工作只要是控制运行的线程池数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超过数量的线程排队等候，等其他线程执行完毕，再从队列中取出任务来执行。

**特点：**

线程复用；控制最大并发数；管理线程。

- 降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- 提高响应速度。当任务到达时，任务可以不需要等待线程创建就能立即执行。
- 提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行捅一的分配，调优和监控。



**ThreadPoolExecutor：**

![]( https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/ThreadPool04.png)

![]( https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/ThreadPool03.png)



![]( https://raw.githubusercontent.com/CoderLeftEar/Blog-Images/master/blog/ThreadPool02.png)



```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.acc = System.getSecurityManager() == null ?
        null :
    AccessController.getContext();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```



**拒绝策略：**

等待队列已经满了，再也塞不下新任务了，同时，线程池中的max线程也达到了最大，无法继续为新任务服务

这事需要拒绝策略机制合理的处理这个问题。

Runtime.getRuntime().availableProcessors();

new ThreadPoolExecutor.AbortPolicy()：

`线程总数（核心线程 + ...）+ 阻塞队列 = 总`

- AbortPolicy()：直接抛出RejectedExecutionException异常阻止系统正常运行
- CallerRunsPolicy()：“调用者运行”一种调节机制，该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者（main线程），从而降低新任务的流量。

- DiscardPolicy()：多余的不要了，允许消息丢失，弱移植性
- DiscardOldestPolicy()：抛弃队列中等待最久的任务



### ForkJoinPool、ForkJoinTask、RecursiveTask

```java
public class J14_ForkJoinPool {
    public static void main(String[] args) throws Exception {
        //
        MyTask myTask = new MyTask(0, 100);
        // 创建池子
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        // 递交任务
        ForkJoinTask<Integer> submit = forkJoinPool.submit(myTask);
        // 打印获取结果
        System.out.println(submit.get());
        // 关闭池子
        forkJoinPool.shutdown();
    }
}

class MyTask extends RecursiveTask<Integer> {

    private Integer begin;
    private Integer end;
    private Integer middle;
    private Integer result = 0;

    public MyTask(Integer begin, Integer end) {
        this.begin = begin;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        // 递归终止条件
        if ((end - begin) <= 10) {
            for (int i = begin; i <= end; i ++) {
                result += i;
            }
        } else {
            // 递归
            middle = (begin + end) / 2;
            MyTask myTask01 = new MyTask(begin, middle);
            MyTask myTask02 = new MyTask(middle + 1, end);
            // 调用fork()，会重新调用compute()
            myTask01.fork();
            myTask02.fork();
            // 合并
            result = myTask01.join() + myTask02.join();
        }
        return result;
    }
}
```



### CompletableFuture：

```java
public class J15_CompletableFuture {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
//        test();

        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
            System.out.println("没有返回值");
        });

        CompletableFuture<Integer> completableFuture2 = CompletableFuture.supplyAsync(() -> {
            System.out.println("有返回值");
            return 1024;
        });
        // a：是上面的返回结果
        // b：异常的getMessage();
        completableFuture2.whenComplete((a, b) -> {
            System.out.println(a + "---" + b);
        }).exceptionally(a -> {
            System.out.println(a.getMessage());
            return 404;
        });
        System.out.println(completableFuture2.get());


    }

    private static void test() {
        int num = 10;
        // 异步处理，从当前分支分离出去执行完其他任务再回来
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
            System.out.println(num + 10);
        });
        try { TimeUnit.MILLISECONDS.sleep(400);} catch (InterruptedException e) { e.printStackTrace();}
        System.out.println(num);
    }

}
```













哪些map实现类有序，TreeMap、LinkedHashMap如何保证有序

```JAVA
Hashmap和Hashtable 都不是有序的。
TreeMap和LinkedHashmap都是有序的。（TreeMap默认是key升序，LinkedHashmap默认是数据插入顺序）
TreeMap是基于比较器Comparator来实现有序的。
LinkedHashmap是基于链表来实现数据插入有序的。
```

那个有序实现比较好

```java
HashMap在增加数据上性能优越,linkedhashmap在增加数据上一样不错
linkedhashmap删除数据和查询上更加优秀
treemap在两种都不太好
在数据增删改动较大的情况下建议使用hashmap,在查阅较多的情况下建议使用linkedhashmap。
```

更好、更高效的实现方式

HashMap线程安全吗

```java
not
```

并发下使用什么map类，内部原理、扩容、hashcode、存储方式、默认容量

```java
hashmap 16 0.75
hashtable 11 0.75
    
ArrayList扩容为原来一半
HashMap扩容为原来一倍
```

hash表的实现、hash碰撞的解决方法

锁相关

所有线程等待某个事件的发生时，所有线程开始向下执行，CyclicBarrier、实现原理

IO/NIO

虚拟机：内存模型、类加载、GC

Spring、分布式相关、缓存、中间件、

TCP/IP、算法、源码

