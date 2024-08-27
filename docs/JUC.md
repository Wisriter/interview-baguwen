## synchronized

【https://zhuanlan.zhihu.com/p/665862642】

synchronized关键字，在安全集合，比如`CopyOnWriteArrayList`里面就用到了这个关键字，而在juc的其他类里面确实没怎么看见这个关键字。synchronized可以作用于静态方法，实例方法，代码块。

对于实例方法，锁对象是实例对象本身（this），对于静态方法，锁对象是类的Class对象。对于代码块，可以手动指定锁对象。作用于代码块时有synchronized(this)和synchronized(对象)两种方式，区别如下：

1. synchronized(this)，锁定的是当前实例对象（this）。意味着同一实例的不同方法调用会相互排斥，但不同实例之间的方法调用不会相互排斥。
2. synchronized(对象)，例如`synchronized(lock)`，这个lock只是一个对象的名字，比如`final transient Object lock = new Object();`一般会用final修饰。锁对象可以有多个。因此可以按照需求让需要同步的多个代码块获取同一个锁对象。
3. 根据以上特性恰当地使用synchronized。

synchronized关键字在使用的过程中会有如下几个问题：

1.  不可控性，无法做到随心的加锁和释放锁； 
2.  效率比较低下，比如我们现在并发的读两个文件，读与读之间是互不影响的，但如果给这个读的对象使用synchronized来实现同步的话，那么只要有一个线程进入了，那么其他的线程都要等待； 
3.  无法知道线程是否获取到了锁； 

## JUC包源码！！

java.util.concurrent包，里面有两个子包以及很多接口和类。

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1716912585178-c543a453-d828-4be0-b3fe-8e751e273fb7.png" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1716913568102-24c67321-b115-421c-8647-e8d02b9fef51.png" alt="img" style="zoom:80%;" />

1. **atomic包：**这些原子类主要用于在多线程环境中实现线程安全的更新操作，避免了传统的锁机制，从而减少了上下文切换和提高了性能。内部使用CAS实现。

【https://www.jb51.net/article/270480.htm】

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1716912804078-5913e846-fe4d-4949-8c90-323055489419.png" alt="img" style="zoom:67%;" />

2. **locks包：**非常重要，包括：

- 3个接口：Lock、ReadWriteLock、Condition（不怎么考察）
- 3个抽象类：AOS、AQLS、AQS
- 2个重要的子类：ReentrantLock、ReentrantReadWriteLock

ReentrantLock是排他锁，即在同一时刻只允许一个线程进行访问，而读写锁在同一时刻可以允许多个读线程访问，但是在写线程访问时，所有的读线程和其他写线程均被阻塞。读写锁维护了一对锁，一个读锁和一个写锁，通过分离读锁和写锁，使得并发性相比一般的排他锁有了很大提升。

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1716913112949-555104a6-840d-4407-8404-75522f5c5f2c.png" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1716913339163-8a534692-8d73-4c2d-b253-27b6fe9b6493.png" alt="img" style="zoom:67%;" />



三个接口

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1716918328200-b2c8c0eb-1c10-4692-a2ac-1e26dc473043.png" alt="img" style="zoom:67%;" />

### Lock



### ReadWriteLock



### Condition

- Java 对象拥有一组监视方法：wait()、wait(long timeout)、notify() 以及 notifyAll() 方法，这些方法与 synchronized 同步关键字配合，可以实现等待/通知模式，进行线程之间的通讯。
- Condition 接口也提供了类似的方法，与 Lock 配合可以实现等待/通知模式，但两者使用方法和功能上存在差异。

使用Condition时，需要先调用Lock.lock()获取锁，通过Lock.newCondition()获取Condition对象，再调用Condition对象的方法。具体看代码。

常用方法：

1. await() : 线程主动释放锁，进入沉睡状态，直到被再次唤醒。
2. await(long time, TimeUnit unit) ：线程主动释放锁，进入沉睡状态，被唤醒或者未到达等待时间时一直处于等待状态。
3. signal()： 唤醒一个等待线程。
4. signal()All() ：唤醒所有等待线程,能够从等待方法返回的线程必须获得与Condition相关的锁。



### AQS

提供了一个框架用于实现大多数的锁和同步器，如ReentrantLock、ReentrantReadWriteLock等。抽象队列同步器AQS维护一个同步队列，线程获取锁失败时会被加入队列，等待前一个线程释放锁。

### ReentrantLock

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1716914865634-383a7fdd-68b3-4c78-8f31-a0036320d1b0.png" alt="img" style="zoom:67%;" />

***



## 实现多线程有哪些方式？

答：主要有4种。**继承 Thread 类、实现 Runnable 接口、实现 Callable 接口、使用线程池**

1. **继承 Thread 类**： 实现多线程最简单的方式是让自定义的类继承 Thread 类。重写 Thread 类的 run 方法，并创建该类的实例调用 start 方法来启动新线程。

```java
public class MyThread extends Thread {
    public void run() {
        // 线程执行的代码
    }
}
MyThread t = new MyThread();
t.start();
```

1. **实现 Runnable 接口**： 通过实现 Runnable 接口来创建线程。实现 Runnable 接口需要定义一个 run 方法，然后将该 Runnable 对象传给 Thread 对象，接着调用 Thread 对象的 start 方法。

```java
复制
public class MyRunnable implements Runnable {
    public void run() {
        // 线程执行的代码
    }
}
Thread t = new Thread(new MyRunnable());
t.start();
```

1. **实现 Callable 接口**： Callable 接口与 Runnable 相似，但它可以有返回值，并且可以抛出异常。Callable 任务通过 Future 对象来获取任务的结果。

```java
public class MyCallable implements Callable<String> {
    public String call() {
        // 返回线程执行的结果
        return "Callable result";
    }
}
Future<String> future = executor.submit(new MyCallable());
String result = future.get(); // 等待任务完成并获得结果
```

1. **使用线程池**： 线程池可以有效地管理线程，减少在创建和销毁线程时所产生的性能开销。通过线程池还可以控制最大并发数，提高系统资源的利用率。

```java
ExecutorService threadPool = Executors.newFixedThreadPool(3);
for (int i = 0; i < 5; i++) {
    threadPool.submit(new MyRunnable());
}
threadPool.shutdown();
```

- 如果任务简单且不需要返回结果或异常处理，可以使用继承 Thread 类或实现 Runnable 接口。
- 如果需要任务返回结果或处理异常，可以使用实现 Callable 接口并结合线程池。
- 对于复杂的应用，需要大量并发线程，通常会选择使用线程池。

***



## 线程池的参数和拒绝策略

【https://blog.csdn.net/qq_36896711/article/details/134820062】

答：7大参数，4大拒绝策略。

为什么用线程池：每次创建线程的时候都会占用一定的内存空间，如果不断地进行创建线程，可能会导致消耗大量的内存，导致OOM（Out Of Memory内存溢出）。一台电脑的cpu是有限的，同一时刻每个cpu只能处理一个线程，如果大量的请求到来，我们创建了大量的线程，那么很多线程都没有cpu的执行权，就需要等待cpu调度，这将导致大量线程的切换，也会导致性能变慢。所以说，一般在项目开发的过程中，都会使用线程池来管理线程

创建：在Java中，主要使用ThreadPoolExecutor类来创建线程池，同时JDK也提供了Executors工厂类来创建线程池。但更推荐ThreadPoolExecutor类。

```bash
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
```

1. corePoolSize（核心线程数目）：线程池中同时存在的最小线程数，即使线程处于空闲状态，也不会被回收
2. maximumPoolSize（最大线程数）：线程池中允许存在的最大线程数，当队列满时并且当前线程数小于最大线程数时，线程池会创建新的线程（救急线程）来处理任务。最大线程数=核心线程数+救急线程数的最大值。【应用程序提交一个任务时，线程池会根据当前线程的状态和参数决定如何处理这个任务。如果线程池中的核心线程都在忙，并且线程池未达到最大线程数，新提交的任务会被放入队列中进行等待。如果任务队列已满，且当前线程数量小于最大线程数，线程池会创建新的线程来处理任务。】
3. keepAliveTime（生存时间）：当线程池中的线程数量大于核心线程数时，多余的空闲线程（救急线程）在空闲时间超过keepAliveTime后会被回收。
4. unit(时间单位)：用于设置keepAliveTime的时间单位，例如TimeUnit.SECONDS。
5. workQueue（工作队列）：用于存储等待执行的任务的阻塞队列，当没有空闲核心线程时，新来任务会加入到此队列排队，队列满会创建新线程执行任务
6. threadFactory（线程工厂）：用于创建新线程的工厂，可以定制线程对象的创建，例如设置线程名字、是否是守护线程等
7. handler（拒绝策略）：当所有线程都在繁忙，workQueue也放满时，会触发拒绝策略

流程：

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1714020825209-4a334b45-c80b-43ac-8966-f15bc5cf87da.png" alt="img" style="zoom:80%;" />



**4种已经实现了的拒绝策略：**

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1717389838793-0328d7e6-2621-47ce-9564-88287cb2cd58.png" alt="img" style="zoom: 67%;" />

1. AbortPolicy（默认）：当任务添加到线程池中被拒绝时，会抛出RejectedExecutionException异常。
2. CallerRunsPolicy：当任务添加到线程池中被拒绝时，会使用调用者所在的线程来执行该任务。
3. DiscardOldestPolicy：当任务添加到线程池中被拒绝时，会丢弃队列中最靠前（来的最久）的任务，然后尝试重新提交被拒绝的任务。
4. DiscardPolicy：当任务添加到线程池中被拒绝时，会丢弃该任务，不会有任何异常抛出。

也可以自己去实现这个接口，自定义拒绝策略。



**线程池常用的阻塞队列：**

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1717307930619-91523363-8218-4c8d-a2ae-030a13bbcc28.png" alt="img" style="zoom:67%;" />



## 创建线程池的方法

答：线程池的创建可以分为以下两大类：

- 通过 ThreadPoolExecutor 手动创建线程池。
- 通过 Executors 执行器自动创建线程池

以上两类创建线程池的方式，又有 7 种具体实现方法，这 7 种实现方法分别是：

1. Executors.newSingleThreadExecutor：创建只有**一个线程**数的线程池，它可以保证先进先出的执行顺序，但是等待队列是Integer.MAX_VALUE。

2. Executors.newCachedThreadPool：创建一个**可缓存**的线程池，若线程数超过处理所需，缓存一段时间后会回收，若线程数不够，则新建线程。核心线程数是0，最大线程数Integer.MAX_VALUE个。

3. Executors.newFixedThreadPool：创建一个**固定大小**的线程池，可控制并发的线程数，超出的线程会在队列中等待。等待队列是Integer.MAX_VALUE。

4. Executors.newScheduledThreadPool：创建一个可以执行**延迟任务**的线程池。核心线程数是指定的，最大线程数为Integer.MAX_VALUE，等待队列是一个延迟无界队列。

5. Executors.newSingleThreadScheduledExecutor：创建一个**单线程的可以执行延迟任务**的线程池。核心线程数为1，但是最大线程数为Integer.MAX_VALUE，等待队列是一个延迟无界队列。

6. Executors.newWorkStealingPool：创建一个**抢占式执行的线程池**（任务执行顺序不确定，jdk1.8引入），等待队列是Integer.MAX_VALUE。

7. ThreadPoolExecutor：**手动创建线程池**的方式。


使用**ThreadPoolExecutor** 对象手动创建线程池的方式：

```java
public ThreadPoolExecutor(int corePoolSize,  
                          int maximumPoolSize,  
                          long keepAliveTime,  
                          TimeUnit unit,  
                          BlockingQueue<Runnable> workQueue,  
                          ThreadFactory threadFactory,  
                          RejectedExecutionHandler handler) 
```

【[https://javabetter.cn/sidebar/sanfene/javathread.html#_54-%E8%83%BD%E8%AF%B4%E4%B8%80%E4%B8%8B%E5%9B%9B%E7%A7%8D%E5%B8%B8%E8%A7%81%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E5%8E%9F%E7%90%86%E5%90%97](https://javabetter.cn/sidebar/sanfene/javathread.html#_54-能说一下四种常见线程池的原理吗)】（沉默王二八股）

 阿里《Java 开发手册》里禁止使用 Excutors类里面的方法来创建线程池。从源码可以看到ThreadPoolExecutor类有4个构造函数，当使用Excutors类里面的方法创建线程池时，使用的是ThreadPoolExecutor的前两种构造方法，参数列表里面没有拒绝策略，这就有OOM的隐患，而使用后面两种构造方法可以设置拒绝策略。

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1717310506533-d5eed5f8-0df9-4df6-ab09-2d9613720d56.png" alt="img" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1717310676619-eeac5c9f-8695-4a65-8406-1e80679aa49d.png" alt="img" style="zoom:67%;" />

主要介绍一下Executors里面4种常见的线程池构造方法，前三种线程池的构造直接调用 ThreadPoolExecutor 的构造方法。而newScheduledThreadPool不太一样，详见源码。

1. newFixedThreadPool和newSingleThreadPool的核心线程数量都等于最大线程数量，阻塞队列都使用LinkedBlockingQueue。可能造成OOM的原因就在于无界队列。newSingleThreadPool适合串行任务，newFixedThreadPool适合CPU密集型任务

```java
public static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory) {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>(),
                                threadFactory));
}
```

3. newCachedThreadPool的核心线程数为0，最大线程数为Integer.MAX_VALUE，空闲时间为0（keepAliveTime=0），阻塞队列为SynchronousQueue。适合并发大量短期小任务，OOM的原因在于来一个任务就创建一个线程。

4. newScheduledThreadPool的构造方法如下，也是来一个任务就创建一个线程，阻塞队列是 DelayedWorkQueue。

```java
public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
          new DelayedWorkQueue());
}
```

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1717308062102-122af64c-6735-4afc-ac30-4b79ba98b239.png" alt="img" style="zoom:67%;" />

要知道它们各自的构造方法，工作机制，适用场景！！

线程池参数设置，主要在于**核心线程数**和**最大线程数。**分为三种情况，CPU密集型，IO密集型，混合型

1. 调用 availableProcessors() 方法来获取当前系统中可用的处理器数量。

```java
//核数
int processorAmount = Runtime.getRuntime().availableProcessors();
```

1. 核心线程数：推荐使用以下方式设置核心线程，（CPU密集：核心线程数=CPU内核数 + 1）（IO密集： 核心线程数=CPU内核数 * 2）
2. 最大线程数：推荐核心线程数+核心线程数/2。`int maxThreadSize = coreThreadSize + (coreThreadSize / 2);`

------



## AQS类

【https://www.cnblogs.com/54chensongxia/p/11970870.html】（内容很多）

答：AbstractQueuedSynchronizer （简称AQS）类是整个 JUC包的核心类。JUC 中的ReentrantLock、ReentrantReadWriteLock、 CountDownLatch、Semaphore和LimitLatch等同步器都是基于AQS实现的。AQS是一个抽象类，主是是以继承的方式使用。AQS本身是没有实现任何同步接口的，它仅仅只是定义了同步状态的获取和释放的方法来供自定义的同步组件的使用。

AQS 分离出了构建同步器时的通用关注点，这些关注点主要包括如下：

- 资源是可以被同时访问？还是在同一时间只能被一个线程访问？（共享/独占功能）。ReentrantLock（可重入锁）是独占的；Semaphore（信号量）/CountDownLatch（倒计时器）是共享的。
- 访问资源的线程如何进行并发管理？（等待队列）
- 如果线程等不及资源了，如何从等待队列退出？（超时/中断）

这些关注点都是围绕着资源——同步状态（synchronization state）来展开的，AQS将这些通用的关注点封装成了一个个模板方法，让子类可以直接使用。

AQS 留给用户的只有两个问题：

- 什么是资源
- 什么情况下资源是可以被访问的

这样一来，定义同步器的难度就大大降低了。用户只要解决好上面两个问题，就能构建出一个性能优秀的同步器。下面是几个常见的同步器对资源的定义：

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1714666807246-cba688cb-ed76-449e-92f6-f662d9af9e72.png" alt="img" style="zoom:67%;" />

**同步状态的管理：**同步状态其实就是资源。AQS使用单个int（32位）来保存同步状态，并暴露出getState、setState以及compareAndSetState操作来读取和更新这个状态。

**线程的阻塞和唤醒：**在JDK1.5之前，使用内置的监视器机制。JDK1.5以后，java.util.concurrent.locks包提供了LockSupport类来作为线程阻塞和唤醒的工具。

**等待队列：**是AQS框架的核心，整个框架的关键其实就是如何在并发状态下管理被阻塞的线程。等待队列是严格的FIFO队列，采用双向循环链表实现，也叫CLH队列。结点一共有两种类型：独占（EXCLUSIVE）和共享（SHARED），每种类型的结点都有一些状态。当线程请求资源时，如果请求不到，会将线程包装成结点，将其挂载在队列尾部。

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1714668238019-2b6dfdb0-a012-4bea-8935-adb088c71ac1.png" alt="img" style="zoom:67%;" />

**自己写一个同步器：**一般情况下只要继承 AQS ，并重写 AQS 中的这几个方法就行了。至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。要不怎么说Doug Lea贴心呢。需要注意的是：如果你没在子类中重写这几个方法就直接调用了，会直接抛出异常。所以，在你调用这些方法之前必须重写他们。不使用的话可以不重写。

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1714667522358-2b4ef2d4-d4f6-49b6-b157-92b23f47993f.png" alt="img" style="zoom:67%;" />



## wait和sleep

在Java中，wait() 和 sleep() 都是线程控制的方法，但它们之间有着显著的区别。以下是它们之间的主要差异：

1. **所属类**：

- - wait() 方法是 java.lang.Object 类的方法，因此所有Java对象都可以调用它。
  - sleep() 方法是 java.lang.Thread 类的方法，只能被线程实例调用。

1. **用途**：

- - wait() 方法通常用于多线程间的通信，使得一个线程等待某个条件成立（通常是通过其他线程的操作）。当调用 wait() 时，线程会释放它持有的对象锁，并进入等待状态，直到其他线程调用了同一个对象的 notify() 或 notifyAll() 方法，或者等待超时。
  - sleep() 方法仅用于让当前线程暂停执行一段时间，不释放任何锁，只是简单地让出CPU的使用权，使其他线程有机会执行。

1. **唤醒机制**：

- - wait() 依赖于其他线程调用 notify() 或 notifyAll() 方法来唤醒。
  - sleep() 方法在指定的时间间隔后自动唤醒，无需其他线程干预。

1. **同步控制**：

- - 由于 wait() 方法在调用时需要先获取对象的锁，因此它通常与 **synchronized 关键字**一起使用，以确保线程安全。
  - sleep() 方法不涉及锁的操作，因此可以独立使用。

## wait()与notify()方法

答：

wait 做的事情：

1. 释放当前的锁。
2. 使当前执行代码的线程进入阻塞等待（把线程放到等待队列中）。
3. 满足一定条件时（收到通知时）被唤醒，同时重新尝试获取这个锁。

wait 结束等待的条件：

1. 其他线程调用该对象的 notify 方法。
2. wait 等待时间超时 （wait 方法提供一个带有 timeout 参数的版本，来指定等待时间）。
3. 其他线程调用该等待线程的 interrupted 方法，导致 wait 抛出 InterruptedException 异常。

注意wait() 必须搭配 synchronized 来使用，以下是一个简单的示例

```vbnet
public class Test2 {
    public static void main(String[] args) throws InterruptedException {
        Object locker = new Object();
 
        Thread t1 = new Thread(() -> {
                try {
                    System.out.println("wait开始");
                    synchronized (locker) {
                        locker.wait();
                    }
                    System.out.println("wait结束");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
        });
 
        t1.start();
 
        Thread.sleep(1000); //保证t1先启动，wait()先执行
 
        Thread t2 = new Thread(() -> {
            synchronized (locker) {
                System.out.println("notify开始");
                locker.notify();
                System.out.println("notify结束");
            }
        });
 
        t2.start();
    }
}
```

如果有多个线程在wait()，notify()是只随机唤醒一个；而notifyAll()则是唤醒所有，然后线程重新竞争锁。



这个示例中，我们有一个生产者线程和一个消费者线程。生产者线程生产整数并将它们放入一个**共享的队列**中，而消费者线程则从该队列中取出整数进行消费。我们使用 synchronized 块来同步对共享队列的访问，并使用 wait() 和 notifyAll() 方法来实现线程间的通信。



一个多线程的场景题，以生产者和消费者为例。考察wait和notify方法

- 当队列已满时，生产者线程会调用 wait() 方法进入等待状态，直到消费者线程从队列中取出一些元素并调用 notifyAll() 方法唤醒它。
- 当队列为空时，消费者线程会调用 wait() 方法进入等待状态，直到生产者线程向队列中添加一些元素并调用 notifyAll() 方法唤醒它。

这样，我们就实现了一个简单的生产者和消费者模型，其中生产者和消费者可以并发地运行而不会相互干扰。



## synchronized、volatile、lock

答：volatile关键字可以保证可见性、有序性，但无法保证原子性。而synchronized关键字，可以同时保证三者。

**synchronized：**是Java内置的关键字。可以修饰实例方法、静态方法、代码块。synchronized是可重入锁。

synchronized可以确保同一时刻只有一个线程可以访问共享资源。当一个线程进入一个对象的synchronized(this)方法或代码块时，它会获取该对象的锁，然后执行该方法或代码块。其他尝试进入该对象的synchronized方法或代码块的线程将被阻塞，直到第一个线程释放锁。

synchronized锁的释放是自动的，锁释放会在以下几种情况下发生：

1. 当一个线程进入synchronized块或方法并执行完这些代码后，它会自动释放锁。
2. 如果在synchronized块或方法中，线程因为调用了wait()方法而进入等待状态，那么这个线程会释放锁。
3. 如果在synchronized块或方法中，线程因为发生了未捕获的异常而终止，那么这个线程也会释放锁。

```java
public class SynchronizedTest1 {
    public synchronized void test() {//修饰实例方法
        System.out.println("synchronized 修饰 方法");
    }
}

public static synchronized void test(){//修饰静态方法
       i++;
}

public class SynchronizedTest2 {
    public void test() {
        synchronized (this) {//修饰代码块
            System.out.println("synchronized 修饰 代码块");
        }
    }
}
```



## 构造方法可以用synchronized修饰吗？

不能，也不需要，因为构造方法本身就是线程安全的

**volatile**：volatile不是锁，它是一种轻量级的同步机制，主要用于确保多线程之间的可见性。

当一个共享变量被声明为volatile时，它会告诉JVM这个变量是不稳定的，每次使用它的时候都要从主内存中读取它的值，而当它发生变化的时候，又会立即同步到主内存中。

volatile并不能保证原子性，也就是说，它不能保证复合操作（如i++）的原子性。

volatile的使用场景主要是当一个变量被多个线程共享，且该变量的值经常改变时，使用volatile可以确保每个线程都能看到该变量的最新值。

**Lock：**是Java提供的一个接口，需要我们手动管理锁的获取和释放，unlock()方法来释放锁。ReentrantLock（可重入锁）是Lock的一个实现类。在一些需要更灵活控制线程同步的场景下，可以考虑使用Lock。



## juc包里面的常用工具类

答：【https://blog.csdn.net/weixin_44440231/article/details/137756739】

常用的有CountDownLatch（计数器）、CyclicBarrier（循环栅栏）、Semaphore（信号量）、Phaser（同步器）

1. 计数器用于实现一个线程（或多个线程）等待一组线程完成各自任务后的同步。初始化时设定一个计数器，每当一个任务线程完成任务后调用 countDown() 方法使计数器减一。需要等待的线程通过 await() 方法阻塞等待，直到计数器归零，表示所有任务线程已结束，等待线程才能继续执行。使用时，传递一个整数作为参数，表示需要等待的线程数量`CountDownLatch latch = new CountDownLatch(n);`
2. 循环栅栏用于让一组线程到达某个屏障（同步点）时互相等待，直到所有线程都到达后才能继续执行。与 CountDownLatch 不同的是，它可以重复使用，当所有线程到达屏障时，计数器重置，线程可以再次参与到新的循环中。线程通过 await() 方法进入等待状态，直到所有参与者都到达。适用于周期性的多线程协作场景。在所有线程到达屏障时，可以触发一个预设的屏障动作（通过构造函数传入一个 Runnable），用于执行一些全局的初始化操作、统计结果、清理资源等。

```java
//1. 创建实例
CyclicBarrier barrier = new CyclicBarrier(parties, barrierAction); // parties 为参与线程数，barrierAction 为可选的屏障动作
//2. 每个线程在屏障点都调用await()方法
try {
    barrier.await(); // 可能抛出 BrokenBarrierException 或 InterruptedException
} catch (InterruptedException | BrokenBarrierException e) {
    // 处理中断或屏障破坏的情况
}
//3. 执行屏障动作（可选）
//4. 重复使用
```

1. 信号量用于控制同时访问特定资源的线程数量。acquire() 方法获取许可证（如果无可用许可证则等待），release() 方法释放许可证。常用于限流或资源池（比如数据库连接池、线程池）场景。使用：①构造 Semaphore 实例；②线程调用 acquire() 获取许可；③线程调用 release() 释放许可。此外，Semaphore 还提供了非阻塞版本的 tryAcquire() 方法，以及带有超时的 tryAcquire(long timeout, TimeUnit unit) 方法，允许线程尝试获取许可而不必立即阻塞。
2. 同步器允许一组线程按照预定的阶段（phase）进行协作，每个阶段可以有多个参与线程。线程在每个阶段开始时注册，完成各自任务后注销，所有参与线程在当前阶段全部注销后，Phaser 进入下一个阶段。Phaser 支持动态注册新的参与线程，且可以循环使用，适用于复杂的多阶段并发任务协调场景。使用步骤包括：

1. 1. 构造 Phaser 实例，可以传入一个参与的线程数
   2. 线程注册与注销，注册arriveAndAwaitAdvance() 或 register()，注销arriveAndDeregister() 或 arrive()
   3. 查询与控制，使用 getPhase() 查询当前阶段数。调用 reset() 方法重置 Phaser，使其回到初始状态，准备开始新一轮的多阶段同步。



## **Synchronized和ReentrantLock的使用场景的区别**

答：Synchronized是关键字。ReentrantLock是locks包下面的一个锁类，使用lock()方法和unlock()方法进行加锁解锁，此外还有一个trylock(time 超时时间)方法，表示如果一定时间内没有获取到就放弃。



## 阻塞队列有哪些?如何保证安全性的?

答：JDK17里面有7种（除去Deque）。常用的有ArrayBlockingQueue、LinkedBlockingQueue、SynchronousQueue、PriorityBlockingQueue、DelayQueue。其中，ArrayBlockingQueue、LinkedBlockingQueue都是先进先出；PriorityBlockingQueue可以自定义排序。

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1717011874769-3aecbb62-3fc3-4378-848b-27f51b20e725.png" alt="img" style="zoom:67%;" />

1. ArrayBlockingQueue是有界队列，内部用数组存储元素，利用 ReentrantLock 实现线程安全，使用 Condition 来阻塞和唤醒线程。构造器如下：

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1717012458798-049cce46-d087-44af-9764-aaeb616142fe.png" alt="img" style="zoom:67%;" />

1. LinkedBlockingQueue使用链表实现，是无界队列，但也可以设置固定大小。它同样也利用 ReentrantLock 实现线程安全，使用 Condition 来阻塞和唤醒线程，但ReentrantLock 默认是非公平的，构造器没有fair参数：

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1717012657784-75f22c42-4e36-49b1-a08f-06b34c98e382.png" alt="img" style="zoom:67%;" />

1. SynchronousQueue 的容量为 0，所以没有一个地方来暂存元素，导致每次取数据都要先阻塞，直到有数据被放入；同理，每次放数据的时候也会阻塞，直到有消费者来取。SynchronousQueue 的 size 方法始终返回 0，因为它内部并没有容量。
2. PriorityBlockingQueue 是一个支持优先级的无界阻塞队列，可以通过自定义类实现 compareTo() 方法来指定元素排序规则，或者初始化时通过构造器参数 Comparator 来指定排序规则。同时，插入队列的对象必须是可比较大小的，也就是 Comparable 的，否则会抛出 ClassCastException 异常。
3. DelayQueue 这个队列比较特殊，具有“延迟”的功能。我们可以设定让队列中的任务延迟多久之后执行，比如 10 秒钟之后执行，这在例如“30 分钟后未付款自动取消订单”等需要延迟执行的场景中被大量使用。



## CAS是什么？CAS频繁失败如何解决？

答：【https://zhuanlan.zhihu.com/p/665440194】（java CAS原理和应用场景）

CAS（Compare and Swap）是一种乐观锁机制，它是一种基于硬件指令实现的原子操作，可以在不使用传统互斥锁的情况下，保证多线程对共享变量的安全访问。在Java中，我们可以使用Atomic类和AtomicReference类来实现CAS操作，这些类提供了一系列原子更新方法，如compareAndSet、getAndSet、incrementAndGet等。

CAS操作存在哪些问题？

答案： CAS操作存在一些问题，其中最常见的是ABA问题。**ABA问题指的是，一个共享变量的值从A变为B，然后再从B变回A，这样CAS操作可能会错误地认为没有其他线程修改过值。**为了解决ABA问题，可以使用带有版本号的CAS操作，确保CAS操作同时检查值和版本号。也可以用时间戳当作版本号。



## **如何设定线程池的大小，如何保证这个线程池是高效的?**

答：根据任务是IO密集型还是CPU密集型来设定核心线程数，而最大线程数一般设置为核心线程数的几倍



## 为什么生产中不推荐用executor包下的fixed,cachedthreadpool等

答：因为存在OOM的问题，详见前面“创建线程池的方法”

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1717517525089-adeda436-8b05-4248-9814-d3decdcf4ac7.png" alt="img" style="zoom:67%;" />