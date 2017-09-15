title: Java 并发
id: 1505293843955
author: 不识
tags: []
categories:
  - java 基础
date: 2017-09-13 17:10:00
---
通过Java编程语言和Java类库中的基本并发支持，Java平台被设计为从根本上支持并行编程。自5.0版本以来，Java平台还包括高级并发API。本课介绍了平台的基本并发支持，并总结了java.util.concurrent包中的一些高级API。
***
# 进程与线程
***
在并发编程中，有两个基本的执行单位：**进程**（process）和**线程**（thread），并发编程主要涉及到线程，然而，进程也十分重要。

一个计算机系统通常有许多活动的进程和线程。即使在只有单个执行核心的系统中也是如此，因此在任何给定的时刻只有一个线程在实际执行。单个核心的处理时间通过OS的时间分片（time slicing）功能在进程和线程之间共享。

对于计算机系统拥有多个处理器，并且处理器有多个执行内核变得越来越普遍。这大大提高了系统并发执行进程和线程的能力—但是即使在简单的，没有多个处理器或执行内核的系统上也可以并发。

<!-- more -->

## 进程
***
**进程**拥有独立的执行环境。一个进程通常有一个完整的，私有的基本运行时资源集，特别每个进程都有自己的内存空间。

**进程**通常被视为程序或应用程序的代名词。然而，用户将其视为单个的应用程序可能实际上是一组协作的进程。为了促进进程之间的通信，大多数操作系统支持Inter Process Communication（IPC）资源，例如pipe和socket。**IPC**不仅用于同一个系统中的进程交流，同时还用于不同系统进程之间的交流。

大部分Java虚拟机的实现运行在一个进程中。Java应用程序可以使用**ProcessBuilder**对象创建其他进程。多进程应用程序超出了本课的范围。

## 线程
***
线程有时被称为轻量级进程。进程和线程都提供一个执行环境，但是创建一个新的线程比创建一个新的进程需要少的多的资源。

线程存在于一个进程中 — 每个进程都至少有一个线程共享进程的资源，包括内存和打开的文件。这使程序更加有效，但也导致了可能的通信问题。

多线程执行是Java平台的一个基本特征。每个应用程序有至少一个线程，或多个，如果你将"systm"线程算入，它执行诸如内存管理和信号处理等操作。但是从应用程序开发者的角度来看，你只需要一个线程，**主线程**。这个线程有能力创建其他的线程，我们将在下一节演示。

***
# Thread对象
***
每个线程与一个[Thread](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html)类实例关联。有两种策略来使用Thread对象创建一个并发应用程序：
- 为了直接控制线程创建和管理，每次应用程序需要启动异步任务时，只需实例化Thread。
- 要从你的应用程序其他部分抽象线程管理，将应用程序任务传递给一个executor。

本节讲述Thread对象的使用，Executors 在高级并发对象中讨论。

## 定义和启动一个Thread
***
一个应用程序要想创建一个Thread实例，必须要提供在这个线程中运行的代码。有两种方式：
- **提供一个Runable对象**。[Runable](https://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html)接口只定义了一个方法，run，意味着包含要在该线程中执行的代码。Runable对象被传递进Thread的构造方法中，如下示例：
```java
public class HelloRunnable implements Runnable {

    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new Thread(new HelloRunnable())).start();
    }

}
```
- **子类化Thread**。Thread类本身实现了Runable，尽管它的run方法什么也没做。一个应用程序可以子类化Thread，提供它自己的run方法是ixan，如下所示：
```java
public class HelloThread extends Thread {

    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new HelloThread()).start();
    }

}
```

注意两个示例中Thread.start是为了启动新的线程。

应该使用哪一个种方式呢？使用Runable对象的第一种更为通用，因为Runable对象可以子类化另一个不是Thread的对象。第二种在简单的应用程序中更容易使用，但受限于你的任务类必须是Thread的后代。本课重点介绍第一种方法，它将Runnable任务与执行任务的Thread对象隔离开。这种方式不仅更加灵活，而且它更适用于稍后涉及的高级线程管理。

Thread方法定义了一系列用于线程管理的方法。包含static方法，它们提供了线程调用方法的信息，或影响了线程调用方法的状态。其他的方法是从涉及管理线程和Thread对象的其他线程调用的。我们将在以下部分中查看其中的一些方法。

## sleep
***
**Thread.sleep**导致当前线程在指定的时间段内暂停执行。这是一种使处理器可用于应用程序其他线程，或其他运行在计算机系统中的应用程序的有效手段。sleep方法也可以用于pacing，如下面例子所示，并且用于等待另一个被理解为有时间要求职责的线程，如稍后SimpleThreads示例。

此外还提供了sleep方法的两个重载版本：一个指定毫秒单位的休眠时间，一个指定纳秒单位的休眠时间。然而休眠时间无法保证精确，因为它们受底层操作系统提供的设施的限制。此外，休眠期可以通过中断终止，我们将在后面的部分中看到。在任何情况下，你不能假定调用sleep方法将在指定的时间段内暂停线程。

SleepMessages示例使用sleep以四秒的间隔打印消息：
```java
public class SleepMessages {
    public static void main(String args[])
        throws InterruptedException {
        String importantInfo[] = {
            "Mares eat oats",
            "Does eat oats",
            "Little lambs eat ivy",
            "A kid will eat ivy too"
        };

        for (int i = 0;
             i < importantInfo.length;
             i++) {
            //暂停4秒
            Thread.sleep(4000);
            //打印信息
            System.out.println(importantInfo[i]);
        }
    }
}
```
注意main方法声明它throws InterruptedException。这是一个当sleep激活，而其他线程打断当前线程时sleep抛出的异常。由于这个应用程序还没有定义另一个线程来引起中断，所以它并不需要捕获InterruptedException。

## interrupt
***
中断是指示一个线程应当停止它正执行的工作，而去执行其他内容。由程序员决定线程如何响应中断，但终止线程是非常常见的。这也是本课中强调的使用方法。

一个线程通过调用**Thread**对象上的**interrupt**方法来发送一个中断指示。为使中断机制正常工作，被打断的线程必须自身支持中断。

### 支持中断
一个线程如何支持自身的中断？这取决于它目前在做什么。如果线程经常调用抛出**InterruptedException**的方法，它会在捕获该异常之后从**run**方法返回。例如，假设SleepMessages示例中的中央消息循环是在线程**Runnable**对象的**run**方法中。那么可能会如下修改以支持中断：
```java
for (int i = 0; i < importantInfo.length; i++) {
    // Pause for 4 seconds
    try {
        Thread.sleep(4000);
    } catch (InterruptedException e) {
        // We've been interrupted: no more messages.
        return;
    }
    // 我们已经中断了：没有更多的消息。
    System.out.println(importantInfo[i]);
}
```
许多抛出**InterruptedException**的方法（如sleep）被设计为在接收到中断时取消当前操作并立即返回。

如果一个线程很长时间没有调用会抛出InterruptedException的方法怎么办？那么它必须定期调用**Thread.interrupted**，如果已经接收到中断指示，则返回true。例如：
```java
for (int i = 0; i < inputs.length; i++) {
    heavyCrunch(inputs[i]);
    if (Thread.interrupted()) {
        // 我们已经被打断了：没有更多的捣蛋
        return;
    }
}
```
在这个简单的例子中，代码只是测试中断并退出线程（如果已经被接收到中断指示）。在更复杂的应用程序中，抛出**InterruptedException**可能更有意义：
```java
if (Thread.interrupted()) {
    throw new InterruptedException();
}
```
这允许中断处理代码集中在一个catch子句中。

### 中断状态标志
中断机制使用被称为中断状态（interrupt status）的内部标志来实现。调用**Thread.interrupt**设置此标志。当线程通过调用静态方法**Thread.interrupted**检查中断时，中断状态被清除。非静态的**isInterrupted**方法，由一个线程用于查询另一个线程的中断状态，不改变中断状态标志。

按照惯例，任何通过抛出InterruptedException退出的方法都会在执行此操作时清除中断状态。但是，通过另一个线程调用**interrupt**，始终可以立即重新设置中断状态。

## join
***
**join**方法允许一个线程等待另一个线程的完成。如果**t**是一个**Thread**对象，它的线程正在被执行,
```java
t.join();
```
导致当前线程暂停执行，直到t的线程终止。join的重载方法允许程序员指定等待的时间。但是，与sleep一样，join依赖于操作系统的校时，所以你不应该假设join会等待你指定的时间。像sleep一样，join通过抛出InterruptedException响应一个中断。

# java内存模型
***
如果要设计正确行为的并发程序，了解Java内存模型非常重要。Java内存模型指定不同的线程如何以及何时可以看到其他线程写入到共享变量的值，并且如何在必要时同步访问一个共享变量。

>原始的Java内存模型不足，所以Java内存模型在Java 1.5中进行了修改。此版本的Java内存模型仍然在Java 8中使用。

## 内部Java内存模型
***
JVM在内部将Java内存模型














***
# 同步
***
线程主要通过共享对字段的访问和对象引用字段进行通信。这种形式的通信是非常有效的，但可能造成两种错误：**线程干扰**（thread interference）和**内存一致性错误**（memory consistency errors）。此时需要**同步**（sychronization）来防止这些错误。

然而，当两个或更多线程尝试同时访问相同的资源的时候，同步也引入了**线程竞争**（thread contention）问题,并且导致Java运行时执行一个或多个线程 更加缓慢，甚至暂停执行。饥饿与活锁（Starvation and Livelock）就是线程竞争的一种形式。





## 线程干扰
***
考虑一个Counter这个简单类：
```java
class Counter {
    private int c = 0;

    public void increment() {
        c++;
    }

    public void decrement() {
        c--;
    }

    public int value() {
        return c;
    }

}
```



