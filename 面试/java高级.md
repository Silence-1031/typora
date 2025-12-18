1. #### HashMap底层原理

HashMap基于哈希表的Map接口实现，是以key-value存储形式存在，即主要用来存放键值对。HashMap 的实现不是同步的，这意味着它不是线程安全的。它的key、value都可以为null。

JDK1.8 之前 HashMap 由 数组+链表 组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）.JDK8 以后在解决哈希冲突增加了红黑树作为底层数据结构，当链表长度大于阈值（或者红黑树的边界值，默认为 8）并且当前数组的长度大于64时，此时此索引位置上的所有数据改为使用红黑树存储。

补充：将链表转换成红黑树前会判断，即使阈值大于8，但是数组长度小于64，此时并不会将链表变为红黑树。而是选择进行数组扩容。

这样做的目的是因为数组比较小，尽量避开红黑树结构，这种情况下变为红黑树结构，反而会降低效率，为了提高性能和减少搜索时间，只有满足条件时链表才转换为红黑树。。

当然虽然增了红黑树作为底层数据结构，结构变得复杂了，但是阈值大于8并且数组长度大于64时，链表转换为红黑树时，效率也变的更高效。




2. #### **JVM内存分哪几个区，每个区的作用是什么**

![img](https://i-blog.csdnimg.cn/blog_migrate/33df2502dd158c62f76f4aa5f26376ec.png)

一、线程私有区域（每个线程独立拥有，随线程创建 / 销毁）

1. 程序计数器（Program Counter Register）

- 作用：记录当前线程执行的字节码指令地址（行号），是 JVM 中唯一不会抛出`OutOfMemoryError`的区域；
  - 线程切换时，通过程序计数器恢复当前线程的执行位置，保证线程切换后能继续执行；
  - 若线程执行的是 Native 方法（如 JNI 调用），计数器值为 undefined。
- **特点**：占用内存极小，生命周期与线程一致。

2. 虚拟机栈（VM Stack）

- 作用：存储方法执行的 “栈帧”（每个方法调用对应一个栈帧），栈帧包含局部变量表、操作数栈、方法出口等；
  - 局部变量表：存储方法内的局部变量（基本类型、对象引用、returnAddress 类型），大小在编译期确定；
  - 方法调用时创建栈帧入栈，方法执行完出栈，栈帧的入栈 / 出栈对应方法的调用 / 结束。
- **异常**：栈深度超过虚拟机允许的范围（如递归无终止），抛出`StackOverflowError`；栈容量动态扩展时内存不足，抛出`OutOfMemoryError`。

3. 本地方法栈（Native Method Stack）

- **作用**：与虚拟机栈功能类似，但专门服务于 Native 方法（如 Java 调用 C/C++ 编写的方法）；
- **特点**：HotSpot 虚拟机将虚拟机栈和本地方法栈合并实现；同样会抛出`StackOverflowError`和`OutOfMemoryError`。

二、线程共享区域（所有线程共用，随 JVM 启动 / 关闭）

1. 堆（Heap）

- 作用：JVM 中最大的内存区域，唯一目的是存储对象实例（几乎所有对象都在堆上分配），是垃圾回收（GC）的核心区域；
  - 堆被划分为 “新生代” 和 “老年代”（更细分为 Eden 区、Survivor 区（From/To）、Old 区），GC 主要针对堆进行：
    - 新生代：存储新创建的对象，GC 频率高（Minor GC），采用复制算法，回收效率高；
    - 老年代：存储新生代中多次 GC 后仍存活的对象，GC 频率低（Major GC/Full GC），采用标记 - 清除 / 标记 - 整理算法。
- **异常**：堆内存不足（如创建大量大对象无法回收），抛出`OutOfMemoryError: Java heap space`。
- **特点**：可通过`-Xms`（初始堆大小）、`-Xmx`（最大堆大小）配置，是 JVM 调优的核心区域。

2. 元空间（Metaspace，JDK8 替代永久代）

- 作用：存储类的元数据（类的结构信息：类名、方法、字段、常量池、静态变量、方法字节码等），以及方法区的其他内容（如运行时常量池）；
  - 运行时常量池：是类常量池的运行时版本，存储字面量（如字符串常量）和符号引用，`String.intern()`就是利用运行时常量池实现；
- **区别于永久代**：元空间不再占用 JVM 堆内存，而是使用本地内存（操作系统内存），默认无固定大小限制（可通过`-XX:MetaspaceSize`/`-XX:MaxMetaspaceSize`限制）；
- **异常**：元空间内存不足（如加载大量类未卸载），抛出`OutOfMemoryError: Metaspace`。





#### **什么情况下会产生StackOverflowError（栈溢出）和OutOfMemoryError（堆溢出）?怎么排查**

一、StackOverflowError（栈溢出）

1. 产生场景（核心：虚拟机栈深度超过阈值）

虚拟机栈用于存储方法栈帧，当方法调用深度超出 JVM 允许的栈深度上限时触发，常见场景：

- **无限递归调用**：最典型场景，如递归方法无终止条件（如计算阶乘时递归基例写错），每次递归创建新栈帧，栈深度持续增加；
- **方法调用链过深**：非递归但调用层级极深（如多层嵌套的业务方法、框架级别的深度调用）；

二、OutOfMemoryError（内存溢出）—— 重点说堆溢出（Java heap space）

堆是存储对象实例的核心区域，当对象持续创建且无法被 GC 回收（内存泄漏），或堆容量不足以承载正常业务对象时触发：

- **内存泄漏**：对象被无用但可达的引用持有（如静态集合缓存大量对象不清理、连接池未关闭导致连接对象泄漏、ThreadLocal 未移除导致线程池线程持有对象），GC 无法回收，堆内存逐步耗尽；
- **内存溢出（无泄漏）**：业务需要创建大量大对象 / 数组（如一次性加载 100 万条数据库记录到内存），堆容量不足以支撑，即使 GC 回收了所有无用对象，仍无足够空间分配新对象；
- **堆配置过小**：`-Xms`（初始堆）/`-Xmx`（最大堆）设置过小（如`-Xmx256m`），无法满足正常业务需求。



进程、线程与多线程

- 进程是操作系统分配资源（内存、CPU 等）的基本单位，是程序的一次运行实例（如一个 Java 程序启动对应一个 JVM 进程），拥有独立的内存空间，进程间相互隔离；
- 线程是进程内的执行单元，是CPU 调度的基本单位，一个进程可包含多个线程，线程共享进程的内存资源（堆、方法区），仅拥有独立栈空间；
- 多线程则是在一个进程内同时启动多个线程并行执行不同任务，能充分利用 CPU 多核资源，提升程序执行效率（如 Java 通过 Thread、Runnable 实现多线程，解决单线程阻塞导致的效率低下问题）



16. #### 线程的创建方式

最基础的是**继承 Thread 类**，自定义类继承`Thread`，重写`run()`方法，封装线程需要执行的逻辑，最后通过`start()`方法启动线程；缺点：Java 单继承限制，继承 Thread 后无法继承其他类，耦合度高；

以及**实现 Runnable 接口**，自定义类实现`Runnable`接口，先定义一个线程任务，重写`run()`方法，将该类实例传入`Thread`构造器，包装为线程对象，通过`Thread.start()`启动，这两种均无返回值；优点是**任务与线程解耦**：任务（`Runnable`）和线程（`Thread`）分离，便于多个线程共享同一个任务对象（如多线程操作同一资源）。

**实现 Callable 接口**可以返回线程执行的结果，自定义类实现`Callable<V>`接口，重写`call()`方法（支持返回值、抛出受检异常），`FutureTask`（实现 Runnable）封装任务，再传入`Thread`启动，可通过`FutureTask.get()`获取线程执行结果，且能处理异常；

实际开发中更常用线程池（如 ExecutorService）管理线程（基于 Runnable/Callable），而非直接创建 Thread 实例，既简化管理又提升资源复用效率，避免频繁创建销毁线程的性能开销，是企业级开发的标准做法。



- 启动线程必须用`start()`，而非直接调用`run()`

  - **`start()`的作用**：① 向 CPU 注册新线程；② CPU 调度后自动调用`run()`执行任务（真正实现 “多线程”）；

  - **直接调用`run()`的问题**：仅将线程对象当作 “普通对象” 调用方法，不会启动新线程，代码仍按 “单线程顺序执行”（如先执行完`run()`的循环，再执行主线程循环）。





17. #### 线程的状态转换有什么（生命周期）

Java 线程的生命周期核心分为**6 种状态**（定义在`Thread.State`枚举中），状态转换围绕 “启动 - 运行 - 阻塞 - 结束” 展开，核心状态及转换逻辑如下：

![img](https://i-blog.csdnimg.cn/blog_migrate/07a5293c06bac45357d0da0c90d306e9.jpeg)

**核心 6 种状态**

- NEW 状态是线程对象已创建但未调用 start ()，仅为普通对象，未与操作系统线程绑定；
- 调用 start () 后进入 RUNNABLE 状态，说明线程已就绪，等待 CPU 调度；；
- 获取 CPU 权限执行时切换为 Running 运行状态（仅能从就绪态进入）；
- 阻塞状态是线程放弃 CPU 使用权，暂时停止运行，分为
  -  wait () 导致的等待阻塞，属于 `WAITING`状态：线程释放锁后进入无时限等待，需其他线程调用`notify()`/`notifyAll()`唤醒
  - 获取 synchronized 锁失败的同步阻塞，属于blocked状态
  - sleep ()/ join ()/ I/O 请求导致的其他阻塞， 属于`TIMED_WAITING`：线程休眠指定时长，不释放锁。当sleep()状态超时、join(）等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。
- 最终线程执行完或异常退出 run () 方法则进入 Dead 死亡状态，结束生命周期。





7. 为什么要使用线程池

线程池核心是**解决线程频繁创建销毁的性能损耗问题**，线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，线程池的工作主要是控制运行的线程数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超出数量的线程排队等候，等其它线程执行完毕，再从队列中取出任务来执行。






8. #### **线程池底层工作原理**

![img](https://i-blog.csdnimg.cn/blog_migrate/35a8f50ff016795fa2e9b79f034d8d3e.png)

第一步：线程池刚创建的时候，里面没有任何线程，等到有任务过来的时候才会创建线程。
第二步：调用`execute()`提交一个任务时，如果当前的工作线程数<`corePoolSize`，直接创建新的线程执行这个任务
第三步：如果当时工作线程数量`>=corePoolSize`，会将任务放入**任务队列**中缓存
第四步：如果队列已满，并且线程池中工作线程的数量<maximumPoolSize，会创建临时线程执行这个任务
第五步：如果队列已满，并且线程池中的线程已达到maximumPoolSize，这个时候会执行**拒绝策略**，JAVA线程池默认的策略是AbortPolicy，即抛出`RejectedExecutionException`异常





9. #### ThreadPoolExecutor对象有哪些参数 怎么设定核心线程数和最大线程数 拒绝策略有哪些

   **参数与作用：共7个参数**

- corePoolSize：核心线程数，
  在ThreadPoolExecutor中有一个与它相关的配置：`allowCoreThreadTimeOut`（默认为false），当allowCoreThreadTimeOut为false时，核心线程会一直存活，哪怕是一直空闲着。而当allowCoreThreadTimeOut为true时核心线程空闲时间超过keepAliveTime时会被回收。

- maximumPoolSize：最大线程数
  线程池能容纳的最大线程数，当线程池中的线程达到最大时，此时添加任务将会采用拒绝策略，默认的拒绝策略是抛出一个运行时错误（RejectedExecutionException）。值得一提的是，当初始化时用的工作队列为LinkedBlockingDeque时，这个值将无效。

- keepAliveTime：存活时间，
  当非核心空闲超过这个时间将被回收，同时空闲核心线程是否回收受allowCoreThreadTimeOut影响。

- unit：keepAliveTime的单位。
- workQueue：任务阻塞队列
  存储等待执行的任务，如 ArrayBlockingQueue常用有三种队列，即SynchronousQueue,LinkedBlockingDeque（无界队列）,ArrayBlockingQueue（有界队列）。

- threadFactory：线程工厂，
  ThreadFactory是一个接口，用来创建线程。通过线程工厂可以对线程的一些属性进行定制。默认直接新建线程。

- RejectedExecutionHandler：拒绝策略也是一个接口，只有一个方法，当线程池中的资源已经全部使用，添加新线程被拒绝时，会调用`RejectedExecutionHandler`的`rejectedExecution`方法。默认是抛出一个运行时异常。

**线程池大小设置：**

首先要分析线程池执行的任务的特性： CPU 密集型还是 IO 密集型

如果是 CPU 密集型，主要是执行计算任务，响应时间很快，cpu 一直在运行，这种任务 cpu的利用率很高，那么线程数的配置应该根据 CPU 核心数来决定，一般设置为 cpu 核心数+1 ，来应对一些线程阻塞问题

如果是 IO 密集型，执行 IO 操作的时间较长，这时 cpu 处于空闲状态，利用率不高，这种情况下可以增加线程池的大小。这种情况下可以结合线程的等待时长来做判断，等待时间越高，那么线程数也相对越多。一般可以配置 cpu 核心数的 2 倍。

同时需结合业务场景调整，如考虑任务队列长度、系统负载、拒绝策略，避免线程数过多导致上下文切换频繁，或过少导致资源利用率低

**拒绝策略：**

AbortPolicy：直接抛出`rejectedExecution`异常，默认策略；
CallerRunsPolicy：用调用者所在的线程来执行任务；
DiscardOldestPolicy：丢弃阻塞队列中靠最前的任务，并执行当前任务；
DiscardPolicy：直接丢弃任务；当然也可以根据应用场景实现 RejectedExecutionHandler 接口，自定义策略



> 10. 常见线程安全的并发容器有哪些
>
> 常见的线程安全并发容器可按核心用途分类，核心如下：
>
> 1. **ConcurrentHashMap**：线程安全的 HashMap 替代方案，JDK8 用 CAS + 分段锁（局部锁）替代 Hashtable 的全局锁，高并发下性能更优，支持高效的键值对存取；
> 2. **CopyOnWriteArrayList**：线程安全的 List 替代方案，读操作无锁，写操作复制底层数组后修改，适合读多写少场景（如配置列表）；
> 3. **CopyOnWriteArraySet**：基于 CopyOnWriteArrayList 实现，线程安全的 Set，同样适配读多写少；
> 4. **ConcurrentLinkedQueue/Deque**：无锁的并发队列 / 双端队列，基于 CAS 实现，适合高并发的队列操作（如生产者消费者模型）；
> 5. **BlockingQueue 系列**（如 ArrayBlockingQueue、LinkedBlockingQueue、SynchronousQueue）：阻塞队列，支持阻塞式的入队 / 出队，是线程池的核心底层容器；
> 6. **ConcurrentSkipListMap/Set**：线程安全的有序映射 / 集合，基于跳表实现，替代同步版的 TreeMap/TreeSet，支持高效的有序遍历和查找。
>
> 核心特点：这类容器通过 CAS、分段锁、写时复制等机制实现线程安全，相比 Hashtable、同步包装类（Collections.synchronizedXxx），并发性能更优，适配不同的并发场景（读多写少、高并发读写、阻塞操作等）。





6. sleep和wait方法的区别

sleep 和 wait 是 Java 中用于线程暂停的两个核心方法

1. 归属类不同

- sleep 是`java.lang.Thread`类的**静态方法**，直接通过`Thread.sleep(毫秒数)`调用，属于线程自身的行为；
- wait 是`java.lang.Object`类的**成员方法**，需通过对象锁（如`obj.wait()`）调用，依赖对象的监视器锁（monitor），所有对象都可调用。

2. 释放锁机制不同（核心区别）

- sleep：线程执行 sleep 时，释放cpu给其它线程，**不会释放持有的对象锁**，只是让线程进入` TIMED_WAITING `状态，休眠时间到后自动恢复就绪状态；比如同步代码块中调用 sleep，其他线程仍无法获取该锁；
- wait：线程执行 wait 时，**会立即释放持有的对象锁**，让线程进入 `WAITING/TIMED_WAITING` 状态，直到被唤醒或超时，重新竞争锁后才能继续执行；这是实现线程间协作的核心特性。

3. 唤醒方式不同

- sleep：无需手动唤醒，**到达设定的休眠时间后自动唤醒**；也可通过`interrupt()`中断 sleep，抛出`InterruptedException`；
- wait：分两种情况：① 无参`wait()`需通过其他线程调用该对象的`notify()`/`notifyAll()`手动唤醒；② 有参`wait(毫秒数)`可超时自动唤醒，也可被`notify()`/`notifyAll()`提前唤醒，同样可被`interrupt()`中断。

4. 使用场景不同

- sleep：适用于**单纯的线程延时执行**，比如定时任务,不涉及线程间通信；
- wait：适用于**线程间协作 / 通信**，比如生产者 - 消费者模型中，消费者线程无数据时调用 wait 释放锁，生产者生产数据后调用 notify 唤醒消费者，实现线程间的同步。

另外补充：wait 必须在同步代码块 / 同步方法中调用（持有对象锁时），否则抛`IllegalMonitorStateException`；sleep 无此限制



9. String buffer和String builder区别

- StringBuffer 和 StringBuilder 都是 Java 中用于处理可变字符串的类,替代不可变的 String，避免频繁创建字符串对象,其中的方法和功能完全是等价的，

- 只是StringBuffer 中的方法大都采用了 synchronized 关键字进行修饰，会通过加锁保证操作的原子性，因此是线程安全的，而 StringBuilder 没有这个修饰，可以被认为是非线程安全的，多线程同时操作时可能会出现字符串拼接错误，数据丢失等并发问题




Java如何实现线程间同步？

​	核心思想是：让多线程**在竞争共享资源时 “排队访问”**，而非全程单线程，仅在 “操作共享资源的关键步骤” 排队（如取钱的 “判断余额 - 扣减金额” 过程）；其他非共享操作（如打印日志、获取用户名）仍可并发执行，兼顾安全性与效率。核心方案是通过加锁来实现，为共享资源绑定一个 “锁对象”，线程需先 “抢到锁” 才能访问共享资源，访问结束后 “释放锁”，其他线程再竞争锁；

​	实现同步的方式

① 基于`synchronized`（对象锁 / 类锁）实现隐式同步，通过修饰方法或代码块保证同一时刻仅一个线程执行临界区代码；

- 实例同步方法：默认锁对象是`this`
- 静态同步方法：默认锁对象是`类名.class`

② 基于`Object`的`wait()`/`notify()`/`notifyAll()`等待 - 通知机制，配合`synchronized`实现线程间协作（如生产者 - 消费者模型）；

③ 基于 JUC 包的显式锁（`ReentrantLock`）需手动调用方法加锁 / 释放，支持更灵活的锁控制（如可中断、超时获取锁）；、`Semaphore`（信号量）等并发工具类实现复杂场景的线程同步，以及`ThreadLocal`通过线程隔离规避同步需求（线程 A 存入 ThreadLocal 的变量，线程 B 完全无法访问，线程间的变量操作互不干扰），`volatile`关键字保证变量可见性（轻量级同步，无互斥性）。





11. synchronized底层实现是什么 lock底层是什么 有什么区别


1. synchronized 底层实现

- **方法级同步**：通过方法常量池`method_info`结构中的`ACC_SYNCHRONIZED`访问标志实现。调用方法时，JVM 检查该标志，若设置则先获取 monitor（管程），执行方法后释放 monitor。
- **代码块同步**：依赖`monitorenter`和`monitorexit`字节码指令。执行`monitorenter`时，线程尝试获取 monitor 所有权（未加锁 / 已持有则计数器 + 1）；执行`monitorexit`时计数器 - 1，计数器为 0 则释放锁；获取失败则线程阻塞。

2. **Lock原理：**

Lock的存储结构：维护一个 volatile 的 state 变量（0 = 无锁，1 = 持有锁，>1 = 重入），一个双向链表（用于存储等待中的线程）

Lock获取锁的过程：本质上是通过CAS来获取状态值修改，如果当场没获取到，会将该线程放在线程等待链表中。

Lock释放锁的过程：修改状态值，调整等待链表,唤醒队列头节点，实现锁的传递

Lock大量使用CAS+自旋。因此lock建议使用在低锁冲突的情况下。

3. **Lock与synchronized的区别：**

Lock的加锁和解锁都是由java代码配合native方法（调用操作系统的相关方法）实现的，而synchronize的加锁和解锁的过程是由JVM管理的。

当一个线程使用synchronize获取锁时，若锁被其他线程占用着，那么当前只能被阻塞，直到成功获取锁。而Lock则提供超时锁和可中断等更加灵活的方式，在未能获取锁的  条件下提供一种退出的机制。

> CAS（Compare and Swap，比较并交换）是 Java 并发编程中实现无锁同步的核心原子操作，核心逻辑是**先比较内存中某个值（V）是否等于预期值（A），若相等则将其更新为新值（B），整个过程原子性完成，无需加锁**；若比较失败（说明值已被其他线程修改），则不执行更新，返回失败结果，通常需配合自旋（循环重试）直到成功。





15. 了解volatile关键字不
    volatile是Java提供的最轻量级的同步机制，仅能修饰变量（如成员变量、静态变量），保证了共享变量的可见性，被volatile关键字修饰的变量，如果值发生了变化，其他线程立刻可见，避免出现脏读现象。
    volatile禁止了指令重排，可以保证程序执行的有序性，但是由于禁止了指令重排，所以JVM相关的优化没了，效率会偏弱

> - JVM 为优化性能会对无依赖的指令重排（如`int a=1; int b=2;`可能重排为`b=2;a=1;`），多线程下可能导致逻辑异常；
> - volatile 通过在变量读写前后插入内存屏障（LoadLoad、StoreStore、LoadStore、StoreLoad），阻止指令重排，保证执行顺序与代码顺序一致



16. synchronized和volatile有什么区别

volatile本质是告诉JVM当前变量在寄存器中的值是不确定的，需要从主存中读取，synchronized则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住。

volatile仅能用在变量级别，而synchronized可以使用在变量、方法、类级别。

volatile仅能实现变量的修改可见性，不能保证原子性；而synchronized则可以保证变量的修改可见性和原子性。

volatile不会造成线程阻塞，synchronized可能会造成线程阻塞。

volatile标记的变量不会被编译器优化，synchronized标记的变量可以被编译器优化。





15. Java类加载过程

加载 加载时类加载的第一个过程，在这个阶段，将完成一下三件事情：
通过一个类的全限定名获取该类的二进制流。

将该二进制流中的静态存储结构转化为方法去运行时数据结构。 

在内存中生成该类的Class对象，作为该类的数据访问入口。

验证 验证的目的是为了确保Class文件的字节流中的信息不回危害到虚拟机.在该阶段主要完成以下四钟验证:
文件格式验证：验证字节流是否符合Class文件的规范，如主次版本号是否在当前虚拟机范围内，常量池中的常量是否有不被支持的类型.

元数据验证:对字节码描述的信息进行语义分析，如这个类是否有父类，是否集成了不被继承的类等。

字节码验证：是整个验证过程中最复杂的一个阶段，通过验证数据流和控制流的分析，确定程序语义是否正确，主要针对方法体的验证。如：方法中的类型转换是否正确，跳转指令是否正确等。

符号引用验证：这个动作在后面的解析过程中发生，主要是为了确保解析动作能正确执行。

准备
准备阶段是为类的静态变量分配内存并将其初始化为默认值，这些内存都将在方法区中进行分配。准备阶段不分配类中的实例变量的内存，实例变量将会在对象实例化时随着对象一起分配在Java堆中。

解析
该阶段主要完成符号引用到直接引用的转换动作。解析动作并不一定在初始化动作完成之前，也有可能在初始化之后。

初始化
初始化时类加载的最后一步，前面的类加载过程，除了在加载阶段用户应用程序可以通过自定义类加载器参与之外，其余动作完全由虚拟机主导和控制。到了初始化阶段，才真正开始执行类中定义的Java程序代码。

18. 什么是类加载器，类加载器有哪些  难度系数：⭐
    类加载器就是把类文件加载到虚拟机中，也就是说通过一个类的全限定名来获取描述该类的二进制字节流。

主要有以下四种类加载器
启动类加载器(Bootstrap ClassLoader)用来加载java核心类库，无法被java程序直接引用

扩展类加载器(extension class loader):它用来加载 Java 的扩展库。Java 虚拟机的实现会提供一个扩展库目录。该类加载器在此目录里面查找并加载 Java 类

系统类加载器（system class loader）也叫应用类加载器：它根据 Java 应用的类路径（CLASSPATH）来加载 Java 类。一般来说，Java 应用的类都是由它来完成加载的。可以通过 ClassLoader.getSystemClassLoader()来获取它

用户自定义类加载器，通过继承 java.lang.ClassLoader类的方式实现

什么时候会使用到加载器？java中的加载器是按需加载，什么时候用到，什么时候加载
new对象的时候
访问某个类或者接口的静态变量，或者对该静态变量赋值时
调用类的静态方法时
反射
初始化一个类的子类时，其父类首先会被加载
JVM启动时标明的启动类，也就是文件名和类名相同的那个类



20、如何查看java死锁     难度系数：⭐

####演示死锁

```JAVA
package com.ssg.mst;
public class 死锁 {
private static final String lock1 = "lock1";
private static final String lock2 = "lock2";
public static void main(String[] args) {
    Thread thread1 = new Thread(() -> {
        while (true) {
            synchronized (lock1) {
                try {
                    System.out.println(Thread.currentThread().getName() + lock1);
                    Thread.sleep(1000);
                    synchronized (lock2){
                        System.out.println(Thread.currentThread().getName() + lock2);
                    }
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    });
 
    Thread thread2 = new Thread(() -> {
        while (true) {
            synchronized (lock2) {
                try {
                    System.out.println(Thread.currentThread().getName() + lock2);
                    Thread.sleep(1000);
                    synchronized (lock1){
                        System.out.println(Thread.currentThread().getName() + lock1);
                    }
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    });
 
    thread1.start();
    thread2.start();
}
```
}

程序运行，进程没有停止。


通过jps查看java进程，找到没有停止的进程

通过jstack 9060 查看进程具体执行信息





21. Java死锁如何避免     难度系数：⭐

    造成死锁的几个原因

1.一个资源每次只能被一个线程使用

2.一个线程在阻塞等待某个资源时，不释放已占有资源

3.一个线程已经获得的资源，在未使用完之前，不能被强行剥夺

4.若干线程形成头尾相接的循环等待资源关系

这是造成死锁必须要达到的4个条件，如果要避免死锁，只需要不满足其中某一个条件即可。而其中前3个条件是作为锁要符合的条件，所以要避免死锁就需要打破第4个条件，不出现循环等待锁的关系。

在开发过程中

1.要注意加锁顺序，保证每个线程按同样的顺序进行加锁

2.要注意加锁时限，可以针对锁设置一个超时时间

3.要注意死锁检查，这是一种预防机制，确保在第一时间发现死锁并进行解决



ThreadLocal 是 Java 中用于实现**线程本地存储**的**工具类**，核心作用是为每个使用它的线程创建一个独立的变量副本，让每个线程只能访问自己的副本，不会和其他线程的副本产生冲突，以此实现线程间的数据隔离，避免多线程共享变量的线程安全问题。

它的本质是通过一个内部的 Map（ThreadLocalMap），以线程为 Key，存储该线程专属的变量副本：当线程调用 ThreadLocal 的`set()`方法时，变量会被存入当前线程的专属副本；调用`get()`时，只会获取当前线程的副本；线程销毁时，对应的副本也会被回收，不会产生内存泄漏（正确使用时）。

常用场景是存储线程专属的上下文信息，比如 Web 请求中的用户登录信息、数据库连接（避免多线程共享连接导致的事务混乱），或者工具类中的线程专属状态，既无需加锁保证线程安全，又比手动维护线程专属变量更简洁。