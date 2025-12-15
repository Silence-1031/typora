### Java中级

## 第一章 异常

### 一、概念

异常是**程序运行过程中可能出现的意外问题**，并非语法错误（语法错误在编译阶段就会报错，不属于异常）。

- 典型示例：
  - 数组索引越界（访问不存在的数组索引）；
  - 算术异常（如 `10 ÷ 0`）；
  - 空指针异常（调用 `null` 对象的方法或属性）；
  - 外部资源异常（读取已删除的文件、断网时获取网络数据）。

### 二、Java 异常体系

Java 中所有问题都以 “对象” 形式存在，异常体系基于继承关系设计，顶层父类为 `Java.lang.Throwable`，主要分为两大分支：`Error`（错误）和 `Exception`（异常）。

![image-20251024185917707](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251024185917707.png)

1. 顶层父类：Throwable

- 作用：代表 Java 中所有 “问题”（包括错误和异常），是异常体系的根类。
- 核心子类：`Error` 和 `Exception`，二者的本质区别决定了开发中是否需要处理。

2. 分支 1：Error（错误）

- 定义：**系统级严重错误**，由 JVM 或操作系统触发，超出程序员控制范围。
- 典型示例：内存溢出（`OutOfMemoryError`）、栈溢出（`StackOverflowError`）。
- 处理原则：**无需程序员处理**（无法通过代码修复，需通过硬件升级、重启系统等外部操作解决）。

3. 分支 2：Exception（异常）

- 定义：**程序运行中可能出现的问题**，由代码逻辑或外部环境导致，是开发中核心处理对象。
- 分类：根据 “异常发生时机”，分为**运行时异常**和**编译时异常**，二者的触发时机和处理要求完全不同。

### 三、Exception 的两大分类

1. 运行时异常（RuntimeException 及其子类）

- 定义：**编译阶段不报错，仅在程序运行时可能触发**的异常，又称 “非受检异常”（Unchecked Exception）。
- 典型示例：
  - `ArrayIndexOutOfBoundsException`（数组索引越界）；
  - `ArithmeticException`（算术异常）；
  - `NullPointerException`（空指针异常）；
  - `ClassCastException`（类型转换异常）。

![image-20251024190558879](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251024190558879.png)

- 核心特点：
  1. 编译阶段无错误提示（IDEA 等工具不会标红）；
  2. 运行时触发后，若未处理，程序会**直接崩溃**（后续代码停止执行）；
  3. 本质原因：多为程序员代码逻辑错误（如数组索引计算错误、未判断对象是否为`null`），可通过优化代码避免。
- 处理原则：**可选处理**（可通过逻辑判断提前规避，如访问数组前检查索引范围、调用方法前判断对象非`null`）。



2. 编译时异常（非 RuntimeException 的 Exception 子类）

- 定义：**编译阶段强制要求处理**的异常，又称 “受检异常”（Checked Exception），若不处理则代码无法通过编译。
- 典型示例：
  - `ParseException`（日期解析异常，如将格式不匹配的字符串转为`Date`对象）；
  - `FileNotFoundException`（文件未找到异常，如读取不存在的文件）；
  - `IOException`（IO 流异常，如写入文件时磁盘空间不足）。
- 核心特点：
  1. 编译阶段报错（IDEA 标红提示），必须处理后才能运行代码；
  2. 本质原因：多为外部环境不确定性（如文件是否存在、网络是否通畅），无法仅通过代码逻辑完全规避；
- 处理原则：**必须处理**（否则代码编译不通过，有两种核心处理方案）。

### 四、编译时异常的两种处理方案

编译时异常必须处理，否则代码无法运行，Java 提供两种标准处理方式：

**方案 1：抛出异常（throws 关键字）**

- 核心逻辑：“将异常抛给调用者处理”，自己不解决，由上层代码负责处理。

- 语法：在方法声明后添加`throws 异常类型1, 异常类型2...`

  ```java
  // 方法抛出ParseException（日期解析异常），由调用show2()的代码处理
  public static void show2() throws ParseException {
      String time = "2024-07-09 11:12:13";
      SimpleDateFormat sdf = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
      Date date = sdf.parse(time); // 编译时异常，需处理
  }
  ```

- 注意：若上层代码继续抛出，最终可能抛给 JVM（JVM 会打印异常信息并终止程序）。

方案 2：捕获异常（try-catch 语句）

- 核心逻辑：“自己处理异常”，通过代码块监视可能抛出异常的代码，捕获后执行容错逻辑（如打印异常信息、提示用户）。

  ```java
  try {
      // 监视可能抛出异常的代码（如调用带编译时异常的方法）
      show2(); 
  } catch (异常类型 e) {
      // 异常捕获后的处理逻辑
      e.printStackTrace(); // 打印异常详细信息（便于调试）
      System.out.println("日期格式错误，请检查时间字符串！"); // 友好提示
  }
  ```

- 多异常处理：若`try`块可能抛出多个异常，可添加多个`catch`块，或直接捕获父类`Exception`（需注意：子类异常需在父类异常前捕获，否则编译报错）。

### 五、细节

1. 异常的 “中断性”：无论运行时异常还是未处理的编译时异常，触发后若未捕获，程序会**立即终止**（后续代码不再执行）；若捕获（`try-catch`），则`catch`块执行后，程序继续执行后续代码。
2. 异常栈信息：异常触发时，JVM 会打印 “异常栈跟踪信息”（`e.printStackTrace()`），从异常发生的最底层方法开始，向上追溯调用链，便于定位错误代码行。
3. 异常的继承特性：捕获父类异常时，会自动捕获所有子类异常（如捕获`Exception`，可处理所有编译时和运行时异常，但不推荐过度使用，可能掩盖具体问题）。

### 六、异常的作用

1. 定位程序 Bug，提升问题排查效率

- **核心价值**：异常是定位系统问题的 “关键线索”，尤其在项目上线后，可通过异常日志快速定位 Bug 位置与原因。
- 具体表现：
  - 程序运行出错时，异常会自动打印**错误位置（如代码行号）** 和**错误原因（如 “除数不能为零”）**；
  - 项目后台会生成 “异常日志文件”，开发者可通过日志中的异常信息（如`NullPointerException`、`ArithmeticException`）直接定位问题代码，快速修改并打补丁；

2. 作为方法的 “特殊返回值”，通知上层调用者执行状态

​	这是异常在编程层面的核心作用，解决了 “方法需返回结果，但错误场景下无合理返回值” 的矛盾。通过`throw`关键字抛出异常，替代传统 “特殊返回值”，实现两大目标：

- **通知执行状态**：上层调用者可通过 “是否捕获到异常” 判断底层方法执行结果（捕获到异常→执行失败，未捕获→执行成功）；
- **传递错误原因**：异常可封装具体错误信息（如 “除数不能为零，参数有误”），让上层明确失败原因。

3. 代码实现逻辑（关键步骤）

```java
// 1. 定义方法时，抛出编译时异常（提醒更强烈）
public static int div(int a, int b) throws Exception { 
    if (b == 0) {
        // 2. 错误场景：抛出异常（封装错误原因），替代return特殊值
        throw new Exception("除数不能为零，参数有误"); 
    }
    // 3. 正常场景：返回计算结果
    return a / b; 
}

// 4. 上层调用者：通过try-catch捕获异常，判断执行状态
public static void main(String[] args) {
    System.out.println("程序开始");
    try {
        int result = div(10, 0); // 调用可能抛出异常的方法
        System.out.println("底层方法执行成功，结果：" + result); // 仅正常时执行
    } catch (Exception e) {
        // 捕获到异常 → 底层执行失败，打印错误信息
        e.printStackTrace(); 
        System.out.println("底层方法执行失败");
    }
    System.out.println("程序结束"); // 异常处理后，程序不会终止（关键优势）
}
```

4. 核心优势

- **逻辑清晰**：上层通过 “是否进入 catch 块” 明确执行状态，无歧义；
- **程序不终止**：捕获异常后，程序会继续执行`catch`之后的代码（如 “程序结束”），避免因局部错误导致整个程序崩溃；
- **提醒强烈**：若抛出 “编译时异常”（需显式声明`throws`），编译器会强制上层处理（捕获或继续抛出），避免遗漏错误场景。

### 七、自定义异常

1. 概念

当企业业务中的特定问题需要用 “异常机制” 管理（如提醒调用者、标记执行失败、定位 Bug）时，开发者自行定义的 “异常类”，称为自定义异常。其本质是 “继承 Java 异常体系的子类”，融入业务语义。

2. 分类

自定义异常的核心是**选择继承的父类**，父类决定了异常的 “行为特性”，分为两类：

| 分类             | 继承父类                       | 核心特性                                                     | 适用场景                                                     |
| ---------------- | ------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 自定义编译时异常 | `Exception`（直接继承）        | 1. 编译阶段强制要求处理（不处理则代码报错）2. 需显式用`throws`声明抛出 | 问题**严重且易出现**，需强制提醒调用者处理（如 “年龄非法”“权限不足”） |
| 自定义运行时异常 | `RuntimeException`（直接继承） | 1. 编译阶段不强制处理，运行时才可能触发2. 无需显式声明`throws`（默认自动抛出） | 问题**不严重且不易出现**，无需强制干扰调用者（如 “输入格式偶尔错误”） |

3. 实现步骤

无论编译时还是运行时异常，基础实现步骤一致，仅父类和声明方式有差异：

​	**步骤 1：定义异常类，继承指定父类**

- 类名需体现业务语义（如`AgeIllegalException`，而非`MyException`），符合 Java 命名规范。
- 继承`Exception` → 编译时异常；继承`RuntimeException` → 运行时异常。

​	**步骤 2：重写父类的构造器**

- 必须重写 “无参构造器” 和 “带异常信息的有参构造器”，目的是**封装异常原因**，便于调用者获取错误详情。（底层依赖父类构造器实现异常信息的存储和传递）

​	**步骤 3：在业务代码中抛出异常**

- 使用`throw new 自定义异常类(异常原因)`的语法，在业务逻辑不满足时抛出异常，通知调用者 “执行失败”。

![image-20251024195952870](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251024195952870.png)

![image-20251024200315620](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251024200315620.png)

**步骤 4：调用者处理异常（编译时异常强制，运行时异常可选）**

- 编译时异常：必须通过`try-catch`捕获，或继续用`throws`向上抛出；
- 运行时异常：可捕获处理，也可忽略（默认抛给 JVM，程序崩溃并打印异常栈）。

```java
public static void main(String[] args) {
    try {
        saveAge(300); // 调用可能抛出编译时异常的方法
    } catch (AgeIllegalException e) {
        // 捕获异常，处理错误（如打印日志、提示用户）
        System.out.println("处理异常：" + e.getMessage());
        e.printStackTrace(); // 打印异常栈，定位代码行数
    }
}
```

- **优先选择运行时异常**：Java 官方（Sun/Oracle）及现代开发规范已逐步摒弃编译时异常，原因是：

  - 编译时异常需层层用`throws`声明，侵入性强（上百层调用需每层处理）；

  - 运行时异常仅在真正出错时触发，不干扰正常开发，且可在最外层统一捕获（如全局异常处理器）。

### 八、异常处理

- 核心原则

异常处理的核心目标是：**避免程序崩溃、便于定位 bug、给用户友好反馈**，而非单纯 “消灭异常”。开发中需平衡 “底层异常传递” 与 “外层统一处理”，避免过度分散的异常处理逻辑。

8.1 方案 1：底层异常层层上抛，最外层集中捕获处理（开发首选）

1. 核心逻辑

- **异常传递**：底层方法（如 C 方法）产生异常时，不直接处理，通过`throws`关键字将异常逐层向上抛给调用者（C→B→A，A 为最外层方法）。
- 统一处理：最外层方法（面向用户 / 入口的方法，如`main`方法、Controller 层方法）集中使用`try-catch`捕获所有异常，完成两件关键事：
  1. **记录异常日志**：打印异常栈信息（`e.printStackTrace()`或日志框架），便于开发人员定位 bug（类似 “飞机黑匣子”）。
  2. **反馈用户**：不直接暴露异常详情（避免用户看到代码级错误），而是返回易懂信息（如 “资源不存在”“操作非法”）。

2. 实际场景案例

- 网页访问不存在的地址（如新浪、B 站的错误链接）时，服务器底层抛出 “资源未找到” 异常，最终外层统一返回 “404 页面不存在”“5 秒后跳转首页” 等友好提示，同时内部记录异常日志。

3. 代码示例

```java
public class ExceptionDemo {
    public static void main(String[] args)  {
        try {
            show();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void show() throws Exception {
        String str="2023-07-09 11:12:13";
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
        Date date=sdf.parse(str);
        System.out.println(date);

        InputStream is = new FileInputStream("0:/ss.png");

    }
}

```

8.2 方案 2：最外层捕获异常后，尝试重新修复（特殊场景用）

1. 核心逻辑

- **适用场景**：异常是 “临时性、可重试” 的（如用户输入错误、网络波动、下载中断），而非永久性错误（如资源不存在）。
- **处理流程**：最外层捕获异常后，不直接结束程序，而是通过**循环重试**让用户 / 程序重新执行操作，直到成功或达到重试次数。

2. 实际场景案例

- 用户输入商品定价时，误输入文字（如 “abc”），程序捕获 “输入格式异常” 后，提示 “请输入合法数字”，并重新让用户输入，直到输入正确。

3. 代码示例（关键逻辑）

```java
public class ExceptionDemo {
    public static void main(String[] args) {
        while (true) {
            try {
                double price = userInputPrice();
                System.out.println("The price is"+price);
                break;
            } catch (Exception e) {
                System.out.println("The data is wrong.");
            }
        }
    }

    public static double userInputPrice()
    {
        Scanner sc =new Scanner(System.in);
        System.out.println("enter the price:");
        double price=sc.nextDouble();
        return price;
    }
}

```



8.3 开发中的关键注意事项

1. **异常捕获范围**：优先用`catch (Exception e)`捕获所有异常，避免逐个捕获多个具体异常（如`FileNotFoundException`、`ParseException`），减少代码冗余。

2. **日志记录要求**：必须记录异常栈信息（`e.printStackTrace()`或日志框架如 Logback），仅靠 “用户提示” 无法定位 bug。

   

## 第二章 泛型

### 一、概念

1. 泛型（genericity）是指在**定义类、接口或方法时**，同时声明一个或多个 “类型变量”（用尖括号 `<T>` 表示，`T` 为类型变量名，可自定义），<>用来接收具体的数据类型

   - 包含泛型的类称为**泛型类**（如 `ArrayList<T>`）、包含泛型的接口称为**泛型接口**、包含泛型的方法称为**泛型方法**，三者统称为 “泛型”。

   - 示例：`ArrayList<String>` 中，`<String>` 是给泛型类 `ArrayList<T>` 的类型变量 `T` 传入的具体类型（字符串类型）。

2. **本质作用**：泛型的核心是**将数据类型的约束从 “运行时” 提前到 “编译时”**，通过预先约定容器 / 类能操作的数据类型，避免运行时因类型不匹配导致的异常。

### 二、泛型的作用

1. 约束数据类型，避免乱存乱取

- 无泛型的问题：集合（如未指定泛型的`ArrayList`）可存储任意类型数据（字符串、整数、布尔值等），导致数据类型混乱。

  ```java
  ArrayList list = new ArrayList();
  
  //可同时添加
  list.add("Java");
  list.add(23);
  list.add(true);
  ```

  

- 有泛型的解决：声明时指定类型（如`ArrayList<String>`），编译阶段就会强制约束：只能存储指定类型数据，非指定类型会直接报错。

  ```java
  ArrayList<String> list = new ArrayList<>();
  //添加`list.add(23)`会编译报错
  ```

2. 避免强制类型转换，简化代码

- 无泛型的问题：从无泛型集合中取数据时，返回值默认是`object`类型，需手动强制转换为目标类型，代码繁琐。

  ```java
  Object obj = list.get(0); String s = (String) obj;
  //若实际是整数，转换会报错
  ```

- 有泛型的解决：从泛型集合中取数据时，返回值直接是指定类型，无需强制转换。

  ```java
  String s = list.get(0);
  //list直接为ArrayList<String>,get方法直接返回String
  ```

### 三、工作原理

泛型的约束能力源于 “类型变量的替换机制”，核心逻辑如下：

1. **泛型类的定义**：`ArrayList` 是泛型类，源码中声明为 `public class ArrayList<E> extends AbstractList<E>...`，其中 `<E>` 是类型变量（代表 “元素类型”）。

2. **类型传递与替换**：当我们声明 `ArrayList<String>` 时，是将具体类型 `String` 传给了泛型类的类型变量 `E`。

3. 方法签名自动替换：泛型类中所有使用`E`的地方，都会自动替换为传入的具体类型（如

    `String`）：

   - `add(E e)` 方法 → 替换为 `add(String e)`（只能接收 `String` 类型参数）；
   - `E get(int index)` 方法 → 替换为 `String get(int index)`（返回值直接是 `String` 类型）。

简言之：泛型的本质是 将具体类型作为参数传给类型变量，进而替换类 / 接口 / 方法中所有该类型变量的位置。



### 四、泛型类

1. 泛型类的定义贵伐

- 语法格式

  ```java
  // 自定义泛型类 MyArrayList，类型变量代表元素类型
  public class MyArrayList<E> {}
  ```

- 类型变量命名规范

  | 类型变量 | 含义            | 适用场景                   |
  | -------- | --------------- | -------------------------- |
  | E        | Element（元素） | 集合、容器类（存储元素）   |
  | T        | Type（类型）    | 通用类型（如返回值类型）   |
  | K        | Key（键）       | 键值对中的「键」（如 Map） |
  | V        | Value（值）     | 键值对中的「值」（如 Map） |

  > 注意：变量名需大写，避免用小写字母（如`a`/`b`），否则不符合行业规范，可读性差。

2. 泛型类的实战案例

```java
public class MyArrayList<E> {
    // 内部隐藏真实存储容器（封装细节）
    private ArrayList<E> innerList = new ArrayList<>();
    
    // 模拟添加方法（参数类型为 E）
    public boolean add(E element) {
        return innerList.add(element); // 调用内部ArrayList的add方法
    }
    
    // 模拟删除方法（参数类型为 E）
    public boolean remove(E element) {
        return innerList.remove(element);
    }
    
    // 重写toString，返回内部存储的内容
    @Override
    public String toString() {
        return innerList.toString();
    }
}
```

> 使用泛型类：创建对象时指定具体类型，JDK 7 + 支持菱形语法（右侧类型可省略）

> ```java
> // JDK 7前：左右都需指定类型
> MyArrayList<String> list1 = new MyArrayList<String>();
> // JDK 7后：右侧可省略（菱形语法）
> MyArrayList<String> list2 = new MyArrayList<>();
> ```



### 五、自定义泛型接口

1. 泛型接口的定义规范

- 语法格式：在接口名后添加` <类型变量>`，接口中的方法参数 / 返回值可使用该类型变量。


  ```java
  // 泛型接口 DataOperate，类型变量 T 代表操作的数据类型（如学生/老师）
  public interface DataOperate<T> {
      void add(T data);
      boolean remove(T data);
      T query(int id);
  }
  ```

2. 泛型接口的应用场景（通用增删改查）

视频以「学生 / 老师数据的通用操作」为例，说明泛型接口的优势：避免为每种数据类型单独定义接口，实现**接口复用**。

1. 定义数据类

   ```java
   public class Student {} // 学生类
   public class Teacher {} // 老师类
   ```

2. 实现泛型接口

   ```java
   // 实现接口时指定 T = Student，方法参数/返回值均为 Student
   public class StudentData implements DataOperate<Student> {
       @Override
       public void add(Student data) {
           System.out.println("添加学生：" + data);
       }
       
       @Override
       public boolean remove(Student data) {
           System.out.println("删除学生：" + data);
           return true;
       }
       
       @Override
       public Student query(int id) {
           System.out.println("查询ID为" + id + "的学生");
           return new Student(); // 模拟返回学生对象
       }
   }
   ```

3. 针对老师的实现类：

  ```java
  // 实现接口时指定 T = Teacher，方法参数/返回值均为 Teacher
  public class TeacherData implements DataOperate<Teacher> {
      @Override
      public void add(Teacher data) {
          System.out.println("添加老师：" + data);
      }
      
      @Override
      public boolean remove(Teacher data) {
          System.out.println("删除老师：" + data);
          return true;
      }
      
      @Override
      public Teacher query(int id) {
          System.out.println("查询ID为" + id + "的老师");
          return new Teacher(); // 模拟返回老师对象
      }
  }
  ```

4. 使用实现类

   ```java
   // 操作学生数据（只能传入 Student 对象）
   DataOperate<Student> studentOperate = new StudentData();
   studentOperate.add(new Student()); 
   studentOperate.add(new Teacher()); // 编译错误：类型不匹配
   
   // 操作老师数据（只能传入 Teacher 对象）
   DataOperate<Teacher> teacherOperate = new TeacherData();
   teacherOperate.query(1); // 查询ID=1的老师
   ```

### 六、泛型方法

1. 定义：泛型方法是**在方法声明时自行定义泛型类型变量**的方法，核心特征是：在方法的「返回值类型前」通过`<T>`（T 为泛型占位符，可替换为 E、K、V 等）声明泛型，且该泛型仅作用于当前方法。

|   区别   |               泛型方法                |       非泛型方法（使用外部泛型）       |
| :------: | :-----------------------------------: | :------------------------------------: |
| 泛型来源 |        方法自身声明（如`<T>`）        |  所属类 / 接口定义的泛型（如泛型类）   |
| 作用范围 |              仅当前方法               |             整个类 / 接口              |
| 示例代码 | `public <T> void printArray(T[] arr)` | `public void add(E e)`（E 来自泛型类） |

2. 核心作用

- 通用化方法参数：解决「一个方法适配多种数据类型」的问题，避免重复编写同类方法（如打印字符串数组、学生数组可共用一个泛型方法）。

  示例：打印任意类型数组的泛型方法

  ```java
  // 泛型方法：声明<T>，参数为T[]类型，适配所有数组
  public <T> void printArray(T[] arr) {
      for (T element : arr) {
          System.out.println(element);
      }
  }
  ```

- 保证返回值类型一致性：返回值类型与参数类型自动匹配，避免强制类型转换。

  示例：获取任意类型数组的 “最大值”（语法演示）

  ```java
  // 返回值类型为T，与参数数组类型一致
  public <T> T getMax(T[] arr) {
      // 逻辑省略，语法上返回T类型，避免类型强转
      return arr[0]; 
  }
  ```



###七、通配符 - ？

1. 定义与场景

通配符`?`是**使用泛型时代表 “任意类型” 的占位符**，仅用于「泛型的使用场景」（如集合参数、返回值），不用于泛型的定义（如泛型类、泛型方法声明）。

2. 为何需要通配符？

Java 中，**泛型不具备 “协变性”**：即使`A`是`B`的子类，`List<A>`也不是`List<B>`的子类

示例：若方法参数为`List<Car>`，则`List<XiaomiCar>`（XiaomiCar 继承 Car）无法传入，此时需通配符解决通用性问题。

3. 通配符的基础用法

用`List<?>`表示 “任意类型的 List 集合”，可接收所有泛型类型的 List 参数：

```java
// 通配符方法：接收任意类型的List集合
public void startRace(List<?> carList) {
    for (Object car : carList) {
        System.out.println("参赛：" + car);
    }
}

// 调用时，List<XiaomiCar>、List<BYDCar>均可传入
List<XiaomiCar> xiaomiList = new ArrayList<>();
List<BYDCar> bydList = new ArrayList<>();
startRace(xiaomiList); // 合法
startRace(bydList);    // 合法
```



### 八、泛型上下限

通配符`?`允许任意类型，可能导致非法类型传入（如将`List<Dog>`传入 “汽车比赛” 方法），因此需要**上下限**缩小类型范围。

1. 上限（`? extends 父类`）

- 语法：`List<? extends Car>`

- 含义：仅允许「父类本身及子类」的泛型类型（如`Car`、`XiaomiCar`、`BYDCar`，不允许`Dog`）。

  示例：限制 “仅汽车及子类集合” 参赛

  ```java
  // 上限通配符：仅接收Car及其子类的List
  public void startCarRace(List<? extends Car> carList) {
      for (Car car : carList) {
          car.run(); // 可调用Car类的方法，安全
      }
  }
  
  // 调用时，List<Dog>无法传入（Dog不继承Car）
  List<Dog> dogList = new ArrayList<>();
  startCarRace(dogList); // 编译报错，非法类型
  ```

2. 下限（`? super 子类`）

- 语法：`List<? super Car>`

- 含义：仅允许「子类本身及父类」的泛型类型（如`Car`、`Object`，不允许`XiaomiCar`）。

- 核心场景：**写入数据**（方法仅向集合中添加元素，不读取元素），实际使用频率较低。

  示例：限制 “仅能添加 Car 及父类类型” 的集合

  ```java
  // 下限通配符：仅接收Car及其父类的List
  public void addCar(List<? super Car> carList) {
      carList.add(new XiaomiCar()); // 可添加Car子类对象，安全
  }
  
  // 调用时，List<Object>、List<Car>均可传入
  List<Object> objectList = new ArrayList<>();
  addCar(objectList); // 合法
  ```

### 九、泛型支持的类型

1. 泛型（及依赖泛型的集合，如`ArrayList`）**仅支持对象类型（引用数据类型）**，无法直接使用 8 种基本数据类型（`int`、`double`、`char`等）。

- 示例：声明`ArrayList<int>`会直接报错，需改为`ArrayList<Integer>`。

2. 原因：泛型擦除机制

泛型是**编译阶段的语法糖**，编译后生成的`.class`文件中，泛型的具体类型会被 “擦除”，统一替换为`Object`类型（所有类的父类）。

- **`Object`是对象类型，只能存储对象的引用，无法直接存储基本数据类型**（基本数据类型存储在栈内存，而非堆内存的对象中）。
- 例：编译前`ArrayList<String>`，编译后会变为`ArrayList<Object>`，若传入`int`（基本类型），`Object`无法接收，因此泛型禁止直接使用基本类型。

>语法糖（Syntactic sugar）,是由英国计算机科学家彼得·约翰·兰达（Peter J. Landin）发明的一个术语，指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会。

### 十、包装类

1. 包装类的定义与作用

包装类是 Java 为 8 种基本数据类型提供的 “包装器” 类，**将基本数据类型封装为对象**，使其能适配泛型、集合等仅支持对象的场景，同时实现 “万物皆对象” 的设计思想。

2. 基本类型与包装类的对应关系

| 基本数据类型 | 对应的包装类 |
| :----------: | :----------: |
|    `byte`    |    `Byte`    |
|   `short`    |   `Short`    |
|    `int`     |  `Integer`   |
|    `long`    |    `Long`    |
|   `float`    |   `Float`    |
|   `double`   |   `Double`   |
|    `char`    | `Character`  |
|  `boolean`   |  `Boolean`   |

3. 包装类的核心操作：自动装箱与自动拆箱（开发中常用）

Java 5 + 引入 “自动装箱 / 拆箱” 语法糖，无需手动调用`valueOf`或转换方法，JVM 会自动完成基本类型与包装类的转换。

- 自动装箱：基本类型直接赋值给包装类变量。

  ```java
  Integer a = 100;
  //JVM 自动执行Integer.valueOf(100)
  ```

- 自动拆箱：包装类对象直接赋值给基本类型变量。

  ```java
  int b = a;
  JVM 自动执行a.intValue()
  ```

- **集合中的应用**：向`ArrayList<Integer>`中添加`100`时，JVM 自动装箱为`Integer`对象，取出时自动拆箱为`int`。

4. 包装类的额外实用功能

包装类除了适配泛型，还提供了字符串与基本类型的转换功能，是开发中的高频操作：

- 基本类型 → 字符串

  - 方案 1：调用包装类的静态方法`toString(基本类型值)`，如`String s = Integer.toString(23);`（结果为`"23"`）。

  - 方案 2：包装类对象调用`toString()`（基于自动装箱），如`Integer a = 23; String s = a.toString();`。

  - 注意：字符串拼接（如`23 + ""`）也能转字符串，但包装类的`toString`支持更多格式（如二进制、十六进制，如`Integer.toString(23, 2)`返回`"10111"`）。

- **字符串 → 基本类型（高频）**

  将字符串形式的数值（如网页输入的`"98"`）转为基本类型，用于计算或逻辑判断：

  - 方案 1：调用包装类的静态方法`parseXXX(字符串)`，如`int num = Integer.parseInt("98");`、`double d = Double.parseDouble("3.14");`。

  - 方案 2：调用`valueOf(字符串)`（返回包装类对象，可自动拆箱为基本类型），如`int num = Integer.valueOf("98");`。

  - 注意：若字符串非合法数值（如`"98AA"`），会抛出`NumberFormatException`（数字格式异常），需处理。

> parse:（对句子）作语法分析；作句法分析

## 第三章 集合框架

### 一、整体分类

Java 集合框架核心分为两大家族，核心区别在于存储数据的 “维度”：

|    集合家族    |   类型   |            核心特点             |             数据形式             |
| :------------: | :------: | :-----------------------------: | :------------------------------: |
| **Collection** | 单列集合 |     每个元素仅包含 “一个值”     |  单个数据（如`"Java"`、`123`）   |
|    **Map**     | 双列集合 | 每个元素包含 “两个值”（键值对） | 键 - 值映射（如`"name":"张三"`） |

**注意**：两类集合支持嵌套（如 Map 的值可设为 Collection 集合）

### 二、Collection 体系结构

Collection 是所有单列集合的**顶层接口**（仅定义规范，不能直接创建对象），其下衍生出两大核心子接口，再对应具体实现类，结构如下：

```plaintext
Collection（顶层接口，定义增删改查规范）
├─ List（子接口，规定“有序、可重复、有索引”特点）
│  ├─ ArrayList（实现类，最常用，底层数组）
│  └─ LinkedList（实现类，底层链表，适合频繁增删）
└─ Set（子接口，规定“无序、不重复、无索引”特点）
   ├─ HashSet（实现类，最常用，底层哈希表）
   ├─ LinkedHashSet（实现类，有序（按添加顺序）、不重复、无索引）
   └─ TreeSet（实现类，按大小升序排序、不重复、无索引）
```

![image-20251025114829075](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251025114829075.png)

**关键结论**：

1. 接口（Collection/List/Set）仅定义 “规范 / 特点”，不能直接实例化；
2. 实现类（ArrayList/HashSet 等）是具体可使用的集合对象，需通过 “接口引用指向实现类对象”（多态写法，如`List<String> list = new ArrayList<>()`）；
3. 实际开发中，**ArrayList**（List 家族）和**HashSet**（Set 家族）是最常用的实现类。

### 三、List 与 Set 家族的对比

|     对比     |                      List 家族                      |                           Set 家族                           |
| :----------: | :-------------------------------------------------: | :----------------------------------------------------------: |
|  **有序性**  |       有序（按 “添加顺序” 保存，与大小无关）        | 大部分无序（HashSet）；LinkedHashSet 按添加顺序有序；TreeSet 按大小排序 |
|  **重复性**  |     可重复（允许添加相同元素，如两次`"Java"`）      |  不可重复（相同元素仅保留一个，如两次`"Java"`最终只存一个）  |
|   **索引**   |   有索引（类似数组，支持`get(0)`获取第一个元素）    |    无索引（不支持通过索引操作元素，如`get()`方法不存在）     |
| **常用场景** | 需按添加顺序存储、允许重复数据（如 “用户浏览记录”） | 需去重、无需顺序（如 “用户标签列表”）；需排序（TreeSet，如 “成绩排名”） |

```java
// 1. List集合：有序、可重复、有索引
List<String> list = new ArrayList<>();
list.add("Java");
list.add("Java"); // 允许重复
list.add("C++");
System.out.println(list); // 输出：[Java, Java, C++]（按添加顺序）
System.out.println(list.get(0)); // 输出：Java（支持索引）

// 2. Set集合（HashSet）：无序、不重复、无索引
Set<String> set = new HashSet<>();
set.add("Java");
set.add("Java"); // 重复元素被过滤
set.add("C++");
System.out.println(set); // 输出：[C++, Java]（无序，与添加顺序不同）
// set.get(0); // 报错：Set无get()方法（无索引）
```

### 四、Collection 的通用方法

Collection 作为顶层接口，定义了**所有单列集合都通用的方法**

| 方法名               | 功能描述                         | 返回值 / 注意事项                                            |
| -------------------- | -------------------------------- | ------------------------------------------------------------ |
| `add(E e)`           | 向集合添加一个元素               | `boolean`：添加成功返回`true`，失败返回`false`（如 Set 添加重复元素返回`false`） |
| `remove(Object o)`   | 删除集合中的指定元素             | `boolean`：删除成功返回`true`，元素不存在返回`false`         |
| `clear()`            | 清空集合中所有元素               | 无返回值（集合变为空）                                       |
| `contains(Object o)` | 判断集合是否包含指定元素         | `boolean`：包含返回`true`，否则`false`                       |
| `isEmpty()`          | 判断集合是否为空（元素个数为 0） | `boolean`：空返回`true`，否则`false`                         |
| `size()`             | 获取集合中元素的个数             | `int`：返回元素数量                                          |
| `toArray()`          | 将集合转换为数组                 | `Object[]`：默认返回 Object 类型数组；可通过方法引用转为指定类型（如`String[] arr = set.toArray(String[]::new)`） |

**通用方法代码示例**：

```java
// 多态写法：Collection引用指向ArrayList实现类（通用方法与实现类无关）
Collection<String> coll = new ArrayList<>();

// 1. 添加元素
coll.add("张三");
coll.add("李四");
System.out.println(coll); // 输出：[张三, 李四]

// 2. 删除元素
coll.remove("李四");
System.out.println(coll); // 输出：[张三]

// 3. 判断是否包含
System.out.println(coll.contains("张三")); // 输出：true

// 4. 获取元素个数
System.out.println(coll.size()); // 输出：1

// 5. 转换为数组
Object[] arr1 = coll.toArray();
String[] arr2 = coll.toArray(String[]::new); // 转为String数组

// 6. 清空集合
coll.clear();
System.out.println(coll.isEmpty()); // 输出：true
```

### 五、Collection的遍历

1. **迭代器（Iterator）遍历**：集合专用遍历方式

迭代器是`Java.util`包下的`Iterator`接口，用于**专门遍历集合**，每个集合可通过`iterator()`方法获取专属迭代器对象。

- 核心逻辑与步骤

  - 获取迭代器对象：通过集合调用`iterator()`方法，泛型类型与集合元素类型一致（如集合存`String`，迭代器为`Iterator<String>`）。

    ```java
    Collection<String> names = new ArrayList<>();
    names.add("张无忌");
    names.add("玄冥二老");
    Iterator<String> it = names.iterator(); // 获取迭代器
    ```

  - 遍历元素（循环判断 + 取值）

    - 用`hasNext()`判断 “当前位置是否有元素”（返回`boolean`，`true`表示有素）；
    - 用`next()`“取出当前元素并移至下一个位置”（注意：每次`next()`都会移位，不可重复调用）。

    ```java
    while (it.hasNext()) { // 判断当前位置是否有元素
        String name = it.next(); // 取元素+移位
        System.out.println(name);
    }
    ```

- **越界异常**：若`hasNext()`返回`false`后仍调用`next()`，会抛出`NoSuchElementException`（无此元素异常）。

2. 增强 for 循环简洁遍历（支持数组 + 集合）

增强 for 循环是 JDK5 后新增的语法，本质是**迭代器的简化版**，可同时遍历**数组和集合**，代码更简洁。

- 格式

```java
for (元素数据类型 变量名 : 要遍历的数组/集合) {
    // 变量名即当前遍历到的元素
    System.out.println(变量名);
}
```

```java
for (String name : names) { // names为Collection<String>集合
    System.out.println(name);
}
```

```java
//遍历数组
String[] users = {"宋青书", "殷素素"};
for (String user : users) {
    System.out.println(user);
}
```

- 特点：语法简洁，无需手动管理索引或迭代器移位，无法获取元素索引（如需索引，需用普通 for 循环，仅`List`支持）；

- 底层逻辑：遍历集合时，会自动转换为迭代器（`Iterator`）遍历，本质与迭代器一致。

3. Lambda 表达式遍历：JDK8 + 函数式编程风格

JDK8 后`Collection`接口新增`forEach(Consumer<? super T> action)`方法，结合 Lambda 表达式（函数式接口简化），实现更简洁的遍历。

- 核心逻辑

  - `forEach()`方法接收`Consumer`接口对象（函数式接口，仅一个抽象方法`accept(T t)`，用于接收遍历到的元素），类型为T或者T的父类；

  - Lambda 表达式可简化`Consumer`的匿名内部类，直接定义元素的处理逻辑。

### 六、遍历方式的区别

1. 并发修改异常

- 当**遍历集合的同时，对集合进行增删元素操作**时，可能出现业务异常（如删不干净、直接抛错），这类问题称为 “并发修改异常”。
  - 本质原因：集合遍历依赖索引或迭代器状态，增删操作会改变集合结构（如元素移位），导致遍历逻辑 “错位”（如正向 for 循环漏删）。

2. 4 种遍历方式的特点与异常处理

Java 中集合遍历主要有 4 种方式，核心区别在于**是否支持 “遍历 + 增删” 操作**

|           遍历方式           |               适用集合类型                | 是否支持 “遍历 + 增删” |                      核心问题与解决方案                      |
| :--------------------------: | :---------------------------------------: | :--------------------: | :----------------------------------------------------------: |
| 1. 普通 for 循环（索引遍历） |      支持索引的集合（如 ArrayList）       |    是，但需处理逻辑    | 方案 1：删除后执行`i--`（让索引回退，重新检查移位后的元素）；<br>方案 2：倒序遍历（从最后一个元素开始删，不影响未遍历的前序元素）。 |
|    2. 迭代器（Iterator）     | 所有 Collection 集合（含 Set、ArrayList） | 是，需用迭代器自身方法 | 问题：若用`集合.remove()`删元素，迭代器会检测到集合结构被修改，直接抛`ConcurrentModificationException`。解决方案：用**迭代器自带的`it.remove()`**（删除当前遍历到的元素，同时同步迭代器与集合状态，避免异常）。 |
| 3. 增强 for 循环（for-each） |           所有 Collection 集合            |           否           | 问题：底层基于迭代器实现，但隐藏了迭代器对象，无法调用`it.remove()`，若用`集合.remove()`会抛异常。结论：仅适合 “纯遍历”，不支持增删。 |
|  4. Lambda 表达式（JDK8+）   |           所有 Collection 集合            |           否           | 问题：本质是增强 for 循环的简化，同样依赖迭代器，增删操作会抛异常。结论：仅适合 “纯遍历”，代码简洁但功能受限。 |

3.  不同场景的遍历选择

- **有索引集合（如 ArrayList）**：
  - 仅遍历：4 种方式均可，优先选 Lambda（简洁）或增强 for；
  - 遍历 + 增删：用普通 for 循环（倒序或`i--`），或迭代器（`it.remove()`）。
- **无索引集合（如 Set）**：
  - 仅遍历：增强 for 或 Lambda；
  - 遍历 + 增删：**只能用迭代器**（唯一支持 “无索引 + 增删” 的方式），且必须调用`it.remove()`。

5. 底层原理补充

迭代器（Iterator）之所以能检测并发修改，依赖两个核心变量：

1. `modCount`：集合的 “修改次数”，每次增删操作会`modCount++`；
2. `expectedModCount`：迭代器初始化时记录的`modCount`（预期修改次数）。

- 遍历过程中，迭代器每次调`next()`都会检查`modCount == expectedModCount`
  - 若不相等（集合被外部修改），直接抛`ConcurrentModificationException`；
  - 若用`it.remove()`，迭代器会同步更新`expectedModCount = modCount`，避免异常。

### 七、List集合

- List 集合的独有功能（索引相关）

正因为`List`支持索引，其在`Collection`基础上扩展了 4 个核心独有方法，均围绕 “索引操作元素” 设计，是开发中高频使用的 API：

|      方法功能      | 具体方法<br>（以`List<String> names`为例） |                             说明                             |
| :----------------: | :----------------------------------------: | :----------------------------------------------------------: |
| 插入元素到指定索引 |   `names.add(int index, String element)`   | 如`names.add(2, "赵敏")`：在索引 2 的位置插入 “赵敏”，原索引≥2 的元素自动后移。 |
|  根据索引删除元素  |         `names.remove(int index)`          | 如`names.remove(1)`：删除索引 1 的元素（如 “李四”），返回被删除的元素（可忽略）。 |
|  根据索引修改元素  | `names.set(int index, String newElement)`  | 如`names.set(2, "金毛")`：将索引 2 的元素替换为 “金毛”，返回修改前的元素（如原 “王五”）。 |
|  根据索引获取元素  |           `names.get(int index)`           | 如`names.get(0)`：直接获取索引 0 的元素（如 “张三”），是`List`遍历的核心方法之一。 |

注意：上述方法的索引需满足 “0 ≤ 索引 < 集合长度”，否则会抛出`IndexOutOfBoundsException`（索引越界异常）。

7.1 ArrayList 与 LinkedList 的核心区别

二者均为`List`接口的实现类，**功能、特点完全一致**（有序、可重复、有索引），核心差异在于**底层数据结构**，进而导致应用场景不同：

| 集合类     | 底层数据结构       | 核心优势场景                | 核心劣势场景                |
| ---------- | ------------------ | --------------------------- | --------------------------- |
| ArrayList  | 数组（连续内存）   | 基于索引的查询操作          | 增删操作（尤其头部 / 中间） |
| LinkedList | 链表（非连续内存） | 增删操作（尤其头部 / 中间） | 基于索引的查询操作          |

7.2 ArrayList 底层核心原理（基于数组）

1. 数组的特性（决定 ArrayList 行为）

   - **内存结构**：内存中一块**连续的区域**，被等分为若干相同大小的 “块”，每块存储一个数据，且有唯一索引（从 0 开始）。

   - 索引查询快的本质：通过 “数组起始地址 + 索引 × 单位长度” 直接计算目标数据的内存地址，查询任意索引的耗时相等（无需遍历）。

2. ArrayList 的增删效率低的原因

   - ArrayList 的动态性本质：数组是 “固定长度” 的，ArrayList 通过 “扩容” 实现 “大小可变”，扩容过程有额外开销：
     1. 当数组容量不足时，创建一个**更大的新数组**；
     2. 将原数组的所有数据**拷贝到新数组**；
     3. 丢弃原数组，用新数组存储后续数据。

   - **扩容规则**：每次扩容为原数组长度的**1.5 倍**（通过 “原长度右移 1 位” 计算，如 10→15，15→22，右移 1 位等价于除以 2 取整）。

3. 适用场景

   - 优先用于**查询操作频繁**的场景（如：通过索引获取数据、遍历所有元素）；

   - 避免用于**增删操作频繁**的场景（尤其是在数组头部或中间增删，如实现 “队列”）。

7.3 LinkList底层原理

1. 底层数据结构：双向链表

   ![image-20251025134328856](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251025134328856.png)

   - 链表节点构成：每个元素以「节点（Node）」形式存储，每个 Node 包含三个部分：
     - `item`：存储当前节点的数据（元素值）；
     - `prev`：指向**前一个节点**的引用（指针）；
     - `next`：指向**后一个节点**的引用（指针）；

   - 链表首尾标识：LinkedList 内部维护两个关键指针：
     - `first`：指向链表的**头节点**（第一个元素）；
     - `last`：指向链表的**尾节点**（最后一个元素）；

   - **无连续内存**：节点在内存中无需连续存储，通过 `prev` 和 `next` 关联形成链式结构，避免了 ArrayList 因「动态扩容」产生的内存浪费问题。

2. 核心特性：基于双向链表的优势与局限

**优势：**

- 高效的首尾操作
  - 新增 / 删除头节点（`addFirst()`/`removeFirst()`）、尾节点（`addLast()`/`removeLast()`）时，仅需修改 `first` 或 `last` 指针的指向，**时间复杂度 O (1)**；
  - 这也是 LinkedList 实现 `Deque`（双端队列）接口的核心原因，可直接作为队列、栈使用。
- 无固定容量限制
  - 链表节点随元素新增动态创建，随元素删除回收，无需像 ArrayList 那样预设初始容量（如默认 10），也无需在容量不足时进行「1.5 倍扩容」，内存利用率更灵活。
- 插入 / 删除效率高（非首尾位置需注意）
  - 若已知要操作的节点（如通过迭代器定位后），插入 / 删除仅需修改前后节点的 `prev`/`next` 引用，**时间复杂度 O (1)**；
  - （注意：若需先通过索引定位节点，需从头 / 尾遍历，时间复杂度会上升为 O (n)，这点需结合场景判断）。

**局限**

- 查询效率低
  - 无法像 ArrayList 那样通过「索引 + 数组首地址」直接计算元素内存位置（随机访问）；
  - 若要获取指定索引（`get(int index)`）的元素，需从 `first` 或 `last` 开始「逐个遍历」（会判断索引更靠近头还是尾，选择较短路径），**时间复杂度 O (n)**，索引越大（或越靠近中间），查询越慢。
- 额外内存开销
  - 每个元素除了存储自身数据，还需额外存储 `prev` 和 `next` 两个指针，相比 ArrayList（仅存储数据），内存开销更大。

3. 常用核心方法（结合 List 与 Deque 接口）

LinkedList 同时实现 `List` 接口和 `Deque` 接口

| 方法分类        | 具体方法        | 功能描述                                      | 时间复杂度 |
| --------------- | --------------- | --------------------------------------------- | ---------- |
| 双端队列特有    | `addFirst(E e)` | 向链表头部添加元素                            | O(1)       |
|                 | `addLast(E e)`  | 向链表尾部添加元素                            | O(1)       |
|                 | `getFirst()`    | 获取头节点元素（peek 为空时返回 null）        | O(1)       |
|                 | `getLast()`     | 获取尾节点元素（peek 为空时返回 null）        | O(1)       |
|                 | `removeFirst()` | 删除并返回头节点元素（poll 为空时返回 null）  | O(1)       |
|                 | `removeLast()`  | 删除并返回尾节点元素（poll 为空时返回 null）  | O(1)       |
| 栈操作（Deque） | `push(E e)`     | 向头部添加元素（等价于 addFirst ()）          | O(1)       |
|                 | `pop()`         | 从头部删除并返回元素（等价于 removeFirst ()） | O(1)       |

4. 与 ArrayList 的核心性能对比

LinkedList 的适用场景需结合与 ArrayList 的对比判断，关键差异如下表：

|                操作场景                |       LinkedList 性能        |               ArrayList 性能                |                        结论                         |
| :------------------------------------: | :--------------------------: | :-----------------------------------------: | :-------------------------------------------------: |
|      随机访问（get (int index)）       |             O(n)             |                    O(1)                     |                 **ArrayList 完胜**                  |
| 首尾新增 / 删除（addFirst/removeLast） |             O(1)             | O (n)（尾部扩容可能 O (1)，头部需移动元素） |                 **LinkedList 完胜**                 |
|          中间位置新增 / 删除           | O (n)（定位）+ O (1)（删除） |              O (n)（移动元素）              | 若定位快（如迭代器），LinkedList 略优；否则两者相近 |
|                内存开销                |       较高（多存指针）       |              较低（仅存数据）               |            内存敏感场景选 **ArrayList**             |
|         遍历效率（迭代器遍历）         |             O(n)             |                    O(n)                     |   效率相近，ArrayList 略快（内存连续，缓存友好）    |

### 八、Set集合

- 特点：不重复、无索引（所有`Set`实现类均具备），“有序性” 因实现类不同有差异。

- 不重复：添加重复元素时会自动去重，仅保留一个；
- 无索引：无法通过索引操作元素（无`get(int index)`方法）；
- 有序性差异：
  - `HashSet`：**无序**（添加顺序与输出顺序可能不一致）；
  - `LinkedHashSet`：**有序**（保留添加顺序，是`Set`家族中的 “特例”）；
  - `TreeSet`：**自定义有序**（默认按元素大小升序排序，可自定义排序规则）。

8.1 HashSet

1. 哈希值（HashCode）：哈希值是理解`HashSet`底层的基础

- **定义**：哈希值是 Java 中每个对象的一个`int`类型整数（范围：-2³¹ ~ 2³¹-1，约 42 亿个值），可理解为对象的 “逻辑地址”（非物理内存地址）。

- **获取方式**：所有对象都继承自`Object`类，可通过调用`Object.hashCode()`方法获取自身哈希值。

- 核心特点

  - 同一对象：多次调用`hashCode()`，返回的哈希值**始终相同**。

  - 不同对象：哈希值**大概率不同**，但存在 “哈希碰撞”（不同对象哈希值相同）的可能

2. HashSet 底层核心：哈希表（Hash Table）

`HashSet`的 “无序、不重复、无索引” 特性，本质由其底层**哈希表**结构决定

- 哈希表的结构差异（JDK8 前后）

| 版本          | 哈希表结构           | 核心改进目的               |
| ------------- | -------------------- | -------------------------- |
| JDK 8 之前    | 数组 + 链表          | 基础存储结构，解决哈希碰撞 |
| JD 1.8 及之后 | 数组 + 链表 + 红黑树 | 优化长链表的查询性能       |

- `HashSet`创建后首次添加数据时，会创建默认长度为**16**的数组（数组名`table`），默认 “加载因子” 为**0.75**（后续扩容用）。

  1. **计算索引**：获取对象 A 的哈希值，通过 “哈希函数”（可理解为 “哈希值 % 数组长度”，实际是更高效的与运算）计算出其在数组中的**索引位置**。
  2. 判断位置是否为空
     - 若索引位置为`null`（空）：直接将对象 A 存入该位置。
     - 若索引位置不为`null`
     - 先调用`equals()`方法比较 “现有数据” 与 “对象 A” 的内容：
       - 若`equals()`返回`true`：判定为重复数据，**不存储**。
       - 若`equals()`返回`false`：发生哈希碰撞，将对象 A 以 “链表” 形式挂载 ——JDK 1.8 之前采用 “新元素占数组位置，老元素挂下方” 的方式，最终形成链表。

  3. 当哈希表中元素总数超过 “数组长度 × 加载因子”（16×0.75=12）时，触发扩容：数组长度扩容为原来的**2 倍**（16→32→64...），重新计算所有现有元素的索引，分散到新数组中 —— 避免数组过度拥挤、链表过长导致查询变慢。

-  JDK 8 及之后：数组 + 链表 + 红黑树的优化逻辑

![image-20251026121726047](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251026121726047.png)

JDK 8 针对 “链表过长导致查询效率低” 的问题，引入**红黑树**（一种自平衡的二叉排序树），核心规则如下：

- 红黑树转换条件（需同时满足）：
  - 某索引位置的链表长度**超过 8**；
  - 哈希表数组长度 **≥64**。

- 红黑树的优势：红黑树基于 “二分查找” 逻辑（小值左放、大值右放），查询时间复杂度降至`O(log n)`，大幅提升长链表的查询性能。

3. HashSet 的性能优势与局限性

- 性能优势：增删改查效率高
  - 查：通过哈希值快速定位索引（数组索引定位`O(1)`），红黑树查询`O(log n)`；
  - 增 / 删：定位索引后，链表 / 红黑树的增删操作效率高于普通数组 / 链表。
- 局限性
  - 耗内存：数组可能存在空位置，红黑树需额外存储左右子节点地址；
  - 无索引：无法通过索引操作（如`get(int index)`）；
  - 不重复：无法存储重复数据（需去重场景适用，需保留重复则不适用）。

4. HashSet 默认去重的局限性

   - **默认去重逻辑**：HashSet 底层通过 “哈希值（hashCode）+ 内容比较（equals）” 判断重复，但默认情况下（未重写`hashCode()`和`equals()`时），判断依据是**对象的内存地址**（源自`Object`类的默认实现）。
   - **局限性场景**：当存储new自定义对象（如`Student`）时，即使两个对象的**成员变量值完全相同**（如姓名、年龄、地址一致），由于它们是不同的对象（内存地址不同），HashSet 会认为是 “不同对象”，无法自动去重。

   - 自定义对象去重的核心原理

   要实现 “成员变量值相同即视为重复对象”，需满足 HashSet 的双重判断标准，必须同时重写自定义类的`hashCode()`和`equals()`方法：

   | 判断步骤                     | 核心作用                                        | 实现要求                                                |
   | ---------------------------- | ----------------------------------------------- | ------------------------------------------------------- |
   | 1. 计算哈希值（hashCode ()） | 确定对象在 HashSet 底层数组中的存储位置         | 保证**成员变量值相同的对象**，返回相同的哈希值          |
   | 2. 内容比较（equals ()）     | 避免 “哈希碰撞”（不同对象哈希值相同）导致的误判 | 保证**成员变量值相同的对象**返回`true`，否则返回`false` |

   > 关键结论：仅重写其中一个方法无效。若只重写`hashCode()`，可能出现 “哈希值相同但内容不同” 的对象被误判为重复；若只重写`equals()`，不同对象的哈希值不同，无法进入内容比较环节，仍无法去重。



8.2 LinkedHashSet

1. 底层实现原理：哈希表 + 双链表

LinkedHashSet 的底层本质是**基于哈希表（数组 + 链表 / 红黑树）实现，但额外通过双链表维护插入顺序**，解决了普通哈希表 “无序” 的问题

2. 顺序维护：双链表（保证有序）

普通哈希表的 “无序” 源于元素存储位置由哈希值决定，与插入顺序无关；LinkedHashSet 通过**额外的双链表机制**记录插入顺序，具体逻辑如下：

- 双链表结构：每个元素节点除了存储自身数据、哈希值、哈希表内的链表指针（`next`，用于解决哈希冲突），还额外维护两个指针：
  - `before`：指向**前一个插入的元素**；
  - `after`：指向**后一个插入的元素**；
- 首尾指针管理：LinkedHashSet 内部维护两个成员变量`head`（头指针）和`tail`（尾指针）：
  - 插入第一个元素时，`head`和`tail`均指向该元素；
  - 插入后续元素时，先通过`tail`找到最后一个插入的元素，将该元素的`after`指针指向新元素，同时将新元素的`before`指针指向`tail`，最后更新`tail`为新元素；
- **遍历逻辑**：遍历 LinkedHashSet 时，不直接遍历哈希表数组，而是从`head`指针出发，顺着`after`指针依次访问元素，最终保证遍历顺序与插入顺序一致。



8.3 TreeSet

1. 特点：不重复、无索引、可排序（默认升序排序，按照元素的大小，由小到大排序）,底层是基于红黑树实现的排序。

- 对于数值类型：Integer，Double，默认按照数值本身的大小进行升序排序。
  对于字符串类型：默认按照首字符的编号升序排序。
  对于自定义类型如Student对象，TreeSet默认是无法直接排序的。

2. 自定义对象排序

TreeSet 无法直接对自定义对象（如`Teacher`、`Student`）排序，因为它 “不知道排序规则”，会抛出`ClassCastException`。需通过以下两种方案指定排序规则：

- **方案 1：让自定义类实现`Comparable`接口（“对象自带规则”）**

  - 自定义类（如`Teacher`）实现`Comparable<Teacher>`接口（泛型指定比较的对象类型）。

  - 重写`compareTo(Teacher o)`方法，在方法内定义排序规则。

- **`compareTo`方法规则（Java 官方约定）**

  - **返回正整数**：表示当前对象（`this`）比目标对象（`o`）大，TreeSet 会将当前对象放右侧。

  - **返回负整数**：表示当前对象比目标对象小，TreeSet 会将当前对象放左侧。

  - **返回 0**：表示两个对象 “相等”，TreeSet 会过滤当前对象（去重）。

```java
//代码示例（按 Teacher 年龄升序）
public class Teacher implements Comparable<Teacher> {
    private String name;
    private int age;
    private double salary;

    // 构造器、getter/setter、toString()省略

    // 重写compareTo：按年龄升序
    @Override
    public int compareTo(Teacher o) {
        // 方案1：手动判断（适合理解逻辑）
        if (this.age > o.getAge()) {
            return 1; // 当前对象大，返回正整数
        } else if (this.age < o.getAge()) {
            return -1; // 当前对象小，返回负整数
        } else {
            return 0; // 年龄相等，视为重复
        }

        // 方案2：简化写法（数值类型可直接做差）
        // return this.age - o.getAge(); // 升序（若要降序，改为 o.getAge() - this.age）
    }
}
```



**方案 2：创建 TreeSet 时传入`Comparator`比较器（“集合指定规则”）**

- 无需修改自定义类，在创建`TreeSet`对象时，通过构造器传入`Comparator<Teacher>`比较器（匿名内部类或 Lambda 表达式），在`compare(Teacher o1, Teacher o2)`方法中定义规则。

- `compare`方法规则（与`compareTo`逻辑一致）

  - **返回正整数**：`o1`比`o2`大，`o1`放右侧。

  - **返回负整数**：`o1`比`o2`小，`o1`放左侧。

  - **返回 0**：`o1`与`o2`相等，去重。

```java
// 方式1：匿名内部类
TreeSet<Teacher> treeSet = new TreeSet<>(new Comparator<Teacher>() {
    @Override
    public int compare(Teacher o1, Teacher o2) {
        // 按薪水降序（o2薪水 - o1薪水，或用Double.compare反转参数）
        return Double.compare(o2.getSalary(), o1.getSalary());
    }
});

// 方式2：Lambda表达式（Java 8+简化写法）
TreeSet<Teacher> treeSet = new TreeSet<>(
    (o1, o2) -> Double.compare(o2.getSalary(), o1.getSalary()) // 薪水降序
);
```

### 九、Map集合

1. 概念

Map 集合是 Java 中**双列集合**（键值对集合）的顶层体系，与`Collection`（单列集合）是 “平等关系”（类比 “玉皇大帝体系与如来佛祖体系”），不属于`Collection`家族，

2. 特点：
   - 以 “键（Key）- 值（Value）” 对为单位存储，1 个键值对视为 1 个元素（如`嫦娥=28`是 1 个元素，而非 2 个）；
   - 所有键（Key）不允许重复（需唯一），若添加重复键，新值会覆盖旧值；值（Value）可以重复，无唯一性要求；
   - 键值对应关系：键与值是 “一对一” 绑定，每个键只能对应 1 个值，无法跨键映射。

3. Map 集合的体系结构与常用实现类

Map 是顶层接口（不能直接创建对象），需通过实现类使用，核心实现类及特点如下（**特点由 “键” 决定**，与 Set 集合体系高度相似）：

| 实现类          | 核心特点（由键决定）                                    | 适用场景                       |
| --------------- | ------------------------------------------------------- | ------------------------------ |
| `HashMap`       | 无序、键不重复、无索引；键 / 值可存`null`               | 日常开发中最常用（查询效率高） |
| `LinkedHashMap` | 有序（按插入顺序）、键不重复、无索引；键 / 值可存`null` | 需要保留插入顺序的场景         |
| `TreeMap`       | 有序（按键的自然顺序升序排列）、键不重复、无索引        | 需要对键进行排序的场景         |

4. Map 集合的常用方法（基于顶层 Map 接口，所有实现类通用

| 方法名                                | 功能描述                                            | 关键说明                                       |
| ------------------------------------- | --------------------------------------------------- | ---------------------------------------------- |
| `V put(K key, V value)`               | 添加 / 修改键值对：键不存在则新增，键存在则覆盖旧值 | 返回被覆盖的旧值（若为新增则返回`null`）       |
| `V get(Object key)`                   | 根据键获取对应的值                                  | 键不存在时返回`null`                           |
| `V remove(Object key)`                | 根据键删除整个键值对                                | 返回被删除的键对应的值（键不存在则返回`null`） |
| `boolean containsKey(Object key)`     | 判断集合是否包含指定键                              | 包含返回`true`，否则`false`                    |
| `boolean containsValue(Object value)` | 判断集合是否包含指定值                              | 包含返回`true`，否则`false`                    |
| `Set<K> keySet()`                     | 获取集合中所有的键，返回`Set`集合                   | 因键唯一，用`Set`存储（符合 Set 特性）         |
| `Collection<V> values()`              | 获取集合中所有的值，返回`Collection`集合            | 因值可重复，用`Collection`存储（不去重）       |
| `int size()`                          | 获取键值对的个数（元素个数）                        | 1 个键值对算 1 个元素                          |
| `void clear()`                        | 清空集合中所有键值对                                | 清空后集合为空（`isEmpty()`返回`true`）        |
| `boolean isEmpty()`                   | 判断集合是否为空（无键值对）                        | 空返回`true`，否则`false`                      |

```java
// 1. 创建HashMap对象（多态写法，开发常用）
Map<String, Integer> map = new HashMap<>();

// 2. 新增键值对
map.put("嫦娥", 20); // 新增，返回null
map.put("嫦娥", 28); // 键重复，覆盖旧值，返回20
map.put(null,null);//键值对数据可以都是null

// 3. 查：根据键取值
Integer age = map.get("嫦娥"); // 返回28
Integer nullAge = map.get("嫦娥二"); // 键不存在，返回null

// 4. 删：根据键删除
Integer removedAge = map.remove("嫦娥"); // 删除"嫦娥=28"，返回28

// 5. 获取所有键/值
Set<String> keys = map.keySet(); // 所有键的Set集合
Collection<Integer> values = map.values(); // 所有值的Collection集合
```

### 十、Map集合的遍历方式

**遍历方式 1：键找值（Key -> Value）**

先获取 Map 集合中所有的**键（Key）**，再通过 “键” 反向获取对应的 “值（Value）”，适合仅需通过键定位值的场景，逻辑简单易理解。

```java
// 1. 创建并初始化Map集合
Map<String, Integer> map = new HashMap<>();
map.put("嫦娥", 28);
map.put("铁扇公主", 35);
map.put("玉兔", 3);
map.put("牛魔王", 45);

// 2. 步骤1：获取所有键的Set集合
Set<String> keySet = map.keySet();

// 3. 步骤2：遍历键的Set集合（增强for循环）
for (String key : keySet) {
    // 4. 步骤3：通过键找值
    Integer value = map.get(key);
    // 5. 输出键值对
    System.out.println("Key: " + key + ", Value: " + value);
}
```

**遍历方式 2：键值对整体遍历（EntrySet）**

1. 核心思想

将 Map 集合中的每个`Key-Value`键值对，封装成一个**Entry 对象**（Entry 是 Map 的内部接口，代表 “键值对实体”），再将所有 Entry 对象存入 Set 集合，遍历 Set 即可一次性获取键和值，适合需同时操作键和值的场景

2. 概念与 API

- Entry 接口：Map 的内部接口，包含两个核心方法：
  - `K getKey()`：获取 Entry 对象中的 “键”；
  - `V getValue()`：获取 Entry 对象中的 “值”；
- `Set<Map.Entry<K,V>> entrySet()`：将 Map 集合中的所有键值对封装为 Entry 对象，存入 Set 集合并返回。

```java
// 1. 创建并初始化Map集合（同前）
Map<String, Integer> map = new HashMap<>();
map.put("嫦娥", 28);
map.put("铁扇公主", 35);
map.put("玉兔", 3);
map.put("牛魔王", 45);

// 2. 步骤1：将Map转为存储Entry对象的Set集合
Set<Map.Entry<String, Integer>> entrySet = map.entrySet();

// 3. 步骤2：遍历EntrySet集合（增强for循环）
for (Map.Entry<String, Integer> entry : entrySet) {
    String key = entry.getKey();
    Integer value = entry.getValue();

    System.out.println("Key: " + key + ", Value: " + value);
}
```

**遍历方式3：Lambda表达式**

1. 思想：Lambda 遍历 Map 的核心是调用 Map 集合提供的 **`forEach`方法 **（JDK8 新增），该方法需接收一个`BiConsumer`类型的参数，用于 “回调处理” 每一组键值对（Key-Value）。

2. `BiConsumer`接口：一个函数式接口（带`@FunctionalInterface`注解），仅包含一个抽象方法`accept(T t, U u)`，其中：

   - `T t`：对应 Map 中的**键（Key）**，类型与 Map 的键类型一致（如`String`）；
   - `U u`：对应 Map 中的**值（Value）**，类型与 Map 的值类型一致（如`Integer`）。

   - **作用**：`accept`方法用于定义 “如何处理每一组键值对”（如打印、计算等），由底层遍历逻辑自动回调。

```java
// 1. 准备一个Map集合（示例：键为String，值为Integer）
Map<String, Integer> map = new HashMap<>();
map.put("嫦娥", 28);
map.put("铁扇公主", 38);

// 2. 调用Map的forEach方法，传入BiConsumer匿名内部类
map.forEach(new BiConsumer<String, Integer>() {
    // 重写accept方法：处理每一组键值对
    @Override
    public void accept(String key, Integer value) {
        System.out.println("键：" + key + "，值：" + value); // 自定义处理逻辑
    }
});

// Lambda简化匿名内部类，直接定义accept方法的逻辑
map.forEach((key, value) -> System.out.println("键：" + key + "，值：" + value));

// 进一步简化参数名（只要语义清晰，参数名可自定义，如k、v）
map.forEach((k, v) -> System.out.println("k=" + k + ", v=" + v));
```

### 十一、Stream流

11.1 概念

- **本质**：JDK 8 新增的一套 API（位于 `java.util.stream.*`），用于简化集合、数组的数据操作。
- **核心关联**：大量结合函数式编程风格，主要通过 Lambda 表达式实现，是项目开发中的高频重点技术。

2. 核心优势

| 优势维度 |                           具体说明                           |
| :------: | :----------------------------------------------------------: |
| 功能强大 | 提供筛选、过滤、分析、类似 SQL 风格的操作等丰富 API，覆盖多种数据处理场景 |
| 性能高效 | 支持利用 CPU 并发特性（，底层有优化机制，比传统单线程循环更高效 |
| 代码简洁 | 基于 Lambda 表达式，避免传统多层循环（for+if 嵌套），一行代码可完成复杂处理 |
| 可读性好 |       代码直接体现 “做什么”，而非 “怎么做”，逻辑更直观       |

3. 流程

- 准备数据流：必须先有可操作的数据，包括两类：

  - 集合（如 List、Set、Map 等）

  - 数组（如 String []、int [] 等）

- 获取 Stream 流
  - stream流本身是一个接口:`public interface Stream<T> ...{}`,所以要拿Stream的实现类去使用
  - 不同数据源的获取方式：

|      数据源类型      |                         具体获取方法                         |                           示例代码                           |
| :------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| 单列集合（List/Set） |                  调用集合的 `stream()` 方法                  | `List<String> list = new ArrayList<>(); Stream<String> stream = list.stream();` |
|  Map 集合（键值对）  |   需先拆分为 “键流”“值流” 或 “键值对流”，再调用 `stream()`   | 1. 键流：`map.keySet().stream()`<br>2. 值流：`map.values().stream()`<br>3. 键值对流：`map.entrySet().stream()` |
|         数组         | 1. `Arrays.stream(数组)`<br>2. `Stream.of(数组)`（支持可变参数，可直接传多个元素） | 1. `String[] arr = {"a","b"}; Stream<String> s1 = Arrays.stream(arr);`<br>2. `Stream<String> s2 = Stream.of(arr);` 或 `Stream.of("a","b");` |

> 注意：Stream 是接口，不能直接 new，需通过上述方法获取其实现类对象。

```java
public class StreamDemo {
    public static void main(String[] args) {
        Collection<String> List=new ArrayList<>();
        Stream<String> s1=List.stream();

        Map<String,Integer> map=new HashMap<>();
        Stream<String> s2 = map.keySet().stream();//键流
        Stream<Integer> s3 = map.values().stream();//值流
        Stream<Map.Entry<String,Integer>> s4 = map.entrySet().stream();
        //键值对流

        String[] names = {"张三丰", "张无忌", "张翠山", "张良", "张学友"};
        Stream<String> s5 = Arrays.stream(names);

        Stream<String> s6=Stream.of(names);
        Stream<String> s7 = Stream.of("张三丰", "张无忌", "张翠山", "张良", "张学友");
    }
}
```



- 流操作与结果获取
  - 两类操作区分
    - 中间操作（如过滤、筛选）：返回新的 Stream 流，支持链式调用（如 “筛选姓张”→“筛选三个字”）。
    - 终结操作（如收集、统计）：触发流的实际执行（Stream 流是 “惰性执行”，无终结操作则中间操作不生效），返回最终结果。

```java
// 传统方式：需新建集合+循环+判断
List<String> oldList = new ArrayList<>();
for (String name : list) {
    if (name.startsWith("张") && name.length() == 3) {
        oldList.add(name);
    }
}

// Stream流方式：链式调用+终结操作
List<String> newList = list.stream()
                        .filter(name -> name.startsWith("张")) // 中间操作：筛选姓张
                        .filter(name -> name.length() == 3)   // 中间操作：筛选三个字
                        .collect(Collectors.toList());        // 终结操作：收集到新List
```

11.2 中间方法

1. 核心特性

中间方法是 Stream 流中用于 “加工数据流” 的操作，其核心为:

- **返回新流**：调用中间方法后，不会直接处理数据或输出结果，而是返回一个承载 “处理后数据” 的新 Stream 流；
- **支持链式编程**：由于返回新流，可连续调用多个中间方法，形成 “流水线式” 数据处理逻辑，简化代码结构；
- **惰性执行**：中间方法仅定义处理规则，不会立即执行，需配合后续 “终结方法”（如`forEach`）才会触发实际数据处理。

2. 常见中间方法

|            方法名            |                           功能描述                           |                           关键细节                           | 代码案例（以分数集合`List<Double> scores = Arrays.asList(99.0, 88.0, 77.0, 66.0, 88.0)`为例） |          |                                                              |
| :--------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
|     `filter(Predicate)`      | 筛选数据：根据 Predicate 接口的条件（true/false）保留符合要求的数据 |      条件可通过 Lambda 表达式简化，支持多条件（用`&&`/`      |                                                              | ` 连接） | // 筛选分数 > 80 的数据`scores.stream().filter(s -> s > 80).forEach(System.out::println);` |
|          `sorted()`          | 排序数据：默认按 “自然顺序”（如数字升序、字符串字典序）排序  | 若需自定义排序（如降序），用重载方法`sorted(Comparator)`，通过 Lambda 指定规则 | // 降序排序`scores.stream().sorted((s1, s2) -> Double.compare(s2, s1)).forEach(System.out::println);` |          |                                                              |
|       `limit(long n)`        |   截取数据：保留流中前 n 个元素，若流长度不足 n 则保留全部   | 参数 n 必须为非负整数，常用于 “取 Top N” 场景（如取前 2 名高分） | // 取前 2 个高分`scores.stream().sorted((s1,s2)->Double.compare(s2,s1)).limit(2).forEach(System.out::println);` |          |                                                              |
|        `skip(long n)`        |    跳过数据：跳过流中前 n 个元素，返回剩余元素组成的新流     | 参数 n 为非负整数，若 n≥流长度，返回空流；常用于 “排除前 N 个数据” 场景 | // 跳过前 2 个数据，取剩余`scores.stream().skip(2).forEach(System.out::println);` |          |                                                              |
|         `distinct()`         |                 去重数据：去除流中重复的元素                 | 去重规则依赖元素的`hashCode()`和`equals()`方法：- 基本类型包装类（如 Double、Integer）已重写这两个方法，可直接去重；- 自定义对象（如 Student）需手动重写`hashCode()`和`equals()`，否则按 “对象地址” 去重 | // 去除重复分数`scores.stream().distinct().forEach(System.out::println);` |          |                                                              |
|       `map(Function)`        | 加工数据（映射）：将流中每个元素按 Function 规则 “转换为新元素”，改变数据类型 |   核心是 “数据转换”，如将数字转为字符串、对象转为其属性值    | // 给每个分数加 10 分，并转为 “加分后：XX” 的字符串`scores.stream().map(s -> "加分后：" + (s + 10)).forEach(System.out::println);` |          |                                                              |
| `concat(Stream a, Stream b)` | 合并流：将两个 Stream 流合并为一个新流，需用`Stream.concat()`静态方法调用 | 合并后流的元素类型为 “两个流的共同父类型”：- 若两个流类型相同（如均为 String），合并后类型不变；- 若类型不同（如 String+Integer），合并后类型为`Object` | // 合并字符串流和整数流`Stream<String> s1 = Stream.of("张三", "李四");``Stream<Integer> s2 = Stream.of(1, 2);``Stream<Object> merged = Stream.concat(s1, s2);` |          |                                                              |

- 链式编程逻辑：多个中间方法可串联使用，例如 “去重→排序→取前 2”

```java
scores.stream()
      .distinct() // 先去重
      .sorted((s1,s2)->Double.compare(s2,s1)) // 再降序
      .limit(2) // 最后取前2
      .forEach(System.out::println); // 终结方法触发执行
```

11.3 终结方法

1.概念

- **定义**：Stream 流的终结方法用于获取处理后的结果（如遍历、统计、收集数据），是流操作的 “收尾步骤”。
- **关键特性**：**流的一次性**—— 终结方法调用后，流会 “关闭”（底层可理解为 “索引已走到数据末尾”），无法再次使用，否则会抛出`IllegalStateException`（流已被操作或关闭）。

2. 基础终结方法

| 方法名                       | 功能描述                                                     | 返回值类型    | 关键注意点                                                   |
| ---------------------------- | ------------------------------------------------------------ | ------------- | ------------------------------------------------------------ |
| `forEach(Consumer action)`   | 遍历流中的每个元素，执行自定义操作（如打印）                 | `void`        | 最常用的遍历方式，无返回值，直接消费数据                     |
| `count()`                    | 统计流中符合条件的元素个数                                   | `long`        | 直接返回数值，无需额外处理，适合简单计数场景                 |
| `max(Comparator comparator)` | 根据自定义比较规则，获取流中元素的 “最大值”（如最高薪水的老师） | `Optional<T>` | 1. 需传入`Comparator`指定比较维度（如薪水、年龄）；2. 返回`Optional<T>`容器（避免返回`null`导致空指针异常），需用`get()`方法提取元素 |
| `min(Comparator comparator)` | 根据自定义比较规则，获取流中元素的 “最小值”（如最低薪水的老师） | `Optional<T>` | 与`max`用法完全一致，仅结果为 “最小值”                       |

>Java 8 为所有集合添加了一个新的方法 *forEach()*，该方法以只读形式遍历集合所有的元素并为每一个元素执行一个动作。
>
>```java
>import java.util.Arrays;
>public class ForEachTest {
>   public static void main(String[] args) {
>       Arrays.asList("你好", "二哥！", "我是ForEach。").forEach(System.out::println);
>   }
>}
>```
>
>
>
>在这个示例中，*forEach()* 方法使用方法引用 *System.out::println* 来打印每个元素。
>
>**注意事项**：
>
>- **foreach** 循环不能修改集合中的元素。
>- **foreach** 循环不能获取当前元素的索引。
>- 在需要索引或需要修改集合元素时，应使用传统的 *for* 循环。



3. 核心终结方法：Stream 流的收集操作（`collect()`）

- 开发中，Stream 流处理数据后，需将结果**恢复为集合或数组**（流不适合存储数据，他人 / 下游接口通常接收集合 / 数组）

- 核心方法是`collect(Collector collector)`，通过`Collectors`工具类指定收集目标类型。

  **收集到 List 集合（允许重复元素）**

- 用法：`Collectors.toList()`，底层返回`ArrayList`（无需关注源码，只需知道结果类型）。

  **收集到 Set 集合（去重）**

- 用法：`Collectors.toSet()`，底层返回`HashSet`（无序、去重）。
- 场景：需自动去除重复元素时使用（如收集姓张的老师，避免重复）。

```java
Stream<String> s2=list.stream().filter(s->s.startsWith("张"));
Set>String> set=s2.collect(Collectors.toSet());
System.out.println(set);
```

- 注意：**流只能收集一次**，若已收集到 List，再用同一流收集到 Set 会报错（流已关闭），需重新获取流。

  **收集到数组（`toArray()`）**

- 用法：直接调用`toArray()`，返回`Object[]`（因流中元素类型不确定，默认返回 Object 数组）。
- 场景：需将结果传给仅支持数组的接口时使用。

```java
// 收集姓张的老师到Object数组
Object[] zhangTeacherArray = teachers.stream()
                                     .filter(t -> t.getName().startsWith("张"))
                                     .toArray();
// 结果：Object数组，元素为“张三”（需强制转换为Teacher类型使用）
```

  **收集到 Map 集合（键值对存储）**

- 用法：`Collectors.toMap(Function keyMapper, Function valueMapper)`，需指定两个规则：
  1. `keyMapper`：提取 Map 的 “键”（如老师的姓名，需保证键唯一，否则抛`IllegalStateException`）；
  2. `valueMapper`：提取 Map 的 “值”（如老师的薪水、年龄或整个老师对象）。
- 场景：需按 “键值对” 存储数据时使用（如 “老师姓名→薪水” 的映射）。

```java
// 收集老师到Map：键=姓名，值=薪水（保证姓名唯一）
Map<String, Double> map = teachers.stream()                                            .collect(Collectors.toMap(Teacher::getName,Teacher::getSalary));
// 结果：{张三=5000.0, 白眉鹰王=108000.0, 陈坤=48000.0}
```

- 关键注意点：若键重复（如两个 “张三”），需额外指定冲突处理规则（如`Collectors.toMap(key, value, (v1, v2) -> v1)`，保留第一个值），否则会报错。

### 十二 Collections工具类

1. 类的定位与特点

- **作用**：Java 提供的**集合操作工具类**（对应`java.util.Collections`），专门用于辅助操作`Collection`体系集合（如`List`、`Set`）。
- **核心特性**：所有方法均为**静态方法**，无需创建对象，直接通过`类名.方法名`调用（类似`Arrays`工具类）。

2. 核心常用方法

| 方法功能           | 关键说明                                                     |
| ------------------ | ------------------------------------------------------------ |
| 批量添加元素       | `addAll(Collection<? super T> c, T... elements)`：一次性向集合添加多个元素，底层依赖可变参数，替代多次调用`add()`。<br>`Collections.addAll(list,"abc","cww","ccd");` |
| 打乱 List 集合顺序 | `shuffle(List<?> list)`：仅支持`List`（因`Set`本身无序，`List`基于数组有索引可交换元素），底层通过随机索引交换实现打乱。 |
| 排序 List 集合     | `sort(List<T> list)`：需集合元素实现`Comparable`接口（自定义排序规则）；`sort(List<T> list, Comparator<? super T> c)`：直接传入比较器，灵活指定排序规则（如自定义对象排序）。 |

- **排序前提**：调用无参`sort()`时，集合元素必须实现`Comparable`接口（否则抛出`ClassCastException`）；若元素无默认排序规则，优先使用带比较器的`sort()`方法。























































