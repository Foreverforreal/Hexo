title: Java 并发
id: 1505293843955
author: 不识
tags: []
categories:
  - java 基础
  - java 并发
date: 2017-09-13 17:10:00
---
通过Java编程语言和Java类库中的基本并发支持，Java平台被设计为从根本上支持并行编程。自5.0版本以来，Java平台还包括高级并发API。本课介绍了平台的基本并发支持，并总结了java.util.concurrent包中的一些高级API。
***
# 进程与线程
***
在并发编程中，有两个基本的执行单位：**进程**（process）和**线程**（thread），并发编程主要涉及到线程，然而，进程也十分重要。
<!-- more -->
一个计算机系统通常有许多活动的进程和线程。即使在只有单个执行核心的系统中也是如此，因此在任何给定的时刻只有一个线程在实际执行。单个核心的处理时间通过OS的时间分片（time slicing）功能在进程和线程之间共享。

对于计算机系统拥有多个处理器，并且处理器有多个执行内核变得越来越普遍。这大大提高了系统并发执行进程和线程的能力—但是即使在简单的，没有多个处理器或执行内核的系统上也可以并发。



## 进程
***
**进程**拥有独立的执行环境。一个进程通常有一个完整的，私有的基本运行时资源集，特别每个进程都有自己的内存空间。

**进程**通常被视为程序或应用程序的代名词。然而，用户将其视为单个的应用程序可能实际上是一组协作的进程。为了促进进程之间的通信，大多数操作系统支持Inter Process Communication（IPC）资源，例如pipe和socket。**IPC**不仅用于同一个系统中的进程交流，同时还用于不同系统进程之间的交流。

大部分Java虚拟机的实现运行在一个进程中。Java应用程序可以使用**ProcessBuilder**对象创建其他进程。多进程应用程序超出了本课的范围。

## 线程
***
线程有时被称为轻量级进程。进程和线程都提供一个执行环境，但是创建一个新的线程比创建一个新的进程需要少的多的资源。

线程存在于一个进程中 — 每个进程都至少有一个线程共享进程的资源，包括内存和打开的文件。这使程序更加有效，但也导致了可能的通信问题。

多线程执行是Java平台的一个基本特征。每个应用程序有至少一个线程，或多个，如果你将"system"线程算入，它执行诸如内存管理和信号处理等操作。但是从应用程序开发者的角度来看，你只需要一个线程，**主线程**。这个线程有能力创建其他的线程，我们将在下一节演示。

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
**join**方法允许一个线程等待另一个线程的完成。它需要在start方法之后调用。如果**t**是一个**Thread**对象，它的线程正在被执行,
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
Java内存模型在JVM内部被使用，将内存分为**线程栈**（stack）和**堆**（heap）。下面示图从逻辑角度说明了Java内存模型：
![Java内存模型1](/images/java base/java-memory-model-1.png)
每个Java虚拟机上运行的线程都有它自己的线程栈。<font style="color:#ec70ae;">线程栈包含了线程达到当前执行点调用方法的信息。此外还包含每个正执行方法(调用栈中的所有方法)的局部变量</font>。一个线程只可以访问自己的线程栈，由线程栈创建的局部变量对其他线程都不可见，即使两个线程执行相同的代码。

所有原始类型 ( boolean, byte, short, char, int, long, float, double)的局部变量都完全存储在**线程栈**上，因此对其他线程不可见。**堆**包含所有你的Java应用程序创建的对象，无论是哪个线程创建的。不管一个对象是被创建并分配给一个局部变量，或者作为其他对象的成员变量创建，这个对象依旧存储在堆上。

下面一个图示显示了存储在线程栈上的调用栈和局部变量以及存储在堆上的对象：
![Java内存模型1](/images/java base/java-memory-model-2.png)
1. 一个局部变量可能是原始类型，这是它完全存储在线程栈上。只有局部变量存储在线程栈上。
2. 一个局部变量也可能是一个对象的引用，这种情况引用（局部变量）存储在线程栈上，而对象本身存储在堆中
3. 一个对象的成员变量与对象本身一起存储在堆上。无论成员变量是原始类型还是引用类型。
4. <font style="color:#ec70ae;">静态类变量也与类定义一起存储在堆上。</font>
5. 堆上的对象可以被所有拥有它的引用的线程访问。当一个线程访问一个对象时，它也可以访问对象的成员变量，但每个线程都有它自己的局部变量（对象引用）。

## 硬件内存架构
***
代硬件内存架构与内部Java内存模型有所不同。以下是现代计算机硬件架构的简化图：'
![硬件内存架构](/images/java base/java-memory-model-4.png)
现代计算机经常有2个或更多的CPU。其中一些CPU也可能有多个内核。也就是说，在有2个或更多个CPU的现代计算机上，可以同时运行多个线程。每个CPU都可以在任何给定的时间运行一个线程。这意味着如果你的Java应用程序是多线程的，则每个CPU可能会在Java应用程序中同时（并发）运行一个线程。

每个CPU都包含一组本质上是CPU内存的**寄存器**。CPU可以在寄存器上对变量执行比在主内存上更快的操作。这是因为CPU可以访问这些寄存器比访问主内存的速度快得多。

每个CPU可能还有CPU缓存层。事实上，大多数现代CPU都有一些大小的缓存层。CPU可以比访问主内存更快的访问它的缓存，但通常不如访问它的内部寄存器快。因此，CPU缓存访问速度介于内部寄存器和主内存之间。某些CPU可能有多个缓存（1级和2级），但是了解Java内存模型如何与内存进行交互这一点并不重要。重要的是知道CPU可以具有某种缓存层。

计算机还包含主内存区（RAM）。所有CPU都可以访问主内存。主内存区通常远大于CPU的高速缓存。

通常，当一个CPU需要访问主内存，它会读取主内存一部分到它的CPU缓存，甚至可以将缓存的一部分读入其内部寄存器，然后在上面执行某些操作。当CPU需要将结果写入到主内存，它会将值从它的内部寄存器刷入到缓存，并在某些时候将值刷入到主内存。

存储在缓存中的值通常在CPU需要在缓存存储其他东西时，被刷回到主内存中。CPU可以以此只读写部分其中的数据，而不用在更新时读写所有的缓存。通常缓存以更小的被称为“缓存行”的内存块被更新。可以将一个或多个缓存行读入缓存中，并且一个或多个缓存行可以再次刷新回主内存。

## 原子性访问
***
编程中，原子性动作是可以一次有效完成的动作。它不会在中途停止：也就是要么一次完成，要么根本没有发生。有些表达式看似简单，但并不是原子性动作，比如自加操作a++,它实际是分成几个步骤完成的。这里有一些动作是原子性的：
- 引用类型变量和大多数原始类型变量（除了long和double）的读写是原子性的
- 所有声明为volatile的变量（包括long和double）都是原子性的

原子性操作不用担心多线程干扰，但依然还有可能发生内存一致性错误，此时应该使用volatile关键字减少这些错误。java.util.concurrent包中的一些类提供了不依赖同步的原子性方法。

## Java内存模型与硬件内存架构
***
如前提到，Java内存模型与硬件内存架构并不相同。硬件内存架构并不区分**线程栈**和**堆**。在硬件上，**线程栈**和**堆**都位于**主内存**中。线程栈和堆的一部分有时可能存在于CPU缓存和内部CPU寄存器中。如图所示：
![Java内存模型5](/images/java base/java-memory-model-5.png)
当对象和变量可以存储在计算机的各种不同的存储区域中时，可能会出现某些问题。两个主要问题是：
- 线程更新（写入）共享变量的可见性
- 当读写检查共享变量时的竟态条件

### 共享对象的可见性
如果两个或更多线程共享一个对象，当其中一个线程更新这个对象时，可能对另一个线程不可见。
如下图所示，当一个线程复制共享对象到CPU缓存中，并且将它的count变量值改为2，而这个更改还没有刷回主内存的时候，它是对另一个线程不可见的线程，这导致另一个线程复制共享对象到缓存的时候，count值依旧是1。
![线程可见性](/images/java base/java-memory-model-6.png)

这个问题的解决方法是使用Java **volatile**关键字。**volatile**关键字可以确保给定的变量直接从主内存中读取，并在更新时总是写回主内存。
### 竟态条件
如果两个或更多线程共享一个对象，当超过一个线程更新这个对象中的变量时，**竟态条件**就会发生。
如下图所示，两个线程同时访问一个共享变量的成员变量count=1，每个线程都同时对count进行加1操作，我们期望的是count结果加3，但是由于每个线程在其CPU缓存都有一份count副本，进行操作后重新刷回主内存，count值为2，并不是预期结果。
![竟态条件](/images/java base/java-memory-model-7.png)
这个问题的解决方法是使用Java **synchronized**块。同步块保证在任何给定时间只有一个线程可以进入代码的给定临界区。同步块还保证在同步块内访问的所有变量将从主内存中读入，并且当线程退出同步块时，所有更新的变量将被刷回主内存，而不管该变量是否被声明为**volatile**。

***
# volatile关键字
***
Java volatile关键字用于标记变量“存储在主内存”中。更准确地说，这意味着，每次读取volatile变量都将从计算机的主内存中读取，而不是从CPU缓存中读取，并且每次volatile变量的写入将直接写入主内存中，而不仅仅是写入CPU缓存。

实际上，由于Java 5的volatile关键字不是仅仅保证了变量从主内存中读写。

## 可见性保证
***
Java volatile关键字保证了跨线程的变量更改可见性。假设计算机是多CPU的，可以同时运行多个线程，有一个变量count，只有线程一可以操作count，而线程一和线程二都可以读取count，如果count不使用volatile修饰，线程一将count加1，并将结果存入CPU缓存，还没有刷回主内存，此时线程二读取count的值将不是最新值，造成内存不一致。如果count使用volatile修饰，则count的写入都会被直接写回主内存，同时count的读取也都会直接从主内存中读取。确保了线程之间读写变量的可见性。

## Happens-Before保证
***
从Java 5开始，volatile关键字不仅保证了直接从主内存直接读写变量，还保证了：
- 如果线程A写入一个volatile变量，线程B随后读取同一个volatile变量，那么在写入volatile变量之前，所有对线程A可见的变量，在线程B读取该volatile变量后，同样对线程B可见。
- volatile变量的读写指令无法被JVM重排序（JVM可能会出于性能原因重新排序指令，只要JVM探测到不会对程序行为有影响的话）。之前和之后的指令可以重排序，但volatile变量读写指令相对位置不会改变。

更详细的解释是：
当一个线程写入一个volatile变量时，不仅将volatile变量本身写入主内存。在写入volatile变量之前，线程更改的所有其他变量也会刷入到主内存。当一个线程读取一个volatile变量时，它也将从主内存中读取与volatile变量一起刷入到主内存的所有其他变量。如下例所示
```java
Thread A:
    sharedObject.nonVolatile = 123;
    sharedObject.counter     = sharedObject.counter + 1;

Thread B:
    int counter     = sharedObject.counter;
    int nonVolatile = sharedObject.nonVolatile;
```
由于线程A在写入volatile变量sharedObject.counter之前写入了变量sharedObject.counter,那么在线程A写入sharedObject.counter（volatile）时，sharedObject.nonVolatile和sharedObject.counter都会被写入到主内存。

由于线程B首先读取volatile变量sharedObject.counter，那么sharedObject.counter和sharedObject.nonVolatile都会从主内存读取到线程B使用的CPU缓存中。当线程B读取sharedObject.nonVolatile时，它将看到线程A写入最新值。

JVM不会重排序volatile变量读写指令意思如下：
```java
sharedObject.nonVolatile1 = 123;
sharedObject.nonVolatile2 = 456;
sharedObject.nonVolatile3 = 789;

sharedObject.volatile = true; //一个volatile变量

int someValue1 = sharedObject.nonVolatile4;
int someValue2 = sharedObject.nonVolatile5;
int someValue3 = sharedObject.nonVolatile6;
```
sharedObject.volatile之前与之后的三个变量读写指令都可以由JVM出于性能原因重排序，只要保证之前的指令排序后依然在sharedObject.volatile 之前，之后的依然在之后。

开发人员可以使用这种扩展的可见性保证来优化线程之间变量的可见性。而不用声明每一个变量为volatile，只需要一个或几个需要声明为volatile。如下面Exchanger类示例：
```java
public class Exchanger {

    private Object   object       = null;
    private volatile hasNewObject = false;

    public void put(Object newObject) {
        while(hasNewObject) {
            //等待 - 不会覆盖已有的新对象
        }
        object = newObject;
        hasNewObject = true; //volatile write
    }

    public Object take(){
        while(!hasNewObject){ //volatile read
            //等待 - 不会取走旧对象 (或 null)
        }
        Object obj = object;
        hasNewObject = false; //volatile write
        return obj;
    }
}
```
线程A可能会通过调用put()来不时地放置对象。线程B可能会通过调用take()来不时地接收对象。只要线程A调用put()并且只有线程B调用take()，这个Exchanger可以使用volatile变量（不使用synchronized块）就正常工作。

put()方法中的变量写入指令顺序不能改变，如果改为
```java
while(hasNewObject) {
    //等待 - 不会覆盖已有的新对象
}
hasNewObject = true; //volatile write
object = newObject;
```
则会导致两个问题，首先，可能在线程A执行到设置object值之前，线程B就看到hasNewObject设置为true，其次，无法保证在object设置的值什么时候被刷回到主内存中，对线程B可见。而修改前的顺序，通过volatile的Happens-Before保证，确保了object对象值会随着hasNewObject一起立即被刷回到主内存。

此外，Thread的两个操作也创建了这种happens-before 关系：
- 当一个语句调用Thread.start方法，每个对该语句有happens-before关系的语句，同样对新线程内执行的语句有happens-before关系。这导致创建新线程的代码的效果对于新线程是可见的。
- 当一个线程终止，并导致Thread.join在另一个线程返回，那么由该终止线程执行的语句与join结束后的所有语句拥有happen-before关系。此线程中代码的效果是对join调用处的线程可见的。

## volatile不足
***
如前面例子，当一个线程读取或写入一个共享变量，另一个线程只能读取该共享变量，此时通过volatile关键字可以保证程序如预期运行。volatile关键字可以用于32位和64位的变量。

然而，实际可能多个线程同时读写一个共享的volatile变量，如果新的写入值并不依赖它之前的值，那么在主内存中依旧是正确的值，但是如果依赖之前的值，此时volatile再不能确保正确的可见性，因为两个线程可能同时读取一个共享变量，并且对其进行写入，这时就产生了一个**竞态条件**。此时两个线程写回主内存的值可能会互相覆盖，导致非预期的值。这种情况下需要使用synchronized 关键字来确保变量读写的原子性。


***
# synchronized关键字
***
synchronized关键字可以标记一个方法或代码块同步，称为同步方法（ synchronized methods）和同步语句（synchronized statements）。使用synchronized块可以避免静态条件。Java中的同步块在某个对象上同步。在同一个对象上同步的所有同步块在同一时间内只能执行一个线程。所有其他尝试进入代码块的线程都会被阻塞，直到同步块内的线程退出同步块。
synchronized关键字可用于标记四种不同类型的代码块：
- **实例方法**
- **静态方法**
- **实例方法内代码块**
- **静态方法内代码块 **


## 内部锁
***
同步是围绕着称为内部锁（ intrinsic lock）或监视器锁（monitor lock）的内部实体构建的。内部锁在同步中有作用：强制执行对对象状态的独占访问，并建立对可见性至关重要的happen-before关系。

每个对象都有一个与之相关的内部锁。按照约定，一个线程要想对对象字段进行排他和一致性的访问，在访问之前必须获得该对象的内部锁，然后在完成对象时释放内部锁。线程在获得锁和释放锁的之间被称为拥有内部锁定。只要一个线程拥有对象的内部锁，其他线程都无法获得该锁。当其他尝试获取锁时，会被阻塞。

当一个线程释放内部锁，它与后续获取该锁动作建立了一个happen-before关系。

## 同步方法
***
同步方法分为同步实例方法和同步静态方法，只需要简单地在方法声明上加上**synchronized**关键字。
- 同步实例方法
```java
 public synchronized void add(int value){
      this.count += value;
  }
```
- 同步静态方法
```java
  public static synchronized void add(int value){
      count += value;
  }
```
当一个线程调用同步方法的时候，它自动获得该方法对象的内部锁，并在返回的时候释放它。即使是由未捕获的异常引发的返回，依旧会释放该锁。对于实例同步方法，它的内部锁为调用该方法的实例，对于静态同步方法，它的内部锁为方法所在类的Class实例。

同步方法有两个作用：
1. 同一个对象的所有同步方法无法被多个线程同时调用。当一个线程调用一个对象上的同步方法，其他调用该对象同步方法的线程都处于阻塞状态，知道正在执行的线程返回。
2. 当一个同步方法退出的时候，它自动与该对象同步方法随后的调用建立happen-before关系。这保证了对对象状态的更改对其他线程可见。

注意synchronized关键字无法用在构造方法上，否则的话会导致语法错误。同步构造方法没有意义，因为只有创建对象的线程在构造时才能访问它。

> 当构造一个会在多线程中共享的对象的时候，要非常小心该对象引用的过早“泄漏”。例如假如你像维持一个名为instances的List来保存所有类的实例。你可能会在你的构造方法中添加以下代码
```java
instances.add(this);
```
> 但是其他线程可以使用instances在该对象构造完成前就访问该对象。


同步方法可以有效的避免线程干扰和内存一致性错误，如果一个对象对多个线程可见，则对该对象变量的所有读取或写入都是通过同步方法完成的。除了一个重要例外，final字段，它不会在对象构造后被修改，所以可以安全的通过非synchronized方法读取。

## 同步语句
***
有时不需要将整个方法同步，只需要同步方法内的一部分，这时可以使用同步语句，同步语句分实例方法内的同步语句和静态方法内的同步语句，与同步方法不同是，同步语句必须指定一个对象来提供内部锁。如下：
```java
public void add(int value){
    synchronized(this){
       this.count += value;   
    }
  }
```
同步语句提供内部锁的对象被称为**监视器对象**（monitor object），上面示例中使用this作为监视器对象，它是调用add方法的实例。对于静态方法中的同步语句，由于静态方法是与类相关的，所以需要使用一个类的Class实例作为监视器对象。

同步语句通过指定监视器对象，提升了更细粒度的同步控制。例如，有一个MsLunch 类有两个字段c1和c2，它们从不在一起使用，所有对该字段的更新都必须是同步的，但没有理由在更新c1的时候阻止c2的更新，因此相比使用同步方法，可以通过使用同步语句指定不同的内部锁来实现。
```java
public class MsLunch {
    private long c1 = 0;
    private long c2 = 0;
    private Object lock1 = new Object();
    private Object lock2 = new Object();

    public void inc1() {
        synchronized(lock1) {
            c1++;
        }
    }

    public void inc2() {
        synchronized(lock2) {
            c2++;
        }
    }
}
```


## Java并发工具
***
synchronized机制是Java第一个同步访问多线程共享对象的机制。synchronized机制不是很先进。这就是为什么Java 5得到了一整套并发工具类，以帮助开发人员实现比synchronized的更精细的并发控制。


***
# ThreadLocal
***
Java中的ThreadLocal类允许创建只能由同一个线程读取和写入的变量。因此，即使两个线程正在执行相同的代码，并且代码对ThreadLocal变量进行引用，那么两个线程也看不到彼此的ThreadLocal变量。

```java
private ThreadLocal<String> myThreadLocal = new ThreadLocal<String>();

myThreadLocal.set("Hello ThreadLocal");
String threadLocalValue = myThreadLocal.get();
```
如上初始化ThreadLocal变量，并且使用set()方法设置值，get()方法获取值。由于设置在ThreadLocal对象上的值仅对设置该值的线程是可见的，所以线程不能使用set()设置一个对所有线程都可见的初始值。但是可以通过子类话ThreadLocal，并重写它的**initialValue()**方法来设置一个初始化值：
```java
private ThreadLocal myThreadLocal = new ThreadLocal<String>() {
    @Override protected String initialValue() {
        return "This is the initial value";
    }
};  
```
现在所有线程在调用set()之前调用get()时会看到相同的初始值。

***
# 线程信令
***
线程信令的目的是使线程能够互相之间发送信号。此外，线程信令使线程能够等待来自其他线程的信号。例如，线程B可能等待来自线程A的信号，指示数据准备好被处理。
## 使用共享对象
***
实现线程相互发送信号的一种简单方式是通过将信号值设置在某些共享对象变量中。线程A可以从同步块内部将boolean成员变量hasDataToProcess设置为true，线程B也可以在同步块内部读取hasDataToProcess成员变量。这是一个可以容纳这种信号的对象的简单示例，并提供设置和检查它的方法：
```java
public class MySignal{

  protected boolean hasDataToProcess = false;

  public synchronized boolean hasDataToProcess(){
    return this.hasDataToProcess;
  }

  public synchronized void setHasDataToProcess(boolean hasData){
    this.hasDataToProcess = hasData;  
  }

}
```
处理数据的线程B正在等待数据可用于处理。换而言之，它正在等待来自线程A的信号，这会导致hasDataToProcess()返回true。线程B正在运行循环，同时等待此信号：
```java
protected MySignal sharedSignal = ...

...

while(!sharedSignal.hasDataToProcess()){
  //do nothing... busy waiting
}
```
注意在hasDataToProcess()返回true之前，while循环如何保持执行。这被称为**忙等待**(busy waiting)。

## wait(), notify()和notifyAll()
***
在运行等待线程时，忙等待对CPU的利用不是非常高效的，除非平均的等待时间非常少。如果使等待的线程可以休眠或者变为待用，可能会更明智些。

Java有一个内置的等待机制，使得线程在等待信号时变为不活动。java.lang.Object类定义了**wait()**，**notify()**和**notifyAll()**这三个方法。

一个线程可以在任何对象上调用wait()方法时线程不活动，直到另一个线程在该对象上调用notify()。为了调用wait()和notify()，调用线程必须首先获得监视器对象上的锁。换句话说，调用线程必须从同步块内部调用wait()或notify()。这是MySignal的修改版本MyWaitNotify，它使用wait()和notify()。
```java

public class MonitorObject{
}

public class MyWaitNotify{

  MonitorObject myMonitorObject = new MonitorObject();

  public void doWait(){
    synchronized(myMonitorObject){
      try{
        myMonitorObject.wait();
      } catch(InterruptedException e){...}
    }
  }

  public void doNotify(){
    synchronized(myMonitorObject){
      myMonitorObject.notify();
    }
  }
}
```
等待线程可以调用doWait(),而唤醒线程可以调用doNotify()。当一个线程在对象上调用notify(),在该线程上等待的一个线程就会被唤醒继续执行。还有一个notifyAll()方法会唤醒所有在给定对象上等待的线程。

如上所见，调用wait()和notify()的调用都在同步块中，这是强制的。如果没有持有这些方法对象上的锁的话，一个线程无法调用wait(),notify()和notifyAll()方法。如果调用，会抛出IllegalMonitorStateException 异常。

一旦线程调用了wait()方法，它会释放它持有的该监视器对象上的锁。此时其他线程可以获得该锁。
<font style="color:#ec70ae;">一旦线程被唤醒，它不能立即退出wait()方法，要一直到调用notify()的线程离开它的同步块。</font>换而言之，唤醒的线程必须在它可以退出wait()方法前，重新获得监视器对象上锁,因为wait的调用嵌套在同步块内。如果多个线程被使用notifyAll()唤醒，一次只有一个唤醒的线程可以退出wait()方法，因为每个线程必须在退出wait()之前依次获取监视对象上的锁。

## 信号丢失
***
notify（）和notifyAll（）方法不会将保存方法调用，以防止调用时没有线程处于wait状态。然后通知信号就会丢失。因此，如果一个线程在其他线程调用wait()前调用了notify()，信号将被等待线程错过。这可能是或可能不是一个问题，但在某些情况下，这可能导致等待线程永远等待，永远不会醒来，因为唤醒的信号被错过了。

为了避免丢失信号，信号应该存储在信号类中。在MyWaitNotify示例中，通知信号应存储在MyWaitNotify实例中的成员变量中。以下是MyWaitNotify的修改版本：
```java
public class MyWaitNotify2{

  MonitorObject myMonitorObject = new MonitorObject();
  boolean wasSignalled = false;

  public void doWait(){
    synchronized(myMonitorObject){
      if(!wasSignalled){
        try{
          myMonitorObject.wait();
         } catch(InterruptedException e){...}
      }
      //clear signal and continue running.
      wasSignalled = false;
    }
  }

  public void doNotify(){
    synchronized(myMonitorObject){
      wasSignalled = true;
      myMonitorObject.notify();
    }
  }
}
```

## 虚假唤醒
***
由于不可思议的原因，即使notify（）和notifyAll（）尚未被调用，线程也可以唤醒。这被称为虚假唤醒。唤醒没有任何理由。

如果在MyWaitNofity2类的doWait（）方法中发生虚假唤醒，则等待线程可能会没有收到信号就继续处理！这可能会导致您的应用程序的严重问题。

为了防止虚假唤醒，信号成员变量在一个while循环内被检查，而不是在if语句内部。这样的while循环也称为**自旋锁**。线程直到自旋锁（while循环）中的条件变为false才会真正唤醒。这是MyWaitNotify2的修改版本：
```java
public class MyWaitNotify3{

  MonitorObject myMonitorObject = new MonitorObject();
  boolean wasSignalled = false;

  public void doWait(){
    synchronized(myMonitorObject){
      while(!wasSignalled){
        try{
          myMonitorObject.wait();
         } catch(InterruptedException e){...}
      }
      //清楚信号，继续运行。
      wasSignalled = false;
    }
  }

  public void doNotify(){
    synchronized(myMonitorObject){
      wasSignalled = true;
      myMonitorObject.notify();
    }
  }
}
```
注意wait（）调用如何嵌套在while循环而不是if语句中。如果等待线程在没有收到信号的情况下唤醒，则isSignalled成员仍将是false，while循环将再次执行，导致唤醒的线程返回等待。

## 多个线程等待同一个信号
***
当有多个线程等待，并且被notifyAll()全部唤醒的，但只有一个线程应该运行被执行，此时这个while循环也是一个很好的解决方案。一次只能有一个线程能够获取监视器对象上的锁，这意味着只有一个线程可以退出wait()调用并且清除wasSignalled标记。一旦该线程在doWait()方法中退出同步块，其他线程就可以退出wait()调用，并检查while循环内的wasSignalled成员变量。然而，这个标志被第一个线程清除，所以其余的唤醒的线程返回等待，直到下一个信号到达。

##  不要在String或全局对象上调用wait()方法
***
由于JVM/编译器别不将常量字符串转换为同一个对象，因此如果使用字符串作为监视器对象，如MyWaitNotify定义“String myMonitorObject = "";”，这将导致一个问题，即使创建多个MyWaitNotify实例，但监视器对象还是同一个，在多个线程等待唤醒的调用中将出现混乱。

所以：不要对wait（）/ notify（）机制使用全局对象，字符串常量等。使用使用它的构造是唯一的对象。例如，每个MyWaitNotify3（早期部分的示例）实例都有自己的MonitorObject实例，而不是为wait（）/ notify（）调用使用空字符串。

***
# 线程生命周期
***
![线程生命周期](/images/java base/thread-life.png)
创建并运行线程：

① **新建状态**(New Thread)：在Java语言中使用new操作符创建一个线程后，该线程仅仅是一个空对象，它具备类线程的一些特征，但此时系统没有为其分配资源，这时的线程处于创建状态。
线程处于创建状态时，可通过Thread类的方法来设置各种属性，如线程的优先级(setPriority)、线程名(setName)和线程的类型(setDaemon)等。

②**就绪状态**(Runnable)：使用start()方法启动一个线程后，系统为该线程分配了除CPU外的所需资源，使该线程处于就绪状态。此外，如果某个线程执行了yield()方法，那么该线程会被暂时剥夺CPU资源，重新进入就绪状态。

③**运行状态**(Running)：Java运行系统通过调度选中一个处于就绪状态的线程，使其占有CPU并转为运行状态。此时，系统真正执行线程的run()方法。
- 可以通过Thread类的isAlive方法来判断线程是否处于就绪/运行状态：当线程处于就绪/运行状态时，isAlive返回true，当isAlive返回false时，可能线程处于阻塞状态，也可能处于停止状态。

④ **阻塞和唤醒线程**
**阻塞状态**(Blocked)：一个正在运行的线程因某些原因不能继续运行时，就进入阻塞 状态。这些原因包括：
1. 当执行了某个线程对象的sleep()等阻塞类型的方法时，该线程对象会被置入一个阻塞集内，等待超时而自动苏醒。
2. 当多个线程试图进入某个同步区域时，没能进入该同步区域的线程会被置入锁定集，直到获得该同步区域的锁，进入就绪状态。
3. 当线程执行了某个对象的wait()方法时，线程会被置入该对象的等待集中，知道执行了该对象的notify()方法wait()/notify()方法的执行要求线程首先获得该对象的锁。

⑤**死亡状态**(Dead)：线程在run()方法执行结束后进入死亡状态。此外，如果线程执行了interrupt()或stop()方法，那么它也会以异常退出的方式进入死亡状态。

终止线程的三种方法
①使用退出标志，使线程正常退出，也就是当run方法完成后线程终止，推荐使用。
②使用stop方法强制终止线程(这个方法不推荐使用，因为stop和suppend、resume一样，也可能发生不可预料的结果)。
③ 使用interrupt方法中断线程。



***
# 不可变对象
***
如果一个对象在创建之后状态不能改变，那么它被认为是一个不可变对象。不可变对象在并发应用程序中特别有用。由于它们不能改变状态，它们不会被线程干扰所破坏或者在不一致状态下观察到。

程序员通常不愿使用不可变对象，因为他们担心创建一个新对象的花销，而不是更新一个对象。对象创建的影响往往被高估，它可以通过与不可变对象的一些有点带来的效率来抵消。这些包括由于垃圾回收而导致的开销降低，以及为消除为了保护可变对象免受损坏所需的代码。

以下规则定义了创建不可变对象的简单策略。不是所有“不可变”的类都遵循这些规则。这并不一定意味着这些类的创作者都是草率的 - 他们可能有充分的理由相信他们的类在构造后永远不会改变。然而，这种策略需要复杂的分析，不适合初学者。
1. 不提供“setter”方法 — 修改字段或引字段引用对象的方法。
2. 使所有的字段final和private
3. 不允许子类重写方法。最简单的方法是将类声明为final。更复杂的方法是使构造函数为私有并在工厂方法中构造实例。
4. 如果实例字段包含可变对象的引用，则不允许更改这些对象：
	- 不提供修改这些可变对象的方法
	- 不共享可变对象的引用。
	不要存储引用到外部，可变对象传递给构造方法；如果需要，创建一个副本，并存储副本引用。同样，如果需要的时候创建内部可变对象副本，以避免在方法中返回原始可变对象。
	

***
# 高级并发对象
***
前面是是一些低级并发API，这些API足够用于非常基本的任务，但更高级的构建模块需要更高级的任务。对于充分利用当今多处理器和多核系统的大规模并发应用程序尤其如此。在Java 5.0中引入了一些高级并发功能，大多数这些功能都在新的java.util.concurrent包中实现。 Java Collections Framework（JCF）中还有新的并发数据结构。

## Lock
***
同步代码依赖于一种简单的重入锁。这种锁便于使用，但也有很多限制。java.util.concurrent.locks包支持更多复杂的同步锁功能。其中最基本的是Lock接口。

Lock对象非常向同步代码块使用的隐式锁。与隐式锁一样，一次只能有一个线程拥有一个Lock对象。锁定对象还通过其关联的Condition对象支持wait/notify机制。

## Executors
***
前面例子中，在由Runable对象定义的要使用线程完成的任务，与有Thread对象定义的线程本身有着紧密联系。这适用于小型应用程序，但在大规模应用程序中，将线程管理和创建与其他应用程序分开是有意义的。封装这些函数的对象被称为执行器（executor），以下是执行器的一些细节：
- Executor接口：定义了三个执行器对象类型
- 线程池：最常用的执行器实现方式。
- fork/join：一个用于利用多个处理器的框架（JDK 7中的新功能）

### Executor接口
java.util.concurrent包定义了三个执行器接口：
- **Executor**，一个支持启动新任务的简单界面。
- **ExecutorService**，Executor的一个子接口，它增加了帮助管理独立任务和执行者本身生命周期的功能。
- **ScheduledExecutorService**，ExecutorService的一个子接口，支持将来和/或定期执行任务。

通常，引用执行器对象的变量被声明为这三种接口类型之一，而不是执行器类类型。

#### Executor接口
Executor接口只提供了一个方法，execute，被设计为普通线程创建形式的替代品。。如果r是一个Runnable对象，e是一个Executor对象，可以将
```java
(new Thread(r)).start();
```
换为
```java
e.execute(r);
```
但是，execute的定义较不具体。第一种语句创建一个线程并立即启动。根据Executor的实现，execute可能执行相同的操作，但更有可能使用现有的一个工作线程来运行r，或者将r置于队列中等待一个工作线程变为可用。（将在线程池部分中描述工作线程。）

java.util.concurrent中的执行器实现旨在充分利用更高级的ExecutorService和ScheduledExecutorService接口，尽管它们也可以与基本Executor接口一起使用。

#### ExecutorService接口
ExecutorService接口使用类似但更通用的submit方法来补充execute方法。像execute一样，submit接受Runnable对象，但也接受Callable对象，这允许任务返回一个值。 submit方法返回一个Future对象，用于检索Callable返回值并管理Callable和Runnable任务的状态。

ExecutorService还提供了用于提交大型Callable对象集合的方法。最后，ExecutorService提供了许多管理执行器关闭的方法。为了支持立即关闭，任务应正确处理interrupt。

#### ScheduledExecutorService 接口
ScheduledExecutorService接口使用schedule补充其父接口ExecutorService中的方法，schedule方法在指定的延迟之后执行Runnable或Callable任务。另外，这个接口定义了scheduleAtFixedRate和scheduleWithFixedDelay，它们以定义的的间隔重复执行指定的任务。

### 线程池
java.util.concurrent中的大多数执行器实现都使用由**工作线程**（worker thread）组成的**线程池**（thread pool）。这种线程与其执行的Runnable和Callable任务分离开，并且经常用于执行多个任务。

使用工作线程可以最大限度地减少线程创建时的开销。Thread对象使用大量的内存，并且在大规模应用程序中，分配和释放许多线程对象会产生显着的内存管理开销。

一种常见的**线程池**类型是**固定线程池**。这种类型的线程池总是有指定数量的线程在运行;如果一个线程在它还在使用的时候不知何故被终止，则会自动被一个新的线程替换。任务通过它的内部队列被提交到线程池，每当存在比线程更多的活动任务时，多出的任务会被保存到该内部队列。

固定线程池的一个重要优点是使用它的应用程序正常地降级。要理解这一点，请考虑一个Web服务器应用程序，其中每个HTTP请求由单独的线程处理。如果应用程序只是为每个新的HTTP请求创建一个新的线程，并且系统收到比它可以立即处理的更多的请求，当所有这些线程的开销超过系统的容量时，应用程序将突然停止响应所有请求。通过对可以创建的线程数量的限制，应用程序将不会尽快为HTTP请求提供服务，但它将在系统可以承受的时候尽快提供服务。

一个创建使用固定线程池的执行器的简单方法是在java.util.concurrent.Executors中调用**newFixedThreadPool**工厂方法，此类还提供以下工厂方法：
- **newCachedThreadPool**方法创建一个具有可扩展线程池的执行器。这个执行器适用于启动许多短暂任务的应用程序。
- **newSingleThreadExecutor**方法创建一次执行一个任务的执行器。
- 几个是上述执行器的ScheduledExecutorService版本的工厂方法。

如果上述工厂方法提供的任何执行程序都不满足需求，则构造**java.util.concurrent.ThreadPoolExecutor**或**java.util.concurrent.ScheduledThreadPoolExecutor**的实例将提供其他选项。

### Fork/Join
fork/join框架是ExecutorService接口的一个实现，可以帮助利用多个处理器。它被设计用于可递归地分解成更小的部分的工作。目标是使用所有可用的处理能力来提高应用程序的性能。

与任何ExecutorService实现一样，fork/join框架将任务分配到线程池中的工作线程。fork/join框架是独特的，因为它使用work-stealing算法。无事可做的工作线程可能会从仍然忙碌的其他线程中窃取任务。

fork/join框架的中心是**ForkJoinPool**类，它是**AbstractExecutorService**类的扩展。 **ForkJoinPool**实现了核心的**work-stealing**算法，并且可以执行**ForkJoinTask**进程。

#### 基本使用
使用fork/join框架的第一步是编写执行工作段的代码，您的代码应类似于以下伪代码：
```
if (my portion of the work is small enough)
  do the work directly
else
  split my work into two pieces
  invoke the two pieces and wait for the results
 ```
将此代码包装在**ForkJoinTask**子类中，通常使用其更专门的类型之，即**RecursiveTask**（可返回结果）或**RecursiveAction**。

在**ForkJoinTask**子类准备好后，创建表示所有要完成的工作的对象，并将其传递给**ForkJoinPool**实例的**invoke（）**方法。

#### 使用示例
为了帮助了解fork/join框架的工作原理，请考虑以下示例。假设你想模糊图像。原始的源图像由整数数组表示，其中每个整数包含单个像素的颜色值。模糊的目标图像也用与源的大小相同的整数数组表示。

执行模糊是通过一次处理源数组一个像素来完成的。每个像素与其周围的像素（红色，绿色和蓝色被平均）进行平均，并将结果放到目标数组中。由于图像是一个大的数组，这个过程可能需要很长时间。您可以通过使用fork/join框架实现算法来利用多处理器系统上的并发处理。这是一个可能的实现：
```java
public class ForkBlur extends RecursiveAction {
    private int[] mSource;
    private int mStart;
    private int mLength;
    private int[] mDestination;
  
    // 处理窗口大小，应该是一个奇数.
    private int mBlurWidth = 15;
  
    public ForkBlur(int[] src, int start, int length, int[] dst) {
        mSource = src;
        mStart = start;
        mLength = length;
        mDestination = dst;
    }

    protected void computeDirectly() {
        int sidePixels = (mBlurWidth - 1) / 2;
        for (int index = mStart; index < mStart + mLength; index++) {
            // 计算平均值。
            float rt = 0, gt = 0, bt = 0;
            for (int mi = -sidePixels; mi <= sidePixels; mi++) {
                int mindex = Math.min(Math.max(mi + index, 0),mSource.length - 1);
                int pixel = mSource[mindex];
                rt += (float)((pixel & 0x00ff0000) >> 16) / mBlurWidth;
                gt += (float)((pixel & 0x0000ff00) >>  8) / mBlurWidth;
                bt += (float)((pixel & 0x000000ff) >>  0)  / mBlurWidth;
            }
          
            // 重新组装目标像素。
            int dpixel = (0xff000000     ) | (((int)rt) << 16) |  (((int)gt) <<  8) | (((int)bt) <<  0);
            mDestination[index] = dpixel;
        }
    }	
  ...
```
现在实现抽象的compute()方法，它要么直接执行模糊，要么将其分为两个更小的任务。一个简单的数组长度阈值有助于确定工作是要执行还是要拆分。
```java
protected static int sThreshold = 100000;

protected void compute() {
    if (mLength < sThreshold) {
        computeDirectly();
        return;
    }
    
    int split = mLength / 2;
    
    invokeAll(new ForkBlur(mSource, mStart, split, mDestination),
              new ForkBlur(mSource, mStart + split, mLength - split,
                           mDestination));
}
```
如果上一个方法在RecursiveAction类的子类中，则设置在ForkJoinPool中配置要运行的任务是直接的，并且涉及以下步骤：
1. 创建一个代表所有要完成的工作的任务。
```java
// 源图像像素在src中
// 目标图像像素dst中
ForkBlur fb = new ForkBlur(src, 0, src.length, dst);
```
2. 创建要运行任务的ForkJoinPoll
```java
ForkJoinPool pool = new ForkJoinPool();
```
3. 运行任务
```java
pool.invoke(fb);
```

对于完整的源代码，包括创建目标映像文件的一些额外的代码，请参阅ForkBlur示例。

#### 标准实现
除了使用fork/join框架来实现在多处理器系统上同时执行的任务的自定义算法（例如上一节中的ForkBlur.java示例），Java SE中有一些常用的功能，它们已经使用fork/join框架。一个这样的实现在Java SE 8中引入，它被**java.util.Arrays**类所使用，用于它的**parallelSort()**方法。这些方法类似于**sort（）**，但是通过fork/join框架来利用并发性。在多处理器系统上运行时，大型数组的并行排序比顺序排序更快。然而，这些方法如何利用fork/join框架是不在本节范围之内的。有关此信息，请参阅Java API文档。 

**fork/join**框架的另一个实现由**java.util.streams**包中的方法所使用，它是为Java SE 8发行版计划的Project Lambda的一部分。有关更多信息，请参阅[“Lambda表达式”]。

## 并发集合
***
java.util.concurrent包包含许多Java Collections Framework的添加。这些最容易被提供的集合接口分类：
- [**BlockingQueue**](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/BlockingQueue.html)定义一个先进先出的数据结构，当你尝试向已满queue添加元素或从空queue中检索时，该数据结构将阻塞或超时。 
- [**ConcurrentMap**](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentMap.html)是java.util.Map的一个子接口，它定义了一些有用的原子性操作。这些操作仅在键存在时才删除或替换键值对，或者仅在键不存在时才添加键值对。使这些操作原子化有助于避免同步问题。ConcurrentMap的标准通用实现是[ConcurrentHashMap](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentHashMap.html)，它是[HashMap](https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html)的并发模拟。
- [**ConcurrentNavigableMap**](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentNavigableMap.html)是ConcurrentMap的一个子接口，它支持近似匹配。ConcurrentNavigableMap的标准通用实现是[ConcurrentSkipListMap](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentSkipListMap.html)，它是[TreeMap](https://docs.oracle.com/javase/8/docs/api/java/util/TreeMap.html)的并发模拟。

所有这些集合通过在向一个集合添加元素操作与后续的访问或移除对象的操作之间建立happens-before关系，来避免了内存一致性错误。

## 原子性变量
***
java.util.concurrent.atomic包定义了对单个变量支持原子操作的类。所有类都有get和set方法，其工作就像在volatile变量上的读写一样。也就是，在同一个变量上set与任何后续的get有一个happens-before关系。原子性compareAndSet方法也具有这些内存一致性特征，简单的原子性算术方法也适用于整数原子性变量。

要看如何使用这个包，先看线程不安全的Counter类：
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
使Counter避免线程干扰的一种方法是使其方法同步，如SynchronizedCounter：
```java
class SynchronizedCounter {
    private int c = 0;

    public synchronized void increment() {
        c++;
    }

    public synchronized void decrement() {
        c--;
    }

    public synchronized int value() {
        return c;
    }

}
```
对于这个简单的类，同步是可以接受的解决方案。但是对于一个更复杂的类，我们可能希望避免不必要的同步的活性影响。用AtomicInteger替换int字段可以防止线程干扰，而不要求同步，如AtomicCounter：
```java
import java.util.concurrent.atomic.AtomicInteger;

class AtomicCounter {
    private AtomicInteger c = new AtomicInteger(0);

    public void increment() {
        c.incrementAndGet();
    }

    public void decrement() {
        c.decrementAndGet();
    }

    public int value() {
        return c.get();
    }

}
```


## ThreadLocalRandom
***
在JDK 7中，java.util.concurrent包含一个便捷类，**ThreadLocalRandom**，用于希望在多线程或ForkJoinTask使用随机数的应用程序。 

对于并发访问，使用**ThreadLocalRandom**而不是**Math.random()**导致更少的争用，并最终产生更好的性能。 

所有需要做的是调用**ThreadLocalRandom.current()**，然后调用其一个方法来检索一个随机数。这里有一个例子： 
```java
int r = ThreadLocalRandom.current() .nextInt(4, 77);
```
***
# 活性
***
一个并发应用程序及时执行的能力被称活性（liveness）。下面讨论常见的活性问题。

## 死锁
***
死锁是两个或多个线程永远被阻塞，互相等待彼此释放锁的的情况。死锁会有至少两个内部锁，并且线程在一个同步块中尝试进入另一个同步块。例如线程1持有A锁，尝试获取B锁，而线程2持有B锁，尝试获取A锁，这时死锁发生了。情况如下所示：
```
Thread 1  locks A, waits for B
Thread 2  locks B, waits for A
```
举例，Alphonse和Gaston是朋友，也是礼貌的信徒。它们你有一个严格的礼貌规则是，当你向朋友鞠躬时，你必须保持鞠躬，直到你的朋友有机会回躬。不幸的是，这个规则不能排除两个朋友可能会同时相互鞠躬的可能。这个例子应用程序, Deadlock，模拟这种可能性：
```java
public class Deadlock {
    static class Friend {
        private final String name;
        public Friend(String name) {
            this.name = name;
        }
        public String getName() {
            return this.name;
        }
        public synchronized void bow(Friend bower) {
            System.out.format("%s: %s" + "  has bowed to me!%n", this.name, bower.getName());
            bower.bowBack(this);
        }
        public synchronized void bowBack(Friend bower) {
            System.out.format("%s: %s" + " has bowed back to me!%n",this.name, bower.getName());
        }
    }

    public static void main(String[] args) {
        final Friend alphonse = new Friend("Alphonse");
        final Friend gaston = new Friend("Gaston");
		
        new Thread(new Runnable() {
            public void run() { alphonse.bow(gaston); }
        }).start();
        new Thread(new Runnable() {
            public void run() { gaston.bow(alphonse); }
        }).start();
    }
}
```

死锁也可以包含两个以上的线程。这使得更难检测。以下是四个线程死锁的示例：
```
Thread 1  locks A, waits for B
Thread 2  locks B, waits for C
Thread 3  locks C, waits for D
Thread 4  locks D, waits for A
```
一个死锁可能会发生的更复杂的情况是数据库事务。数据库事务可能包含许多SQL更新请求。当在一个事务中更新记录时，对于他事务更新该记录将被锁定，直到第一个事务完成。因此，相同事务中的每个更新请求可能会锁定数据库中的一些记录。

如果多个事务正在同时运行，需要更新相同的记录，那么就有可能会导致死锁。例如：
```
Transaction 1, request 1, locks record 1 for update
Transaction 2, request 1, locks record 2 for update
Transaction 1, request 2, tries to lock record 2 for update.
Transaction 2, request 2, tries to lock record 1 for update.
```
因为锁在采用自不同的请求，因此并不是提前知道给定事务所需的所有锁，很难检测或防止数据库事务中的死锁。

## 饥饿与公平锁
***
饥饿描述了线程无法获得对共享资源的正常访问并且无法继续执行的情况。以下三个常见原因可能导致Java中线程的饥饿：
- 具有高优先级的线程相比较低优先级的线程，占据了所有的CPU时间。
- 线程被无限期阻塞等待进入同步块，因为其他线程一直在它之前先进入。
- 线程无限期在等待被唤醒，因为其他线程一直在它之前被唤醒。

饥饿问题的解决方案被称为公平锁，它使所有线程公平的获得执行权限。
### 使用Lock代替同步块
同步代码依赖于一种简单的可重入锁。这种锁很容易使用，但有很多限制。java.util.concurrent.locks包支持更复杂的锁。其中最基本的是Lock接口。

Lock对象的工作非常像同步代码使用的隐式锁。