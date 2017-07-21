[TOC]

# Java 内存区域

对于 Java 程序员来说,虚拟机自带内存管理机制,不容易出现内存泄露和内存溢出问题,一旦出现内存泄露方面的问题,如果我们了解虚拟机是怎样使用内存的,将很容易的找到错误发生的原因.

### 1.1 运行时数据区域

Java 虚拟机在执行 Java 程序时,会把它所管理的内存分成若干的区域,每一个区域都有各自的用途.

### 1.1.1 程序计数器

程序计数器是一块很小的内存空间,它可以看做是当前程序所执行字节码的行号指示器.每一个线程都有一个程序计数器.

### 1.1.2 Java 虚拟机栈（私有）

线程私有的（每一个线程都拥有自己的栈空间，不共享）,生命周期与线程相同.虚拟机栈描述的是 Java 方法执行的内存模型,每个方法在执行的同时都会创建一个栈帧用于存储局部变量表,操作数栈,动态链接,方法出口等信息.每一个方法从调用到完成,就对应着栈帧从入栈到出栈的过程.

### 1.1.3 本地方法栈

与虚拟机栈发挥的作用类似,区别是虚拟机栈为虚拟机执行 Java 方法服务,而本地方法栈为虚拟机用到的 Native 方法服务.

### 1.1.4 Java 堆（共享）

是 Java 虚拟机所管理内存最大的一块,被**所有线程共享**的一块内存区域.所有**对象**实例以及**数组**都要在堆上分配.
Java 堆是垃圾收集器管理的主要区域,也被成为”GC 堆”,从内存回收角度看,现在收集器基本都采用分代收集算法,所以 Java 堆还可以分为:新生代（局部变量等生命周期短的都在新生代）和老年代.

> http://jbutton.iteye.com/blog/1569746
>
> 判断是否可回收算法：引用计数/对象引用链追踪用来确定对象是否可回收
>
> 回收算法：标记-清除、标记-覆盖、标记-整理

### 1.1.5 方法区(共享)

**线程共享**的内存区域,用于存储虚拟机加载的**类信息,常量,静态变量**,即时编译器编译后的代码等数据.

### 1.1.6 运行时常量池（方法区中的一部分）

是**方法区的一部分**.Class 文件除了有类的版本,字段,方法,接口等描述信息外,还有一项信息是常量池,用于存放编译器生成的各种**字面量和符号引用**,这部分内容在类加载后进入方法区的运行时常量池中存放.

### 1.1.7 直接内存

直接内存不是虚拟机运行时数据区的一部分,也不是 Java 虚拟机规范中定义的内存区域.
在 JDK1.4中新加入了 NIO(New Input/Output)类,引入了一种基于通道与缓冲区的 I/O 方式,可以使用 Native 函数库直接分配堆内存,然后通过 Java 堆中的 DirectByteBuffer 对象作为这块内存的引用操作,这样做能显著提高性能,因为避免了在 Java 堆和 Native 堆中来回复制数据.

### 1.2 HotSpot 虚拟机对象探秘

基于使用优先原则,我们以常用的虚拟机 HotSpot 和常用的内存区域 Java 堆为例,探讨 HotSpot 虚拟机在 Java 堆中对象分配,布局和访问的全过程.

### 1.2.1 对象的创建

虚拟机遇到一个 new 指令时,首先去检查这个指令的参数是否能在常量池中定位到一个类的符号引用,并且检查这个类是否已被加载,解析和初始化过,如果没有,必须先执行相应的类加载过程.
在类加载通过后,虚拟机开将为新对象分配内存,如果 Java 堆中内存是绝对规整的,分配内存仅仅是把指针向空闲空间那边挪动一段与对象大小相等的距离,这种分配成为”指针碰撞”.如果 Java 堆中内存不是规整的,就必须维护一个列表,记录那些内存可用,在分配时从列表找到一空间划分给对象实例,并更新列表上的记录,这种分配方式称为”空闲列表”.
接下来,虚拟机要对对象进行必要的设置,如这个对象是哪个类的实例,如果才能找到类的元数据信息,对象的 GC 分代年龄等信息,将这些信息存放在对象头之中.

### 1.2.2 对象的内存布局

在 HotSpot 虚拟机中,对象在内存中存储的区域分为3块:**对象头**,**实例数据**和**对齐填充**.
对象头包括两部分信息,第一部分是存储对象自身运行时数据.如哈希表, GC 分代年龄,锁状态等,另一部分是对象指向它的类元数据的指针,通过这个指针来确定这个对象是哪个类的实例.
实例数据部分存储程序代码中所定义的各种类型的字段内容.
对齐填充不是必然存在的,当对象的大小不是8字节整数倍时,就需要通过对齐填充来补全.

### 1.2.3 对象的访问定位

Java 程序中通过栈上的 reference 数据来操作堆上的具体对象,reference 只规定了一个指向对象的引用,没有定义引用应该通过何种方式定位,访问堆中对象的具体位置,所以对象访问取决于虚拟机实现而定.主流的访问方式有使用句柄和直接指针两种.
如果使用句柄访问,在 Java 堆中将会划分一块内存作为句柄池, reference 中存储的就是对象的句柄地址,而句柄地址中包含了对象实例数据与类型数据各自的具体地址
如果使用直接指针访问,在 Java 堆的布局就必须考虑如何放置访问类型数据的相关信息,而 reference 中存储的直接就是对象地址.
使用句柄优点是对象移动后只需改变句柄中的事例数据指针.使用指针的优点是速度更快,节省了一次指针定位的时间开销.

# 并发

最近重新复习了一边并发的知识，发现自己之前对于并发的了解只是皮毛。这里总结以下Java并发需要掌握的点。

使用并发的一个重要原因是提高执行效率。由于I/O等情况阻塞，单个任务并不能充分利用CPU时间。所以在单处理器的机器上也应该使用并发。
为了实现并发，操作系统层面提供了多进程。但是进程的数量和开销都有限制，并且多个进程之间的数据共享比较麻烦。另一种比较轻量的并发实现是使用线程，一个进程可以包含多个线程。线程在进程中没有数量限制, 数据共享相对简单。线程的支持跟语言是有关系的。Java 语言中支持多线程。

Java 中的多线程是抢占式的。这意味着一个任务随时可能中断并切换到其它任务。所以我们需要在代码中足够的谨慎，防范好这种切换带来的副作用。

## 基础

`Runnable` 它可以理解成一个任务。它的`run()`方法就是任务的逻辑，执行顺序。

`Thread` 它是一个任务的载体，虚拟机通过它来分配任务执行的时间片。
Thread中的start方法可以作为一个并发任务的入口。不通过start方法来执行任务，那么run方法就只是一个普通的方法

线程的状态有四种：

1. **NEW** 线程创建的时候短暂的处于这种状态。这种状态下已经可以获得CPU时间了，随后可能进入RUNNABLE，BLOCKED状态。
2. **RUNNABLE** 此状态下只要CPU将时间分配给线程，线程中的任务就可以执行。随后可能进入BLOCKED，DEAD状态。
3. **BLOCKED** 线程可以运行，但是有某个条件阻止着它。当线程处于阻塞状态时，CPU不会分配时间片给它，直到它重新进入RUNNABLE状态。
4. **DEAD** 此状态的线程将永远不会获得CPU时间片。通常是因为run()方法返回才会到达此状态。此时任务还是可以被中断的。

`Callable<T>` 它是一个带返回的异步任务，返回的结果放到一个Future对象中。

`Future<T>` 它可以接受Callable任务的返回结果。在任务没有返回的时候调用get方法会阻塞当前线程。cancel方法会尝试取消未完成的任务（未执行->直接不执行，已经完成->返回false,正在执行->尝试中断）。

`FutureTask<T>` 同时继承了Runnable, Callable 接口。

Java 1.5之后，不再推荐直接使用Thread对象作为任务的入口。推荐使用Executor管理Thread对象。Executor是线程与任务之间的的一个中间层，它屏蔽了线程的生命周期，不再需要显式的管理线程。并且ThreadPoolExecutor 实现了此接口，我们可以通过它来利用线程池的优点。

线程池涉及到的类有：`Executor`, `ExecutorService`, `ThreadExecutorPool`, `Executors`, `FixedThreadPool`, `CachedThreadPool`, `SingleThreadPool`。

`Executor` 只有一个方法，execute来提交一个任务

`ExecutorService` 提供了管理异步任务的方法，也可以产生一个`Future`对象来跟踪一个异步任务。

主要的方法如下:

- `submit` 可以提交一个任务
- `shutdown` 可以拒绝接受新任务
- `shutdownNow` 可以拒绝新任务并向正在执行的任务发出中断信号
- `invokeXXX` 批量执行任务

`ThreadPoolExecutor` 线程池的具体实现类。线程池的好处在于提高效率，能避免频繁申请/回收线程带来的开销。

它的使用方法复杂一些，构造线程池的可选参数有:

1. **corePoolSize** : int 工作的Worker的数量。
2. **maximumPoolSize** : int 线程池中持有的Worker的最大数量
3. **keepAliveTime** : long 当超过Workder的数量corePoolSize的时候，如果没有新的任务提交，超过corePoolSize的Worker的最长等待时间。超过这个时间之后，一部分Worker将被回收。
4. **unit** : TimeUnit keepAliveTime的单位
5. **workQueue** : BlockingQueue 缓存任务的队列, 这个队列只缓存提交的Runnable任务。
6. **threadFactory** : ThreadFactory 产生线程的“工厂”
7. **handler** : RejectedExecutionHandler 当一个任务被提交的时候，如果所有Worker都在工作并且超过了缓存队列的容量的时候。会交给这个Handler处理。Java 中提供了几种默认的实现，AbortPolicy, CallerRunsPolicy, DiscardOldestPolicy, DiscardPolicy。

这里的Worker可以理解为一个线程。

这里之前想不通，觉得线程不可能重新利用绑定新任务。看了下源码发现原来确实不是重新绑定任务。每一个Worker的核心部分只是一个循环，不断从缓存队列中取任务执行。这样达到了重用的效果。

```
final void runWorker(Worker w) {
    Runnable task = w.firstTask;
    // ...
    try {
        while(task != null || (task=getTask())!=null) {
            try{
                task.run();
            } catch(Exception e){
            }
            // ...
        }
    } finally {
        // ...
    }
    // ...
}
```

`Executors`类提供了几种默认线程池的实现方式。

1. `CachedThreadExecutor` 工作线程的数量没有上限(Integer的最大值), 有需要就创建新线程。
2. `FixedThreadExecutor` 预先一次分配固定数量的线程，之后不再需要创建新线程。
3. `SingleThreadExecutor` 只有一个线程的线程池。如果提交了多个任务，那么这些人物将排队，每个任务都在上一个人物执行完之后执行。所有任务都是按照它们的提交顺序执行的。

`sleep(long)` 当前线程 **中止** 一段时间。它不会释放锁。Java1.5之后提供了更加灵活的版本。

`TimeUnit` 可以指定睡眠的时间单位。

**优先级** 绝大多数情况下我们都应该使用默认的优先级。不同的虚拟机中对应的优先级级别的总数，一般用三个就可以了 `MAX_PRIORITY`, `NORM_PRIORITY`, `MIN_PRIORITY`。

**让步** `Thread.yield()`建议相同优先级的其它线程先运行，但是不保证一定运行其它线程。

**后台线程** 一个进程中的所有非后台线程都终止的时候整个进程也就终止，同时杀死所有后台线程。与优先级没有什么关系。

**join()** 线程 A 持有线程T，当在线程T调用T.join()之后，A会阻塞，直到T的任务结束。可以加一个超时参数，这样在超时之后线程A可以放弃等待继续执行任务。

**捕获异常** 不能跨线程捕获异常。比如说不能在main线程中添加try-catch块来捕获其它线程中抛出的异常。每一个Thread对象都可以设置一个`UncaughtExceptionHandler`对象来处理本线程中抛出的异常。线程池中可以通过参数`ThreadFactory`来为每一个线程设置一个`UncaughtExceptionHandler`对象。

## 访问共享资源

在处理并发的时候，将变量设置为private非常的重要，这可以防止其它线程直接访问变量。

`synchronized` 修饰方法在不加参数情况下，使用对象本身作为锁。静态方法使用Class对象作为锁。同一个任务可以多次获得对象锁。

**显式锁** Lock，相比synchronized更加灵活。但是需要的代码更多，编写出错的可能性也更高。只有在解决特殊问题或者提高效率的时候才用它。

**原子性** 原子操作就是永远不会被线程切换中断的操作。很多看似原子的操作都是非原子的，比如说long,double是由两个byte表示的，它们的所有操作都是非原子的。所以，涉及到并发异常的地方都加上同步吧。除非你对虚拟机十分的了解。

**volatile** 这个关键字的作用在于防止多线程环境下读取变量的脏数据。这个关键字在c语言中也有，作用是相同的。

**原子类** AtomicXXX类，它们能够保证对数据的操作是满足原子性的。这些类可以用来优化多线程的执行效率，减少锁的使用。然而，使用难度还是比较高的。

**临界区** synchronized关键字的用法。不是修饰整个方法，而是修饰一个代码块。它的作用在于尽量利用并发的效率，减少同步控制的区域。

**ThreadLocal** 这个概念与同步的概念不同。它是给每一个线程都创建一个变量的副本，并保持副本之间相互独立，互不干扰。所以各个线程操作自己的副本，不会产生冲突。

## 终结任务

这里我讲一下自己当前的理解。

一个线程不是可以随便中断的。即使我们给线程设置了中断状态，它也还是可以获得CPU时间片的。只有因为sleep()方法而阻塞的线程可以立即收到`InterruptedException`异常，所以在sleep中断任务的情况下可以直接使用`try-catch`跳出任务。其它情况下，均需要通过判断线程状态来判断是否需要跳出任务(Thread.interrupted()方法)。

synchronized方法修饰的代码不会在收到中断信号后立即中断。ReentrantLock锁控制的同步代码可以通过InterruptException中断。

Thread.interrupted方法调用一次之后会立即清空中断状态。可以自己用变量保存状态。

## 线程协作

**wait/notifyAll** wait/notifyAll是Object类中的方法。调用wait/notifyAll方法的对象是互斥对象。因为Java中所有的Object都可以做互斥量(synchronized关键字的参数),所以wait/notify方法是在Object类中的。

wait与sleep 不同在于sleep方法是Thread类中的方法，调用它的时候不会释放锁；wait方法是Object类中的方法，调用它的时候会释放锁。

调用wait方法之前，当前线程必须持有这段逻辑的锁。否则会抛出异常，不能继续执行。

wait方法可以将当前线程放入等待集合中，并释放当前线程持有的锁。此后，该线程不会接收到CPU的调度，并进入休眠状态。有四种情况肯能打破这种状态：

1. 有其它线程在此互斥对象上调用了notify方法，并且刚好选中了这个线程被唤醒；
2. 有其它线程在此互斥对象上调用了notifyAll方法；
3. 其它线程向此线程发出了中断信号；
4. 等待时间超过了参数设置的时间。

线程一旦被唤醒之后，它会像正常线程一样等待之前持有的所有锁。直到恢复到wait方法调用之前的状态。

还有一种不常见的情况，spurious wakeup(虚假唤醒)。就是在没有notify，notifyAll，interrupt的时候线程自动醒来。查了一些资料并没有弄清楚是为什么。不过为了防止这种现象，我们要在wait的条件上加一层循环。

当一个线程调用wait方法之后，其它线程调用该线程的interrupt方法。该线程会唤醒，并尝试恢复之前的状态。当状态恢复之后，该线程会抛出一个异常。

notify 唤醒一个等待此对象的线程。
notifyAll 唤醒所有等待此对象的线程。

### 错失的信号

当两个线程使用notify/wait或者notifyAll/wait进行协作的时候，不恰当的使用它们可能会导致一些信号丢失。例子：

```
T1:
synchronized(shareMonitor){
    // set up condition for T2
    shareMonitor.notify();
}

T2:
while(someCondition){
    // Point 1
    synchronized(shareMonitor){
        shareMonitor.wait();
    }
}
```

信号丢失是这样发生的：

当T2执行到Point1的时候，线程调度器将工作线程从T2切换到T1。T1完成T2条件的设置工作之后，线程调度器将工作线程从T1切换回T2。虽然T2线程等待的条件已经满足，但还是会被挂起。

解决的方法比较简单：

```
T2:
synchronized(sharedMonitor) {
    while(someCondition) {
        sharedMonitor.wait();
    }
}
```

将竞争条件放到while循环的外面即可。在进入while循环之后，在没有调用wait方法释放锁之前，将不会进入到T1线程造成信号丢失。

notify & notifyAll 前面已经提过这两个方法的区别。notify是随机唤醒一个等待此锁的线程，notifyAll是唤醒所有等待此锁的线程。

Condition 他是concurrent类库中显式的挂起/唤醒任务的工具。它是真正的锁(Lock)对象产生的一个对象。其实用法跟wait/notify是一致的。await挂起任务，signalAll()唤醒任务。

生产者消费者队列 Java中提供了一种非常简便的容器，BlockingQueue。已经帮你写好了阻塞式的队列。

除了BlockingQueue，使用PipedWriter/PipedReader也可以方便的在线程之间传递数据。

## 死锁

死锁有四个必要条件，打破一个即可去除死锁。

四个必要条件：

1. 互斥条件。 互斥条件：一个资源每次只能被一个进程使用。
2. 请求与保持条件：一个线程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件:线程已获得的资源，在末使用完之前，不能强行剥夺。
4. 循环等待条件:若干线程之间形成一种头尾相接的循环等待资源关系。

本来自己翻译，但发现百度上描述的更好一些，直接copy到这里来，并把进程换成了线程。

## 其它工具

`CountDownLatch` 同步多个任务，强制等待其它任务完成。它有两个重要方法countDown,await以及构造时传入的参数SIZE。当一个线程调用await方法的时候会挂起，直到该对象收到SIZE次countDown。一个对象只能使用一次。

`CyclicBarrier` 也是有一个SIZE参数。当有SIZE个线程调用await的时候，全部线程都会被唤醒。可以理解为所有运动员就位后才能起跑，早就位的运动员只能挂起等待。它可以重复利用。

`DelayQueue` 一个无界的BlockingQueue，用来放置实现了Delay接口的对象，在队列中的对象只有在到期之后才能被取走。如果没有任何对象到期，就没有头元素。

`PriorityBlockingQueue` 一种自带优先级的阻塞式队列。

`ScheduledExecutor` 可以把它想象成一种线程池式的Timer, TimerTask。

`Semaphore` 互斥锁只允许一个线程访问资源，但是Semaphore允许SIZE个线程同时访问资源。

`Exchanger` 生产者消费者问题的特殊版。两个线程可以在都‘准备好了’之后交换一个对象的控制权。

`ReadWriteLock` 读写锁。 读-读不互斥，读-写互斥，写-写互斥

# 引

如果对什么是线程、什么是进程仍存有疑惑，请先Google之，因为这两个概念不在本文的范围之内。

用多线程只有一个目的，那就是更好的利用cpu的资源，因为所有的多线程代码都可以用单线程来实现。说这个话其实只有一半对，因为反应“多角色”的程序代码，最起码每个角色要给他一个线程吧，否则连实际场景都无法模拟，当然也没法说能用单线程来实现：比如最常见的“生产者，消费者模型”。

很多人都对其中的一些概念不够明确，如同步、并发等等，让我们先建立一个数据字典，以免产生误会。

- 多线程：指的是这个程序（一个进程）运行时产生了不止一个线程
- 并行与并发：
  - 并行：多个cpu实例或者多台机器同时执行一段处理逻辑，是真正的同时。
  - 并发：通过cpu调度算法，让用户看上去同时执行，实际上从cpu操作层面不是真正的同时。并发往往在场景中有公用的资源，那么针对这个公用的资源往往产生瓶颈，我们会用TPS或者QPS来反应这个系统的处理能力。

并发与并行

- 线程安全：经常用来描绘一段代码。指在并发的情况之下，该代码经过多线程使用，线程的调度顺序不影响任何结果。这个时候使用多线程，我们只需要关注系统的内存，cpu是不是够用即可。反过来，线程不安全就意味着线程的调度顺序会影响最终结果，如不加事务的转账代码：

  ```
  void transferMoney(User from, User to, float amount){
    to.setMoney(to.getBalance() + amount);
    from.setMoney(from.getBalance() - amount);
  }
  ```

- 同步：Java中的同步指的是通过人为的控制和调度，保证共享资源的多线程访问成为线程安全，来保证结果的准确。如上面的代码简单加入`@synchronized`关键字。在保证结果准确的同时，提高性能，才是优秀的程序。线程安全的优先级高于性能。

好了，让我们开始吧。我准备分成几部分来总结涉及到多线程的内容：

1. 扎好马步：线程的状态
2. 内功心法：每个对象都有的方法（机制）
3. 太祖长拳：基本线程类
4. 九阴真经：高级多线程控制类

## 扎好马步：线程的状态

先来两张图：

线程状态

线程状态转换

各种状态一目了然，值得一提的是"Blocked"和"Waiting"这两个状态的区别：

- 线程在Running的过程中可能会遇到阻塞(Blocked)情况
  对Running状态的线程加同步锁(Synchronized)使其进入(lock blocked pool ),同步锁被释放进入可运行状态(Runnable)。从jdk源码注释来看，blocked指的是对monitor的等待（可以参考下文的图）即该线程位于等待区。
- 线程在Running的过程中可能会遇到等待（Waiting）情况
  线程可以主动调用object.wait或者sleep，或者join（join内部调用的是sleep，所以可看成sleep的一种）进入。从jdk源码注释来看，waiting是等待另一个线程完成某一个操作，如join等待另一个完成执行，object.wait()等待object.notify()方法执行。

> **Waiting状态和Blocked状态**有点费解，我个人的理解是：Blocked其实也是一种wait，等待的是monitor，但是和Waiting状态不一样，举个例子，有三个线程进入了同步块，其中两个调用了object.wait()，进入了waiting状态，这时第三个调用了object.notifyAll()，这时候前两个线程就一个转移到了Runnable,一个转移到了Blocked。
>
> 从下文的monitor结构图来区别：每个 Monitor在某个时刻，只能被一个线程拥有，该线程就是 “Active Thread”，而其它线程都是 “Waiting Thread”，分别在两个队列 “ Entry Set”和 “Wait Set”里面等候。在 “Entry Set”中等待的线程状态Blocked,从jstack的dump中来看是 “Waiting for monitor entry”，而在 “Wait Set”中等待的线程状态是Waiting，表现在jstack的dump中是 “in Object.wait()”。

此外，在runnable状态的线程是处于被调度的线程，此时的调度顺序是不一定的。Thread类中的yield方法可以让一个running状态的线程转入runnable。

## 内功心法：每个对象都有的方法（机制）

synchronized, wait, notify 是任何对象都具有的同步工具。让我们先来了解他们

monitor

他们是应用于同步问题的人工线程调度工具。讲其本质，首先就要明确monitor的概念，Java中的每个对象都有一个监视器，来监测并发代码的重入。在非多线程编码时该监视器不发挥作用，反之如果在synchronized 范围内，监视器发挥作用。

wait/notify必须存在于synchronized块中。并且，这三个关键字针对的是同一个监视器（某对象的监视器）。这意味着wait之后，其他线程可以进入同步块执行。

当某代码并不持有监视器的使用权时（如图中5的状态，即脱离同步块）去wait或notify，会抛出java.lang.IllegalMonitorStateException。也包括在synchronized块中去调用另一个对象的wait/notify，因为不同对象的监视器不同，同样会抛出此异常。

再讲用法：

- synchronized单独使用：

  - 代码块：如下，在多线程环境下，synchronized块中的方法获取了lock实例的monitor，如果实例相同，那么只有一个线程能执行该块内容

    ```
    public class Thread1 implements Runnable {
       Object lock;
       public void run() {  
           synchronized(lock){
             ..do something
           }
       }
    }
    ```

  - 直接用于方法： 相当于上面代码中用lock来锁定的效果，实际获取的是Thread1类的monitor。更进一步，如果修饰的是static方法，则锁定该类所有实例。

    ```
    public class Thread1 implements Runnable {
       public synchronized void run() {  
            ..do something
       }
    }
    ```

- synchronized, wait, notify结合:典型场景生产者消费者问题

  ```
  /**
     * 生产者生产出来的产品交给店员
     */
    public synchronized void produce()
    {
        if(this.product >= MAX_PRODUCT)
        {
            try
            {
                wait();  
                System.out.println("产品已满,请稍候再生产");
            }
            catch(InterruptedException e)
            {
                e.printStackTrace();
            }
            return;
        }

        this.product++;
        System.out.println("生产者生产第" + this.product + "个产品.");
        notifyAll();   //通知等待区的消费者可以取出产品了
    }

    /**
     * 消费者从店员取产品
     */
    public synchronized void consume()
    {
        if(this.product <= MIN_PRODUCT)
        {
            try 
            {
                wait(); 
                System.out.println("缺货,稍候再取");
            } 
            catch (InterruptedException e) 
            {
                e.printStackTrace();
            }
            return;
        }

        System.out.println("消费者取走了第" + this.product + "个产品.");
        this.product--;
        notifyAll();   //通知等待去的生产者可以生产产品了
    }
  ```

  ##### volatile

  多线程的内存模型：main memory（主存）、working memory（线程栈），在处理数据时，线程会把值从主存load到本地栈，完成操作后再save回去(volatile关键词的作用：每次针对该变量的操作都激发一次load and save)。

volatile

针对多线程使用的变量如果不是volatile或者final修饰的，很有可能产生不可预知的结果（另一个线程修改了这个值，但是之后在某线程看到的是修改之前的值）。其实道理上讲同一实例的同一属性本身只有一个副本。但是多线程是会缓存值的，本质上，volatile就是不去缓存，直接取值。在线程安全的情况下加volatile会牺牲性能。

## 太祖长拳：基本线程类

基本线程类指的是Thread类，Runnable接口，Callable接口
Thread 类实现了Runnable接口，启动一个线程的方法：

```
　　MyThread my = new MyThread();
　　my.start();
```

**Thread类相关方法：**

```
//当前线程可转让cpu控制权，让别的就绪状态线程运行（切换）
public static Thread.yield() 
//暂停一段时间
public static Thread.sleep()  
//在一个线程中调用other.join(),将等待other执行完后才继续本线程。　　　　
public join()
//后两个函数皆可以被打断
public interrupte()
```

**关于中断**：它并不像stop方法那样会中断一个正在运行的线程。线程会不时地检测中断标识位，以判断线程是否应该被中断（中断标识值是否为true）。终端只会影响到wait状态、sleep状态和join状态。被打断的线程会抛出InterruptedException。
Thread.interrupted()检查当前线程是否发生中断，返回boolean
synchronized在获锁的过程中是不能被中断的。

中断是一个状态！interrupt()方法只是将这个状态置为true而已。所以说正常运行的程序不去检测状态，就不会终止，而wait等阻塞方法会去检查并抛出异常。如果在正常运行的程序中添加while(!Thread.interrupted()) ，则同样可以在中断后离开代码体

**Thread类最佳实践**：
写的时候最好要设置线程名称 Thread.name，并设置线程组 ThreadGroup，目的是方便管理。在出现问题的时候，打印线程栈 (jstack -pid) 一眼就可以看出是哪个线程出的问题，这个线程是干什么的。

**如何获取线程中的异常**

不能用try,catch来获取线程中的异常

### Runnable

与Thread类似

### Callable

future模式：并发模式的一种，可以有两种形式，即无阻塞和阻塞，分别是isDone和get。其中Future对象用来存放该线程的返回值以及状态

```
ExecutorService e = Executors.newFixedThreadPool(3);
 //submit方法有多重参数版本，及支持callable也能够支持runnable接口类型.
Future future = e.submit(new myCallable());
future.isDone() //return true,false 无阻塞
future.get() // return 返回值，阻塞直到该线程运行结束
```

## 九阴真经：高级多线程控制类

以上都属于内功心法，接下来是实际项目中常用到的工具了，Java1.5提供了一个非常高效实用的多线程包:*java.util.concurrent*, 提供了大量高级工具,可以帮助开发者编写高效、易维护、结构清晰的Java多线程程序。

### 1.ThreadLocal类

用处：保存线程的独立变量。对一个线程类（继承自Thread)
当使用ThreadLocal维护变量时，ThreadLocal为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本。常用于用户登录控制，如记录session信息。

实现：每个Thread都持有一个TreadLocalMap类型的变量（该类是一个轻量级的Map，功能与map一样，区别是桶里放的是entry而不是entry的链表。功能还是一个map。）以本身为key，以目标为value。
主要方法是get()和set(T a)，set之后在map里维护一个threadLocal -> a，get时将a返回。ThreadLocal是一个特殊的容器。

### 2.原子类（AtomicInteger、AtomicBoolean……）

如果使用atomic wrapper class如atomicInteger，或者使用自己保证原子的操作，则等同于synchronized

```
//返回值为boolean
AtomicInteger.compareAndSet(int expect,int update)
```

该方法可用于实现乐观锁，考虑文中最初提到的如下场景：a给b付款10元，a扣了10元，b要加10元。此时c给b2元，但是b的加十元代码约为：

```
if(b.value.compareAndSet(old, value)){
   return ;
}else{
   //try again
   // if that fails, rollback and log
}
```

**AtomicReference**
对于AtomicReference 来讲，也许对象会出现，属性丢失的情况，即oldObject == current，但是oldObject.getPropertyA != current.getPropertyA。
这时候，AtomicStampedReference就派上用场了。这也是一个很常用的思路，即加上版本号

### 3.Lock类　

lock: 在java.util.concurrent包内。共有三个实现：

- ReentrantLock
- ReentrantReadWriteLock.ReadLock
- ReentrantReadWriteLock.WriteLock

主要目的是和synchronized一样， 两者都是为了解决同步问题，处理资源争端而产生的技术。功能类似但有一些区别。

区别如下：

1. lock更灵活，可以自由定义多把锁的枷锁解锁顺序（synchronized要按照先加的后解顺序）
2. 提供多种加锁方案，lock 阻塞式, trylock 无阻塞式, lockInterruptily 可打断式， 还有trylock的带超时时间版本。
3. 本质上和监视器锁（即synchronized是一样的）
4. 能力越大，责任越大，必须控制好加锁和解锁，否则会导致灾难。
5. 和Condition类的结合。
6. 性能更高，对比如下图：

synchronized和Lock性能对比

**ReentrantLock**　　　　
可重入的意义在于持有锁的线程可以继续持有，并且要释放对等的次数后才真正释放该锁。
使用方法是：

1.先new一个实例

```
static ReentrantLock r=new ReentrantLock();
```

2.加锁　　　　　　

```
r.lock()或r.lockInterruptibly();
```

此处也是个不同，后者可被打断。当a线程lock后，b线程阻塞，此时如果是lockInterruptibly，那么在调用b.interrupt()之后，b线程退出阻塞，并放弃对资源的争抢，进入catch块。（如果使用后者，必须throw interruptable exception 或catch）　　　　

3.释放锁　　　

```
r.unlock()
```

必须做！何为必须做呢，要放在finally里面。以防止异常跳出了正常流程，导致灾难。这里补充一个小知识点，finally是可以信任的：经过测试，哪怕是发生了OutofMemoryError，finally块中的语句执行也能够得到保证。

**ReentrantReadWriteLock**

可重入读写锁（读写锁的一个实现）

```
　　ReentrantReadWriteLock lock = new ReentrantReadWriteLock()
　　ReadLock r = lock.readLock();
　　WriteLock w = lock.writeLock();
```

两者都有lock,unlock方法。写写，写读互斥；读读不互斥。可以实现并发读的高效线程安全代码

### 4.容器类

这里就讨论比较常用的两个：

- BlockingQueue
- ConcurrentHashMap

**BlockingQueue**
阻塞队列。该类是java.util.concurrent包下的重要类，通过对Queue的学习可以得知，这个queue是单向队列，可以在队列头添加元素和在队尾删除或取出元素。类似于一个管　　道，特别适用于先进先出策略的一些应用场景。普通的queue接口主要实现有PriorityQueue（优先队列），有兴趣可以研究

BlockingQueue在队列的基础上添加了多线程协作的功能：

BlockingQueue

除了传统的queue功能（表格左边的两列）之外，还提供了阻塞接口put和take，带超时功能的阻塞接口offer和poll。put会在队列满的时候阻塞，直到有空间时被唤醒；take在队　列空的时候阻塞，直到有东西拿的时候才被唤醒。用于生产者-消费者模型尤其好用，堪称神器。

常见的阻塞队列有：

- ArrayListBlockingQueue
- LinkedListBlockingQueue
- DelayQueue
- SynchronousQueue

**ConcurrentHashMap**
高效的线程安全哈希map。请对比hashTable , concurrentHashMap, HashMap

### 5.管理类

管理类的概念比较泛，用于管理线程，本身不是多线程的，但提供了一些机制来利用上述的工具做一些封装。
了解到的值得一提的管理类：ThreadPoolExecutor和 JMX框架下的系统级管理类 ThreadMXBean
**ThreadPoolExecutor**
如果不了解这个类，应该了解前面提到的ExecutorService，开一个自己的线程池非常方便：

```
ExecutorService e = Executors.newCachedThreadPool();
    ExecutorService e = Executors.newSingleThreadExecutor();
    ExecutorService e = Executors.newFixedThreadPool(3);
    // 第一种是可变大小线程池，按照任务数来分配线程，
    // 第二种是单线程池，相当于FixedThreadPool(1)
    // 第三种是固定大小线程池。
    // 然后运行
    e.execute(new MyRunnableImpl());
```

该类内部是通过ThreadPoolExecutor实现的，掌握该类有助于理解线程池的管理，本质上，他们都是ThreadPoolExecutor类的各种实现版本。请参见javadoc：

ThreadPoolExecutor参数解释

翻译一下：
**corePoolSize**:池内线程初始值与最小值，就算是空闲状态，也会保持该数量线程。
**maximumPoolSize**:线程最大值，线程的增长始终不会超过该值。
**keepAliveTime**：当池内线程数高于corePoolSize时，经过多少时间多余的空闲线程才会被回收。回收前处于wait状态
**unit**：
时间单位，可以使用TimeUnit的实例，如TimeUnit.MILLISECONDS　
**workQueue**:待入任务（Runnable）的等待场所，该参数主要影响调度策略，如公平与否，是否产生饿死(starving)
**threadFactory**:线程工厂类，有默认实现，如果有自定义的需要则需要自己实现ThreadFactory接口并作为参数传入。

http://www.jianshu.com/p/40d4c7aebd66
