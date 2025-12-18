# Java加强

## 第一章 多线程

### 第一节 创建线程

 **一、多线程基础概念**

1. 线程（Thread）

- **定义**：程序内部的一条**执行流程**，Java 中用`java.lang.Thread`类的对象来代表线程。
- **单线程程序**：只有一条执行流程的程序（如仅包含`main`方法的基础程序），代码按顺序依次执行。

2. 多线程

- **定义**：从软硬件层面实现 “多条执行流程同时运行” 的技术，多条线程由**CPU 调度执行**，CPU 作为计算机 “中央处理器”，负责分配资源并控制线程运行。
- 核心价值：解决 “并发处理” 需求，是大型系统、高并发场景的底层核心技术，典型应用场景包括：
  - 12306 购票系统：同时处理数万用户的购票请求（高并发）；
  - 百度网盘：同时进行 “上传文件” 和 “下载文件”（多任务并行）；

**二、多线程创建方式一：继承 Thread 类**

1. Java 官方定义的第一种线程创建方案，核心是 “通过继承`Thread`类，重写任务方法，创建对象并启动”。

| 步骤 |          操作说明           |                           关键逻辑                           |
| :--: | :-------------------------: | :----------------------------------------------------------: |
|  1   |  定义子类，继承`Thread`类   | 让自定义类具备 “线程特性”（`Thread`类是 Java 中线程的 “父类模板”） |
|  2   | 重写`Thread`类的`run()`方法 | `run()`方法是 “线程任务的载体”，线程要执行的逻辑需写在该方法内 |
|  3   |        创建子类对象         |       类本身不代表具体线程，**子类对象才是线程的实例**       |
|  4   | 调用子类对象的`start()`方法 | 启动线程（向 CPU 注册线程，CPU 调度后会自动执行`run()`方法中的任务） |

```java
package com.itheima.demo;

// 步骤1：定义线程子类，继承Thread
class MyThread extends Thread {
    // 步骤2：重写run()方法，编写线程任务
    @Override
    public void run() {
        // 线程要执行的逻辑（示例：打印5次“子线程输出”）
        for (int i = 0; i < 5; i++) {
            System.out.println("子线程输出：" + i);
        }
    }
}

public class TestThread {
    public static void main(String[] args) {
        // 步骤3：创建线程子类对象（代表一个具体线程）
        Thread t1 = new MyThread();
        // 步骤4：调用start()方法启动线程（CPU调度后自动执行run()）
        t1.start();

        // 主线程任务（用于对比“多线程并发效果”）
        for (int i = 0; i < 5; i++) {
            System.out.println("主线程输出：" + i);
        }
    }
}
```

2. **执行效果特点**

- **并发执行**：主线程（`main`方法所在线程）与子线程（`t1`）同时运行，输出结果会 “交替出现”（如 “子线程 0→主线程 0→子线程 1→子线程 2→主线程 1”）；
- **随机性**：每次运行的输出顺序可能不同（CPU 调度线程的顺序是不确定的，属于 “抢占式调度”）。

3. **关键注意事项**

- - 


- 主线程任务需放在`start()`之后（保证并发效果）
  - 若将主线程任务（如上述`main`方法中的循环）放在`t1.start()`之前，主线程会先执行完自身任务，再启动子线程，无法体现 “多线程并发”（相当于单线程执行）。


4. 该创建方式的优缺点

- 优点：编码简单：直接继承`Thread`类，无需额外处理接口或其他类关系 

- 缺点：受限于 “单继承”：Java 中类只能继承一个父类，若自定义类已继承其他类（如`Student`），则无法再继承`Thread`，不利于功能扩展 

**三、多线程创建方式二：实现Runnable接口**

1. 实现`Runnable`接口是 Java 创建多线程的主流方式之一，核心是 “**先定义线程任务，再包装为线程对象**”，共 5 个步骤：

- **定义线程任务类**：创建一个类，实现`java.lang.Runnable`接口（该接口仅含一个抽象方法`run()`，属于函数式接口）。

- **重写`run()`方法**：在任务类中重写`Runnable`的`run()`方法，将线程要执行的逻辑写在`run()`中（即 “线程任务”）。
  - 注意：`run()`是线程的 “任务体”，直接调用`run()`不会启动新线程，仅会以 “主线程普通方法” 的形式执行

- **创建线程任务对象**：实例化自定义的`Runnable`实现类，得到 “线程任务对象”。
  - 注意：该对象**不是线程对象**，仅封装了线程要执行的任务，无法调用`start()`方法启动线程。

- **包装为线程对象**：创建`java.lang.Thread`类的对象，通过`Thread`的构造器（`Thread(Runnable target)`）将 “任务对象” 传入，完成 “任务→线程” 的包装。

- **启动线程**：调用`Thread`对象的`start()`方法，JVM 会自动创建新线程，并在新线程中执行`Runnable`任务的`run()`方法。

```java
public class demo_2 {
    public static void main(String[] args) {
        //3.创建一个线程任务类对象
        Runnable r=new MyRunnable();
        //4.把线程任务类对象传递给线程对象
        Thread t1=new Thread(r);  //构造器
        //5.启动线程
        t1.start();
    }
}

//1.定义一个线程任务类实现Runnable接口
class MyRunnable implements Runnable{
    //2.重写run()方法
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("bbb"+i);
        }
    }
}

```



| 类别     | 具体说明                                                     |
| -------- | ------------------------------------------------------------ |
| **优点** | 1. **扩展性强**：任务类仅实现接口，不影响继承其他类，也可同时实现其他接口（如`Closeable`），功能不受阉割。<br>2. |
| **缺点** | 1. **多创建一个对象**：需额外创建 “任务对象”，比 “继承 Thread” 多一步对象实例化（仅轻微影响性能，通常可忽略）。<br>2. **无法直接返回结果**：`run()`方法返回值为`void`，线程执行完毕后无法直接返回结果（需后续通过 “线程池 + Callable” 或 “Future” 解决）。 |

2. 简化写法（匿名内部类与 Lambda）

- 匿名内部类简化

利用 “接口可直接创建匿名内部类对象” 的特性，省略单独的`Runnable`实现类，直接在创建`Thread`时传入任务：

```java
// 匿名内部类实现Runnable，直接作为Thread构造器参数
Thread t1 = new Thread(new Runnable() {
    @Override
    public void run() {
        for (int i = 0; i <= 5; i++) {
            System.out.println("子线程1输出：" + i);
        }
    }
});
t1.start(); // 启动线程
```

- Lambda 表达式进一步简化

由于`Runnable`是**函数式接口**（仅一个抽象方法），可通过 Lambda 表达式省略匿名内部类的冗余语法（`new Runnable()`、`@Override`等），代码更简洁：

```java
public class demo_2 {
    public static void main(String[] args) {
        new Thread(new Runnable(){
            @Override
            public void run() {
                for(int i=0;i<100;i++){
                    System.out.println("线程1："+i);
                }
            }
        }).start();
    }
}

//进一步简化
public class demo_2 {
    public static void main(String[] args) {
        new Thread(()->{
            for(int i=0;i<100;i++){
                System.out.println("线程1："+i);
            }
        }).start();
    }
}
```

四、多线程创建方式三：实现Callable接口

1. 前两种线程创建方式的局限性

Java 多线程前两种创建方式（继承`Thread`类、实现`Runnable`接口）返回值为`void`，**无法直接返回线程执行结果**，若通过 “静态变量存储结果” 间接获取，主线程无法确定子线程是否执行完毕，可能导致 “结果未计算完就读取” 的 bug

2. 第三种创建方式：实现 Callable 接口 + FutureTask 类

|      组件      |                             作用                             |
| :------------: | :----------------------------------------------------------: |
| `Callable`接口 | **泛型接口**，定义线程任务逻辑，核心方法`call()`可返回指定类型结果（需声明泛型，如`Callable<Integer>`），且支持抛出异常。 |
| `FutureTask`类 | 1. **包装任务**：将`Callable`实现类对象包装为 “可被线程执行的任务对象”（本质是`Runnable`接口实现类，可传给`Thread`）；<br>2. **获取结果**：提供`get()`方法，在子线程执行完毕后获取`call()`的返回结果，且会自动阻塞主线程，直到结果返回（避免 “结果未就绪” 问题）。 |

3. 实现步骤

步骤 1：定义 Callable 实现类，重写call方法，封装要做的事和要返回的数据

```java
// 泛型指定返回结果类型（如String）
class MyCallable implements Callable<String> {
    private int n; // 任务参数（如求和的边界值）

    // 构造器传入参数（灵活控制任务逻辑，避免硬编码）
    public MyCallable(int n) {
        this.n = n;
    }

    // 核心方法：定义线程任务，返回结果
    @Override
    public String call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= n; i++) {
            sum += i;
        }
        // 返回结果（如“1~n的和为：sum”）
        return "子线程计算1~" + n + "的和为：" + sum;
    }
}
```

 步骤 2：创建 Callable 对象，用 FutureTask （线程任务对象）包装

```java
// 1. 创建Callable任务对象（传入参数，如求和1~100）
Callable<String> callable = new MyCallable(100);
// 2. 用FutureTask包装，得到“可执行+可获取结果”的任务对象
FutureTask<String> futureTask = new FutureTask<>(callable);
```

 步骤 3：创建 Thread 对象，启动线程

```java
// 将FutureTask传给Thread（因FutureTask实现Runnable，符合Thread构造器参数要求）
Thread thread = new Thread(futureTask);
// 启动线程（底层会执行Callable的call()方法）
thread.start();
```

 步骤 4：用 FutureTask 的 get () 方法，获取线程执行结果

```java
try {
    // get()方法会阻塞主线程，直到子线程执行完毕并返回结果
    String result = futureTask.get();
    System.out.println("线程执行结果：" + result); // 输出：1~100的和为：5050
} catch (InterruptedException e) {
    e.printStackTrace(); // 线程中断异常
} catch (ExecutionException e) {
    e.printStackTrace(); // call()方法抛出的异常（需捕获处理）
}
```

**优点**

- **可返回结果**：通过`get()`方法安全获取线程执行结果，无需手动处理线程同步等待。
- **扩展性强**：`Callable`是接口，实现类可继续继承其他类、实现其他接口（无 “单继承限制”，优于 “继承 Thread 类” 的方式）。
- **支持异常处理**：`call()`方法可抛出异常，主线程通过`get()`的`ExecutionException`捕获，便于问题排查。

**缺点**

- **编码复杂**：相比前两种方式，需额外创建`FutureTask`对象，步骤更多（需理解 “包装 - 执行 - 获取结果” 的流程）。

4. 创建方式的对比

|       方式       |                             优点                             |         缺点         |
| :--------------: | :----------------------------------------------------------: | :------------------: |
|   继承Thread类   |                 可以直接使用Thread类中的方法                 |       扩展性差       |
| 实现Runnable接口 |           扩展性强，实现接口的同时还可以继承其他类           | 不能返回线程执行结果 |
| 实现Callable接口 | 扩展性强，实现接口的同时还可以继承其他类，且可以得到线程执行的结果 |     编程相对复杂     |



### 第二节 线程的常用方法

一、线程标识与状态相关方法

用于获取线程唯一标识、名称，判断线程状态，是线程管理的基础操作。

|          方法签名           |       功能说明        |                           关键细节                           |
| :-------------------------: | :-------------------: | :----------------------------------------------------------: |
|       `long getId()`        | 获取当前线程的唯一 ID |       由 JVM 自动分配，整个程序运行期间唯一，不可修改        |
|     `String getName()`      |  获取当前线程的名称   | 默认名称格式为 “Thread - 序号”（如 Thread-0），可通过构造器自定义 |
| `void setName(String name)` |    为线程设置名称     | 建议在线程启动前设置，便于日志排查（如区分 “用户登录线程”“数据同步线程”） |
|  `Thread.State getState()`  |   获取线程当前状态    | 返回`Thread.State`枚举值，包含 6 种核心状态：- `NEW`（新建未启动）- `RUNNABLE`（运行中 / 就绪）- `BLOCKED`（阻塞，等待锁）- `WAITING`（无限等待，需被唤醒）- `TIMED_WAITING`（计时等待，如`sleep`后自动唤醒）- `TERMINATED`（已终止） |
|     `boolean isAlive()`     |   判断线程是否存活    | 存活范围：从`start()`启动后到`run()`执行结束前，返回`true`；新建（NEW）或已终止（TERMINATED）返回`false` |

二、线程调度与优先级方法

用于控制线程的执行优先级和调度顺序，影响 CPU 资源分配（需结合操作系统调度机制）。

1. 获取当前执行的线程对象

- `public static Thread currentThread()`，谁调用她，就返回哪个对象

2. **线程优先级方法**

- `int getPriority()`：获取线程优先级，默认优先级为`5`。
- `void setPriority(int newPriority)`：设置线程优先级，范围为`1~10`（超出范围抛`IllegalArgumentException`）。
- 优先级规则：
  - 优先级仅为 “调度建议”，不保证 CPU 一定优先执行高优先级线程（依赖 OS 调度算法，如 Windows 是 “抢占式优先级调度”）；
  - 线程继承父线程优先级（如主线程优先级为 5，其子线程默认也为 5）；
  - 推荐使用默认优先级（避免因优先级过高导致低优先级线程 “饥饿”，即长期无法获取 CPU）。

3. **线程调度核心方法**

- `static void yield()`（静态方法，当前线程调用）：让当前线程主动放弃 CPU 使用权，从 “运行态” 回到 “就绪态”，重新参与 CPU 竞争（注意：不是进入阻塞态，且不保证其他线程能抢到 CPU，可能自己再次被调度）。

- `void join()/void join(long millis)`（其他线程调用）：让 “调用该方法的线程” 优先执行，当前线程进入` “WAITING/TIMED_WAITING” `状态，直到目标线程执行结束（或等待超时）后，当前线程才恢复就绪态。

  示例：主线程中调用`threadA.join()`，则主线程会等待 threadA 执行完，再继续执行自己的代码，常用于 “主线程等待子线程完成数据计算后再汇总结果” 的场景。

三、线程休眠与等待方法

用于控制线程的执行时机，实现 “延迟执行” 或 “线程间同步等待”。

1. **`static void sleep(long millis)`（静态方法，当前线程调用）**

   - 功能：让当前线程从 “运行态” 进入 “TIMED_WAITING” 状态，休眠指定毫秒数（`millis`），时间到后自动唤醒并回到 “就绪态”。
   - 关键细节：
     - 休眠期间**不会释放已持有的锁**（如同步代码块中的`sleep`，其他线程仍无法进入同步块）；
     - 必须处理`InterruptedException`（受检异常）：若线程在休眠时被其他线程中断（调用`interrupt()`），会抛出该异常并提前唤醒；
     - 常用场景：模拟延迟（如定时任务、界面加载等待提示）、控制线程执行频率（如每秒打印一次日志）。

2. **`void wait()` / `void wait(long timeout)`（锁对象调用，需在同步块中使用）**

   - 功能：让当前线程从 “运行态” 进入 “WAITING/TIMED_WAITING” 状态，同时**释放持有的锁**，等待其他线程调用同一锁对象的`notify()`/`notifyAll()`唤醒。

   - 关键细节（与`sleep`对比）：

     | 对比维度 | `sleep(long millis)`         | `wait()`/`wait(long timeout)`                          |
     | -------- | ---------------------------- | ------------------------------------------------------ |
     | 调用对象 | 线程对象（`Thread`）         | 锁对象（如`Object`、`ReentrantLock`关联的`Condition`） |
     | 锁释放   | 不释放锁                     | 释放锁                                                 |
     | 使用场景 | 线程自身延迟                 | 线程间同步（如生产者 - 消费者模型）                    |
     | 异常处理 | 需处理`InterruptedException` | 需处理`InterruptedException`                           |

四、线程相关静态工具方法

- `static Thread currentThread()`：获取 “当前正在执行的线程” 对象，常用于非线程类中操作当前线程（如主线程中获取自身引用、线程池任务中判断当前线程）。

  ```java
  Thread current = Thread.currentThread(); System.out.println("当前线程名：" + current.getName());
  ```

- `static void dumpStack()`：打印当前线程的调用栈信息，用于调试（如定位线程阻塞时的代码执行路径）。



### 第三节 线程安全

一、定义

当**多个线程同时操作同一个共享资源**，且操作涉及对共享资源的**修改**时，可能导致业务逻辑异常（如数据错误、逻辑漏洞），这种异常称为**线程安全问题**。

二、线程安全问题的发生条件

必须同时满足以下 3 个条件，才会触发线程安全问题

- **多线程并发执行**：至少 2 个线程同时运行；
- **操作同一个共享资源**：线程间共享的数据 / 对象；
- **对共享资源进行修改**：操作涉及 “写” 行为（如账户余额的增减），而非仅 “读”。



### 第四节 线程同步

 一、核心思想

1. 线程同步的解决思路

让多线程**在竞争共享资源时 “排队访问”**，而非全程单线程：

- 仅在 “操作共享资源的关键步骤” 排队（如取钱的 “判断余额 - 扣减金额” 过程）；
- 其他非共享操作（如打印日志、获取用户名）仍可并发执行，兼顾安全性与效率。

2. 核心方案：加锁机制

- 原理：为共享资源绑定一个 “锁对象”，线程需先 “抢到锁” 才能访问共享资源，访问结束后 “释放锁”，其他线程再竞争锁；
- 要求：同一批竞争共享资源的线程，必须使用**同一个锁对象**（否则锁无效）。

 二、线程同步的三种实现方式

 （一）方式 1：同步代码块（`synchronized`关键字）

1. 作用：将 “访问共享资源的核心代码” 用`synchronized`包裹，明确指定锁对象，格式如下：

```java
synchronized (同步锁) {
    // 核心代码：操作共享资源的逻辑（如判断余额、扣减金额）
}
```

2. 关键规则

- **锁对象必须唯一**：同一批竞争共享资源的线程，必须使用同一个锁对象（否则各线程各持一把锁，无法排队）；

- 锁对象的选择建议

  | 方法类型 | 推荐锁对象                 | 原因                                                         |
  | -------- | -------------------------- | ------------------------------------------------------------ |
  | 实例方法 | `this`                     | `this`代表当前对象（如具体的银行账户），同一账户的线程共享同一个`this`，仅锁当前账户； |
  | 静态方法 | `类名.class`（字节码对象） | 静态方法属于 “类级别的共享”，`类名.class`是全局唯一的字节码对象，可锁所有访问该静态方法的线程； |

- 避免用 “任意唯一对象”（如`"lock"`字符串）：若多个不相关的共享资源（如 A 账户、B 账户）都用同一个字符串锁，会导致 “A 账户的线程排队时，B 账户的线程也被迫等待”，锁范围过大，性能浪费。

3. 原理示例（取钱案例）

```java
public class Account {
    private double balance; // 共享资源：账户余额

    // 取钱方法（实例方法）
    public void withdraw(double money) {
        // 同步代码块：锁对象为this（当前Account实例）
        synchronized (this) {
            if (balance >= money) { // 核心逻辑1：判断余额
                System.out.println(Thread.currentThread().getName() + "取钱成功，金额：" + money);
                balance -= money; // 核心逻辑2：扣减余额
                System.out.println("当前账户余额：" + balance);
            } else {
                System.out.println(Thread.currentThread().getName() + "取钱失败，余额不足");
            }
        }
    }
}
```

（二）方式 2：同步方法（`synchronized`修饰方法）

1. 作用：直接在 “操作共享资源的方法” 上添加`synchronized`关键字，无需显式写锁对象（锁对象由 Java 默认指定）：

```java
// 实例方法的同步方法
public synchronized void 方法名(参数) {
    // 核心代码：操作共享资源的逻辑
}

// 静态方法的同步方法
public static synchronized void 方法名(参数) {
    // 核心代码：操作共享资源的逻辑
}
```

2. 关键规则

- 默认锁对象（与同步代码块的锁一致）：
  - 实例同步方法：默认锁对象是`this`
  - 静态同步方法：默认锁对象是`类名.class`
- **锁范围**：整个方法体（比同步代码块的锁范围更大，若方法内包含非共享操作，会浪费并发效率）。

3. 原理示例（取钱案例）

```java
public class Account {
    private double balance;

    // 同步方法：默认锁对象为this（实例方法）
    public synchronized void withdraw(double money) {
        if (balance >= money) {
            System.out.println(Thread.currentThread().getName() + "取钱成功，金额：" + money);
            balance -= money;
            System.out.println("当前账户余额：" + balance);
        } else {
            System.out.println(Thread.currentThread().getName() + "取钱失败，余额不足");
        }
    }
}
```

4. 同步代码块 vs 同步方法

|   维度   |               同步代码块               |                 同步方法                 |
| :------: | :------------------------------------: | :--------------------------------------: |
|  锁范围  |        仅包裹核心代码（范围小）        |           整个方法体（范围大）           |
|  灵活性  |     可自定义锁范围，兼顾安全与效率     | 锁范围固定，若方法内有非共享操作，效率低 |
|  可读性  |    需找`synchronized`块，可读性稍差    |     方法名带`synchronized`，一眼识别     |
| 适用场景 | 方法内包含非共享操作，需精准锁核心逻辑 |       方法体全是核心逻辑，无需拆分       |

（三）方式 3：Lock 锁（JDK5 + 新增，`java.util.concurrent.locks`包）

1. 定义与优势

- `Lock`是**接口**，需用实现类`ReentrantLock`创建锁对象；
- 相比`synchronized`（隐式锁，自动加锁 / 释放），`Lock`是**显式锁**，需手动调用方法加锁 / 释放，灵活性更高（支持尝试获取锁、超时放弃锁等）。

2. 核心方法

| 方法名      | 作用                                                         |
| ----------- | ------------------------------------------------------------ |
| `lock()`    | 加锁：线程获取锁，若锁已被占用，则阻塞等待；                 |
| `unlock()`  | 解锁：释放锁，必须在 “最终确保执行” 的场景下调用（如`finally`块），避免死锁； |
| `tryLock()` | 尝试获取锁：若获取成功返回`true`，失败返回`false`，不阻塞（可选）； |

3. 关键规则

- 锁对象需用`final`修饰：防止锁对象被意外修改（如赋值为`null`），导致锁失效；
- 解锁必须放在`finally`块中：无论核心代码是否抛出异常，都能确保锁释放，避免 “锁被占用后无法释放” 导致死锁。

4. 原理示例（取钱案例）

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Account {
    private double balance;
    // 1. 创建Lock锁对象，用final修饰防止篡改
    private final Lock lock = new ReentrantLock();

    public void withdraw(double money) {
        // 2. 手动加锁
        lock.lock();
        try {
            // 3. 核心代码：操作共享资源
            if (balance >= money) {
                System.out.println(Thread.currentThread().getName() + "取钱成功，金额：" + money);
                balance -= money;
                System.out.println("当前账户余额：" + balance);
            } else {
                System.out.println(Thread.currentThread().getName() + "取钱失败，余额不足");
            }
        } finally {
            // 4. 手动解锁（必须放在finally，确保锁释放）
            lock.unlock();
        }
    }
}
```

三、核心对比与总结

|         同步方式         | 锁类型 |      加锁 / 释放方式      |       锁对象指定       |           灵活性           |                适用场景                |
| :----------------------: | :----: | :-----------------------: | :--------------------: | :------------------------: | :------------------------------------: |
|        同步代码块        | 隐式锁 |    自动（代码块进出）     |        显式指定        |        高（精准锁）        |    方法内有非共享操作，需缩小锁范围    |
|         同步方法         | 隐式锁 |     自动（方法进出）      | 隐式（this / 类对象）  |      低（锁整个方法）      |      方法体全是核心逻辑，无需拆分      |
| Lock 锁（ReentrantLock） | 显式锁 | 手动（lock ()/unlock ()） | 显式创建（final 修饰） | 极高（支持尝试锁、超时等） | 需灵活控制锁（如尝试获取锁、中断等待） |



### 第五节 线程池

一、概念

1. 定义：线程池是**复用线程的技术**，通过预先创建固定数量的线程，重复利用它们处理多个任务，避免频繁创建 / 销毁线程影响系统性能。

2. 为什么需要线程池

- **内存溢出风险**：若每个任务创建一个线程（如淘宝 1 亿用户请求创建 1 亿线程），大量线程对象会耗尽服务器内存。
- **CPU 过载**：CPU 无法同时调度海量线程（普通电脑通常仅支持数千线程），会导致系统卡顿甚至宕机。
- **开销高昂**：线程创建 / 销毁需与 CPU 交互，频繁操作会浪费资源，降低系统性能。

二、线程池的工作原理

1. **任务流程**：新任务提交 → 先交给核心线程处理 → 核心线程忙则进入**任务队列排队** → 队列满则创建**临时线程**处理 → 临时线程也满则触发**任务拒绝策略**。
2. 核心角色
   - **工作线程**：核心线程（长期存活）+ 临时线程（空闲时销毁）。
   - **任务队列**：存储待处理的`Runnable`或`Callable`任务（如数组队列、链表队列）。

三、线程池的创建方式

![image-20251031145601298](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251031145601298.png)

Java 中线程池的核心接口是`ExecutorService`（接口，无法直接实例化），主要通过两种方式创建：

**方式 1：通过实现类`ThreadPoolExecutor`创建**

需通过**7 个核心参数**的构造器创建，参数含义及类比如下：

| 参数名                                 | 含义                                              |         参数类型         |
| -------------------------------------- | ------------------------------------------------- | :----------------------: |
| `corePoolSize` 核心线程数量            | 线程池长期维持的线程数，即使空闲也不销毁          |           int            |
| `maximumPoolSize`最大线程数量          | 核心线程 + 临时线程的总数                         |           int            |
| `keepAliveTime` 临时线程的空闲存活时间 | 空闲超过该时间则销毁临时线程                      |           long           |
| `unit`                                 | `keepAliveTime`的时间单位（如`TimeUnit.SECONDS`） |         TimeUnit         |
| `workQueue`任务队列                    | 核心线程忙时，任务在此排队                        | BlockingQueue<Runnable>  |
| `threadFactory` 线程工厂               | 负责创建线程，可自定义线程名称等                  |      ThreadFactory       |
| `handler `任务拒绝策略                 | 线程全忙 + 队列满时，如何处理新任务               | RejectedExecutionHandler |

```java
// 创建ThreadPoolExecutor实例
ExecutorService pool = new ThreadPoolExecutor(
    3,                          // 核心线程数
    5,                          // 最大线程数
    10,                         // 临时线程空闲时间（秒）
    TimeUnit.SECONDS,           // 时间单位
    new ArrayBlockingQueue<>(3),// 任务队列（容量3）
    Executors.defaultThreadFactory(), // 默认线程工厂
    new ThreadPoolExecutor.AbortPolicy() // 拒绝策略（抛异常）
);
```

**方式 2：通过工具类`Executors`创建**

Executors 是 Java 提供的线程池工具类，通过**静态方法**直接返回预设特性的线程池对象（返回类型多为`ExecutorService`或其子类），无需手动配置所有参数，但存在特定场景风险。

1. 核心静态方法及线程池特性

|                        方法名                        |                          线程池特性                          | 适用场景                                                     |
| :--------------------------------------------------: | :----------------------------------------------------------: | ------------------------------------------------------------ |
|     `Executors.newFixedThreadPool(int nThreads)`     | 固定线程数量（核心线程数 = 最大线程数 = nThreads<br>无临时线程（临时线程存活时间 = 0)<br>任务队列无界（基于链表，可无限排队） | 任务数量稳定、需控制线程资源的场景（如常规业务处理）         |
|        `Executors.newSingleThreadExecutor()`         | 仅 1 个核心线程（核心线程数 = 最大线程数 = 1）<br>线程异常时自动创建新线程补充<br>任务队列无界 | 需单线程顺序执行任务，且需保证线程 "故障恢复" 的场景（如日志写入） |
|          `Executors.newCachedThreadPool()`           | - 线程数量动态伸缩（核心线程数 = 0，最大线程数 = Integer.MAX_VALUE）- 临时线程空闲 60 秒后自动回收- 任务队列无界 | 短期、突发任务较多，且任务执行时间短的场景（如临时数据处理） |
| `Executors.newScheduledThreadPool(int corePoolSize)` | - 返回`ScheduledExecutorService`子类- 支持定时任务（如延迟执行、周期性执行）- 核心线程数固定，临时线程无界 | 需要定时器 / 延时器的场景（如定时同步数据、周期性任务调度）  |

2. 底层原理

所有 Executors 方法的底层，均通过`ThreadPoolExecutor`（线程池实现类）的**7 个参数构造器**封装创建，本质是对 "自定义线程池" 的简化包装。例如：

- `newFixedThreadPool(3)`底层：`new ThreadPoolExecutor(3, 3, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>())`
- `newSingleThreadExecutor()`底层：`new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>())`

3. 核心风险（阿里巴巴开发手册规范）

Executors 工具类创建的线程池在**大型并发系统**中存在资源耗尽风险，不建议使用，原因如下：

- **无界任务队列风险**：`newFixedThreadPool`和`newSingleThreadExecutor`的任务队列无界，若任务持续堆积（如 1 亿个任务），会导致 JVM 内存溢出（OOM）。
- **无界线程数量风险**：`newCachedThreadPool`和`newScheduledThreadPool`的最大线程数为`Integer.MAX_VALUE`，若任务突发激增，会创建大量线程，导致 CPU 资源耗尽或内存溢出。

**规范建议**：直接使用`ThreadPoolExecutor`手动配置 7 个参数，明确线程池运行规则，规避资源风险。

4. 企业级应用

- 线程池的核心参数（核心线程数、最大线程数）需根据**业务类型（计算密集型 / IO 密集型）** 和**CPU 核数**配置，根据相应公式来配置

 四、线程池处理任务（Runnable 与 Callable）

线程池支持处理两种任务，处理方式不同：

1. 处理`Runnable`任务（无返回结果）

- 调用`ExecutorService`的`execute(Runnable task)`方法提交任务。
- 特点：无返回结果，无法获取任务执行结果。
- 核心验证：提交 5 个任务，仅 3 个核心线程执行，任务 4、5 进入队列，线程复用明显（如 2 号线程执行多个任务）。

```java
// 1. 定义Runnable任务
class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i <= 4; i++) {
            System.out.println(Thread.currentThread().getName() + ":" + i);
        }
    }
}

// 2. 线程池提交任务
public class ExecutorServiceDemo_1 {
    public static void main(String[] args) {
        ExecutorService pool=new ThreadPoolExecutor(
                3,
                5,
                10,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());

        Runnable target=new MyRunnable();
        pool.execute(target);
        pool.execute(target);
        pool.execute(target);

        pool.shutdown();
        //等所有任务执行完毕后关闭线程池
        //实际应用中线程池不关闭
    }
}

```

2. 处理`Callable`任务（有返回结果）

- 调用`ExecutorService`的`submit(Callable<T> task)`方法提交任务，返回`Future<T>`对象。
- 特点：有返回结果，通过`Future.get()`方法获取结果（会阻塞，直到任务执行完成）。
- 核心验证：提交 4 个求和任务（1~100、1~200 等），线程池复用核心线程（如线程 3 执行 1~300 和 1~400 任务），并能获取每个任务的求和结果。

 代码示例

```java
// 1. 定义Callable任务（泛型指定返回值类型）
class MyCallable implements Callable<String> {
    private int num;
    public MyCallable(int num) { this.num = num; }

    @Override
    public String call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= num; i++) {
            sum += i;
        }
        // 返回线程名+求和结果
        return Thread.currentThread().getName() + "：1~" + num + "求和=" + sum;
    }
}

// 2. 提交任务并获取结果
Future<String> f1 = pool.submit(new MyCallable(100)); // 1~100求和
Future<String> f2 = pool.submit(new MyCallable(200)); // 1~200求和
System.out.println(f1.get()); // 获取结果（阻塞）
System.out.println(f2.get());
```

 五、补充

1. 临时线程的创建时机（面试高频）

**必须同时满足 3 个条件**：

- 新任务提交时，核心线程全部在忙；
- 任务队列已满；
- 当前线程数 < 最大线程数（`maximumPoolSize`）。



2. 任务拒绝策略（4 种常见策略）

当 “核心线程全忙 + 临时线程全忙 + 任务队列满” 时，触发拒绝策略，`ThreadPoolExecutor`提供 4 种默认策略：

|        策略类         |                          行为描述                          |         场景建议         |
| :-------------------: | :--------------------------------------------------------: | :----------------------: |
| `AbortPolicy`（默认） |   直接丢弃新任务，并抛出`RejectedExecutionException`异常   | 生产环境（明确感知错误） |
|    `DiscardPolicy`    |      直接丢弃新任务，不抛异常（无任何提示，无法感知）      |          不推荐          |
| `DiscardOldestPolicy` |       丢弃任务队列中等待最久的任务，将新任务加入队列       |  特殊场景（如实时任务）  |
|  `CallerRunsPolicy`   | 由提交任务的**主线程**亲自执行新任务（“老板亲自接待客人”） |  轻量级任务（避免丢弃）  |

3. 线程池的关闭方法

- `shutdown()`：**温和关闭** → 不再接收新任务，但会执行完已提交的任务（包括队列中的任务）。
- `shutdownNow()`：**强制关闭** → 立即终止所有线程，未执行的任务直接丢弃（风险高，不推荐）。

> 注意：生产环境中线程池通常不关闭，需长期为系统提供服务。

### 第六节 并发、并行

1. 基础概念

- **进程**：正在运行的程序，是操作系统资源分配的基本单位（如 IDEA、浏览器均为独立进程），每个进程包含多个线程。
- **线程**：线程属于进程，是进程内的执行单元，是 CPU 调度的基本单位，线程共享进程的内存资源（如堆内存）。

2. 并发（Concurrency）

- **定义**：线程由CPU负责调度，CPU 通过**分时轮询**调度多个线程，同一时间点仅 1 个线程在 CPU 上执行，但由于 CPU 切换速度极快（纳秒级），从宏观上看多个线程 "同时执行"。
- **场景**：线程数 > CPU 逻辑核数时（如 6800 个线程在 16 核 CPU 上运行），CPU 会轮流为每个线程分配时间片，保证所有线程逐步推进。

3. 并行（Parallelism）

- **定义**：CPU 逻辑核数 ≥ 线程数时，多个线程在**同一时间点**分别在不同 CPU 核上执行，是 "真正的同时执行"。
- **场景**：线程数 ≤ CPU 逻辑核数时（如 10 个线程在 16 核 CPU 上运行），10 个线程分别在 10 个 CPU 核上并行执行，无切换开销。

4. 多线程的实际执行逻辑

在实际应用中，多线程是**并发与并行的结合**（会动态的竞争CPU）：

- 若总线程数为 6800，CPU 逻辑核数为 16：CPU 会先并行执行 16 个线程（1 个核 1 个线程），时间片结束后切换另一批 16 个线程，通过 "并行批次 + 并发切换" 完成所有线程的调度。