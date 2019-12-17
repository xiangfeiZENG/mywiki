---
title: Java笔记-并发
date: 2018-04-25 12:21:20
categories: java
tags: java
---

## Java笔记-并发

并发解决的问题大体上可以分为“速度”和“设计可管理性“两种。

并发通常是提高运行在单处理器上的程序的性能，这是因为阻塞，事实上，从性能角度看，如果没有任务会阻塞，name在单处理器上使用并发就没有任何意义。单处理器系统中的性能提高常见示例是事件驱动的编程。

实现并发最直接的方式是在操作系统级别使用进程，进程间资源隔离。与此相反的是，Java所使用的并发系统会共享诸如内存和I/O这样的资源，因此编写多线程程序最基本的困难在于协调不同线程驱动的任务之间对这些资源的使用，以使得这些资源不会被多个任务访问。

### 基本的线程机制

并发编程使我们可以将程序划分为多个分离的、独立运行的任务。通过使用多线程机制，这些独立任务（也被称为子任务）中的每一个都将由执行线程来驱动。

#### 定义任务

线程可以驱动任务，因此你需要一种描述任务的方式，这可以由Runnable接口来提供。要想定义任务，只需事先Runnable接口并编写run()方法，使得该任务可以执行你的命令。Thread.yield()的调用对线程调度器（Java线程机制的一部分，可以讲cpu从一个线程转移给另一个线程）的一种**建议**。

```java
public class LiftOff implements Runnable {
  protected int countDown = 10; // Default
  private static int taskCount = 0;
  private final int id = taskCount++;
  public LiftOff() {}
  public LiftOff(int countDown) {
    this.countDown = countDown;
  }
  public String status() {
    return "#" + id + "(" +
      (countDown > 0 ? countDown : "Liftoff!") + "), ";
  }
  public void run() {
    while(countDown-- > 0) {
      System.out.print(status());
      Thread.yield();
    }
  }
} ///:~
```

#### Thread类

将Runnable对象转变为工作任务的传统方式是把它提交给一个Thread构造器。start()迅速地反悔了，因为Waiting for LiftoOff消息在倒计时完成之前就出现了。实际上，LiftOff.run()是由不同的线程执行的，因此你仍旧可以执行main()线程中的其他操作（这种能力并不局限于main()线程，任何线程都可以启动另一个线程）。

```java
public class MoreBasicThreads {
  public static void main(String[] args) {
    for(int i = 0; i < 5; i++)
      new Thread(new LiftOff()).start();
    System.out.println("Waiting for LiftOff");
  }
} /* Output: (Sample)
Waiting for LiftOff
#0(9), #1(9), #2(9), #3(9), #4(9), #0(8), #1(8), #2(8), #3(8), #4(8), #0(7), #1(7), #2(7), #3(7), #4(7), #0(6), #1(6), #2(6), #3(6), #4(6), #0(5), #1(5), #2(5), #3(5), #4(5), #0(4), #1(4), #2(4), #3(4), #4(4), #0(3), #1(3), #2(3), #3(3), #4(3), #0(2), #1(2), #2(2), #3(2), #4(2), #0(1), #1(1), #2(1), #3(1), #4(1), #0(Liftoff!), #1(Liftoff!), #2(Liftoff!), #3(Liftoff!), #4(Liftoff!),
*///:~
```

当main()创建Thread对象时，并没有捕获任何这些对象的引用。每个Thread都“注册”了它自己，因此确实有一个对它的引用，而且在它的任务退出其run()并死亡之前，垃圾回收器无法清除它。

#### 使用Executor



