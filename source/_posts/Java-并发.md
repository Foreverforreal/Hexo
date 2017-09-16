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
每个Java虚拟机上运行的线程都有它自己的线程栈。<font style="color:#ec70ae;">线程栈包含了线程达到当前执行点调用方法的信心。此外还包含每个正执行方法(调用栈中的所有方法)的局部变量</font>。一个线程只可以访问自己的线程栈，由线程栈创建的局部变量对其他线程都不可见，即使两个线程执行相同的代码。

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

由于线程B受限读取volatile变量sharedObject.counter，那么sharedObject.counter和sharedObject.nonVolatile都会从主内存读取到线程B使用的CPU缓存中。当线程B读取sharedObject.nonVolatile时，它将看到线程A写入最新值。

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


***
# 活性
***
一个并发应用程序及时执行的能力被称活性（liveness）。下面讨论常见的活性问题。

## 死锁
***