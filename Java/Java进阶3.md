# Java进阶

## 第一章 final关键字

### 第一节 final的用法

#### 一、概念

- `final`在 Java 中意为 “最终的”，是一个关键字，可修饰**类**、**方法**和**变量**，修饰后会限制其可变性，核心作用是 “锁定” 目标，防止意外修改。

#### 二、final 修饰类

- **核心规则**：被`final`修饰的类称为 “最终类”，**不能被其他类继承**

```java
  // 被final修饰的类A（最终类）
  final class A {}
  
  class B extends A {} 
  // 错误：类B无法继承final类A，编译报错“cannot inherit from final A”
```

- 典型应用场景：工具类（如 Java 内置的`java.lang.Math`类）

  工具类的设计目的是 “通过类名直接调用静态方法”（无需创建对象，更无需继承），用`final`修饰可明确其 “不可继承” 的设计意图，避免子类破坏工具类的功能完整性，同时体现代码专业性。

- **注意**：日常开发中自定义类很少用`final`，仅在明确 “禁止继承” 时使用（如工具类、核心 API 类）。

#### 三、final 修饰方法

- **核心规则**：被`final`修饰的方法称为 “最终方法”，**子类不能重写该方法**（子类可调用，但不能修改方法实现）。

```java
  class C {
      // 被final修饰的方法show（最终方法）
      final public void show() {
          System.out.println("父类方法");
      }
  }
  class D extends C {
      // 错误：子类D无法重写final方法show，编译报错
      @Override
      public void show() {} 
  }
```

- 应用场景

  父类中 “核心逻辑不可修改” 的方法（如支付流程、权限校验等关键方法），用`final`防止子类重写后破坏核心逻辑。

#### 四、final 修饰变量

- **核心规则**：被`final`修饰的变量**有且仅能赋值一次**（赋值后不可修改），且必须保证声明的同时必须赋值（初始化）。
- 变量按作用域分为 “局部变量” 和 “成员变量”（分为静态成员变量和实例成员变量）

1. final 修饰局部变量  

- **定义**：方法内、代码块内声明的变量（如方法参数、循环变量）。
- **核心规则**：赋值后不可修改，赋值时机灵活（声明时赋值 / 后续代码赋值，但仅能一次）。

```java
  public static void main(String[] args) {
      // 场景1：声明时直接赋值（推荐，清晰）
      final double PI = 3.14;
      // 错误：PI已赋值，无法二次修改
      PI = 3.1415; 
      
      // 场景2：方法参数用final修饰（防止方法内修改参数值）
      buy(0.8); // 传入“八折”折扣
  }

  // final修饰参数discount，方法内无法修改折扣值
  public static void buy(final double discount) {
      discount = 0.5; // 错误：discount是final参数，不能修改
      System.out.println("折扣：" + discount);
  }
```

- 应用场景

  - 存储固定值（如圆周率`PI`、税率），防止代码中意外修改；
  - 修饰方法参数（如折扣、订单 ID），确保参数值在方法内不被篡改，保证业务逻辑正确性。

2. final 修饰成员变量

- **定义**：类中声明的变量`final`修饰后规则差异较大：

| 成员变量类型 |                           核心规则                           |     应用价值     |
| :----------: | :----------------------------------------------------------: | :--------------: |
| 实例成员变量 | 必须赋值（声明时赋值 / 构造器赋值），但**所有对象共享同一初始值且不可修改** |  几乎无实际意义  |
| 静态成员变量 | 必须赋值（声明时赋值 / 静态代码块赋值），**全局唯一且不可修改** | 极高（定义常量） |

- 关键说明：静态成员变量用`final`修饰是核心场景：静态变量属于 “类”（全局唯一），用`final`固定后成为 常量，可存储系统全局固定值（如项目名、配置信息），之后不允许修改。



3. final 修饰变量的特殊注意：基本类型 vs 引用类型

- **修饰基本类型变量**：变量存储的 “数据值” 不可修改（如`final int a = 20`，`a`的值永远是 20）；

- **修饰引用类型变量**：变量存储的 “地址值” 不可修改（即不能指向新对象），但**地址指向的对象内容可修改**（如数组、自定义对象）。

- 代码演示

```java
  public static void main(String[] args) {
      // final修饰引用类型变量（数组）
      final int[] arr = {10, 20, 30};
      
      // 1. 错误：不能修改地址值（不能指向新数组）
      arr = new int[]{40, 50}; 
      
      // 2. 正确：可以修改数组内容（地址未变，仅对象内部数据变）
      arr[0] = 99; 
      System.out.println(arr[0]); // 输出99
  }
```

  

### 第二节 Java 常量

常量是`final`关键字的核心应用场景，也是 Java 项目开发中高频使用的特性，本质是 “`static`+`final`修饰的静态成员变量”。

1. 常量的定义与规则

- 语法格式：`public static final 数据类型 常量名 = 初始值;`
- **命名规范**：常量名必须**全部大写**，多个单词用 “下划线`_`” 连接（如`SYSTEM_NAME`、`MAX_AGE`），这是 Java 行业通用规范，体现常量的 “全局固定” 特性。
- **赋值规则**：必须在 “声明时” 或 “静态代码块中” 赋值（仅一次），且赋值后不可修改。

2. 常量的核心作用：存储系统配置信息

   常量的设计目的是 “集中管理系统中全局固定的值”核心优势如下：

   - **便于维护**：若需修改值（如项目名从 “黑马智能家居” 改为 “黑马 AI 智能家居”），仅需修改常量定义处，所有引用处自动生效，避免漏改导致的 bug；
   - **性能无损耗**：Java 编译时会对常量进行 **“宏替换”**—— 将所有引用常量的地方，直接替换为常量的实际值（如`System.out.println(SYSTEM_NAME)`编译后变为`System.out.println("黑马智能家居")`），性能与直接用字面量完全一致。

3. 常量的项目实践：单独的常量类 / 常量包

​	企业开发中，常量不会零散定义在业务类中，而是**集中放在单独的常量包（如`com.xxx.constant`）或常量类（如`SystemConstant`）** 中，便于统一管理和团队复用。

```java
// 常量类：集中定义系统常量
package com.itheima.constant;
public class SystemConstant {
    // 项目名称常量
    public static final String SYSTEM_NAME = "黑马程序员智能家居系统";
    // 数据库连接地址常量（示例）
    public static final String DB_URL = "jdbc:mysql://localhost:3306/home_db";
    // 最大并发数常量（示例）
    public static final int MAX_THREAD = 10;
}

// 业务类：引用常量
package com.itheima.service;
import com.itheima.constant.SystemConstant;
public class HomeService {
    public void showSystemInfo() {
        // 直接通过“常量类.常量名”引用，无需重复写字面量
        System.out.println("当前系统："+SystemConstant.SYSTEM_NAME);
    }
}
```



## 第二章 单例类（设计模式）

### 一、设计模式基础

1. 设计模式的定义

- 本质：解决特定问题的**最优解决方案**（前人总结的成熟经验）。
- 数量：软件领域共**23 种经典设计模式**，覆盖大部分开发场景。

2. 学习设计模式的核心目标

只需掌握两点，即可应对所有设计模式学习：

1. **解决什么问题**：明确该模式的应用场景（什么时候用）；
2. **怎么实现**：掌握代码编写逻辑（怎么写）。

### 二、单例设计模式核心

1. 解决的问题

单例类（single instance），确保**某个类在整个程序中只能创建 1 个对象**，避免对象重复创建导致的内存浪费、资源冲突（如任务管理器、Java 虚拟机对象，只需 1 个即可满足需求）。

2. 单例设计的核心原则

- 对外 “堵死” 直接创建对象的入口（禁止外部通过`new`创建对象）；
- 对内 “管控” 对象创建逻辑（确保只创建 1 个对象）；
- 对外 “提供” 唯一对象的获取方式（让外部能拿到这 1 个对象）。

3. 实现方式（重点）

**私有构造器**（堵死外部创建入口）、定义一个**静态变量**（存储唯一对象）、定义一个**静态方法**（提供对象获取入口）

```java
/* 1. 饿汉式单例（提前创建对象）
核心特点：类加载时就创建对象（只要程序加载该类，对象就已存在）；*/

public class A {
	// 2.定义一个类变量记住类的一个对象
	private static A a = new A(); //使用private私有对象，防止外部修改，也可以再用final关键字修饰
    
	// 1.私有构造器
	private A() {}
    
    //3.定义一个类方法返回对象
	public static A getObject(){
		return a;
	}
}

/*2. 懒汉式单例（延迟创建对象）
核心特点：第一次获取对象时才创建（用的时候再做，不用不创建）；*/

public class B {
	//2、私有化静态变量
	private static B b;
    
	//1、私有化构造器
	private B(){}

    //3、提供静态方法返回对象
	public static B instance(){
	if(b == null){ b = new B(); }
	return b;
}
```

4. 应用场景

- 优先用饿汉式：对象占用内存小、程序启动后大概率会用到该对象（如配置类、工具类）；
- 优先用懒汉式：对象占用内存大、程序启动后可能用不到该对象（如大型服务类、按需加载的组件）



## 第三章 枚举类

### 一、枚举类的基础定义与定位

- **本质属性**：枚举类（Enum Class）是 Java 中的**特殊类**，专门用于定义 “有限个、确定的常量对象”（如季节、性别、状态等），是 “多例设计模式” 的典型实现。

### 二、枚举类的语法规则（核心）

1. 基础语法结构

- **关键字**：用 `enum` 声明枚举类（替代普通类的 `class` 关键字）。
- **修饰符**：通常用 `public` 修饰，确保其他类可访问。
- **第一行强制规则**：枚举类的**第一行必须罗列枚举对象的名称**，且只能写枚举对象名，写其他内容会报错。这些名称，本质是常量，每个常量都记住了枚举类的一个对象。

```java
// 枚举类定义
public enum A {
    // 第一行：枚举对象名称（本质是常量）
    X, Y, Z; 
    
    // 第二行后：可定义其他成员（示例）
    private String desc; // 成员变量
    
    // 构造器（默认私有，不可手动改为public）
    private A() {} 
    private A(String desc) { this.desc = desc; }
    
    // 成员方法
    public String getDesc() { return desc; }
}
```

2. 枚举对象的特性

- **本质是常量**：枚举对象名称（如 X、Y、Z）是 `public static final` 修饰的常量，不可修改。
- **关联枚举类对象**：每个枚举对象本质是 “枚举类的实例”（如 `X` 等价于 `new A()`，由 JVM 自动创建）。
- **不可新增对象**：枚举类对外无法创建新对象（构造器默认私有，手动写构造器也必须是 `private`），只能使用第一行定义的有限对象。

3. 枚举类的底层本质（反编译）

通过 `javap` 命令反编译枚举类的 `.class` 文件，可发现其底层特性：

![image-20251021140845215](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251021140845215.png)

- **继承父类**：所有枚举类默认继承 `java.lang.Enum` 类（不可手动继承其他类，因 Java 单继承），`Enum` 类提供了枚举的核心方法（如 `name()`、`ordinal()`）。
- **final 修饰**：枚举类被 `final` 修饰，**不可被继承**（避免子类扩展枚举对象，破坏 “有限常量” 的特性）。
- **构造器私有**：反编译后无公开构造器，说明构造器是私有的，确保外部无法创建枚举对象，仅 JVM 在加载时创建第一行定义的枚举实例。
- **自动生成常量实例**：反编译后可见，枚举对象（如 X、Y、Z）会被编译为 `public static final A X = new A();` 形式，在类加载时初始化。

4. 枚举类的核心方法

枚举类的方法分为两类：`Enum` 父类提供的方法、编译器自动生成的方法。

- Enum 父类提供的核心方法

| 方法名       | 返回值   | 作用                                                         |
| ------------ | -------- | ------------------------------------------------------------ |
| `name()`     | `String` | 返回枚举对象的 “名称”（即第一行定义的常量名，如 X 返回 "X"） |
| `ordinal()`  | `int`    | 返回枚举对象的 “索引”（按定义顺序从 0 开始，如 X 是 0、Y 是 1、Z 是 2） |
| `toString()` | `String` | 重写自 `Object`，默认返回 `name()` 的结果（可手动重写自定义格式） |

- 编译器自动生成的方法

  - `values()`：返回包含所有枚举对象的数组（顺序与第一行定义一致），用于遍历所有枚举对象。

    ```java
    // 遍历所有枚举对象
    for (A a : A.values()) {
        System.out.println(a); // 输出：X、Y、Z
    }
    ```

  - `valueOf(String name)`：根据枚举对象的名称（如 "X"），返回对应的枚举实例（名称不存在则抛`IllegalArgumentException`异常）。

    ```java
    A y = A.valueOf("Y");
    System.out.println(y); // 输出：Y
    ```

### 三、枚举类的设计模式关联

- **多例模式**：枚举类是 “多例模式” 的完美实现 —— 类中只有有限个（如 3 个）实例，对外仅提供这些实例，不可新增。

- **单例模式**：若枚举类第一行只定义 1 个枚举对象（如 `public enum Singleton { INSTANCE; }`），则该枚举类就是一个 “线程安全、防反射破坏” 的单例类（面试常考）。

	

	### 四、枚举的核心应用场景：信息的 “分类与标志”

枚举的本质作用是**对有限、固定的信息进行明确分类，并作为 “标志” 在代码中传递 / 判断**，解决 “特定场景下取值范围不可控” 的问题。

**案例 1：游戏方向控制（上下左右）**

- 需求：用户点击 “上 / 下 / 左 / 右” 箭头，后台`move`方法需根据方向控制色块移动。
- 枚举价值：将 “方向” 这一信息分类为`UP、DOWN、LEFT、RIGHT`四个固定枚举值，作为传递给`move`方法的 “标志”—— 方法仅能接收这四个值，避免传入无效方向（如 “斜向”“0/1 数字” 等）。

### 五、枚举 vs 常量：核心优势对比

|      对比      |                   常量实现（以 int 为例）                    |                           枚举实现                           |
| :------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  **取值约束**  | 无约束：方法接收`int`类型，可传入任意 int 值，语法不报错，逻辑易出错 | 强约束：方法仅能接收枚举类中定义的固定值（如`Direction.UP`），传入其他值直接语法报错 |
| **代码可读性** |                        需记忆常量含义                        |       枚举值语义化（`UP/DOWN`直接表意），无需额外注释        |
|   **扩展性**   | 新增方向需定义新常量（如`2=LEFT`），若常量散落在多个类中易混乱 |       新增方向直接在枚举类中添加，统一管理，无散列风险       |

JDK 设计时默认枚举可用于`switch`，且允许省略枚举类前缀（如`case UP`而非`case Direction.UP`），本质是因为枚举的 “取值固定且可枚举” 特性，与`switch`的 “有限分支判断” 场景完全匹配

### 六、模板方法设计模式

6.1 模板方法设计模式核心概念

1. 解决的核心问题

- 针对**功能步骤总体一致、仅部分步骤有差异**的场景（如不同角色执行同类任务、不同模块实现相似流程），避免代码冗余（重复编写相同步骤），简化子类设计。

2. 核心思想

- 定义一个**模板方法**（封装统一的功能步骤骨架），将差异步骤定义为**抽象方法**，由子类实现具体逻辑，实现 “统一流程 + 个性化实现” 的解耦。

6.2 模板方法设计模式的实现步骤（基于抽象类）

1. 步骤 1：定义抽象父类（模板类）

- **作用**：封装统一流程，声明差异步骤。
- 包含两部分核心内容：
  - **模板方法**：用`final`修饰（关键！防止子类重写破坏统一流程），按顺序调用统一步骤和抽象方法（差异步骤），固定功能执行顺序。
  - **抽象方法**：仅声明方法签名，不实现逻辑步骤 2：定义子类（具体实现类）

- **作用**：继承抽象父类，仅重写抽象方法（差异步骤），无需关注统一步骤（父类已实现），简化子类代码。

3. 步骤 3：调用模板方法

- 创建子类对象，调用父类的模板方法（子类继承父类模板方法，无需重写），程序会自动执行父类的统一步骤，并触发子类重写的差异步骤，完成完整功能。

  

6.3 典型应用场景

- 框架开发：如 Spring 中`JdbcTemplate`（固定数据库操作流程，差异逻辑由用户实现）、Servlet 的`service()`方法（固定请求处理流程，`doGet()`/`doPost()`为差异步骤）。
- 业务系统：如 “订单处理”（统一步骤：验证订单→计算金额→生成日志，差异步骤：支付方式（微信 / 支付宝）、物流对接（顺丰 / 京东））。
- 工具类设计：如 “文件处理”（统一步骤：打开文件→读取 / 写入→关闭文件，差异步骤：文件格式解析（Excel/CSV））。

## 第四章 抽象类

### 一、抽象类与抽象方法的定义

抽象类和抽象方法通过关键字`abstract`修饰，是 Java 中用于定义 “不完整类” 和 “未实现方法” 的特殊语法。

| 类型     | 定义语法                            | 核心特征                                                     |
| -------- | ----------------------------------- | ------------------------------------------------------------ |
| 抽象类   | `abstract class 类名 { ... }`       | 可包含普通成员（变量、方法、构造器）和抽象方法，但**不能直接创建对象**（核心限制） |
| 抽象方法 | `abstract `返回值类型 方法名(参数); | **只有方法签名，没有方法体**（无`{}`及内部逻辑），且必须依赖子类重写才能使用 |

```java
// 抽象类A
abstract class A {
    // 普通成员变量
    private String name;
    private int age;
    
    // 普通构造器（抽象类可包含构造器，供子类调用）
    public A() {}
    public A(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // 普通成员方法
    public void showName() {
        System.out.println("Name: " + name);
    }
    
    // 抽象方法（无方法体）
    public abstract void show();
}
```

### 二、抽象类的核心特点

1. **抽象类与抽象方法的依赖关系**

   - 抽象类中**不一定包含抽象方法**（可只含普通成员）,包含抽象方法的类，必须定义为抽象类（否则编译报错）。

2. **抽象类的成员构成**

   抽象类拥有普通类的所有成员，包括：

   - 成员变量（普通变量、静态变量）；
   - 构造器（无参、有参，供子类通过`super()`调用初始化父类成员）；
   - 普通成员方法（有完整方法体）；
   - 额外可包含抽象方法（区别于普通类的核心特征）。

3. **抽象类不能直接创建对象**

   - 语法上：`A a = new A();` 会编译报错；因为抽象方法无方法体
   - 设计本质：抽象类是 “概念性类”（如 “动物”“交通工具”），本身不具备具体实例的特征，**仅用于被子类继承**。

### 三、子类继承抽象类的规则

抽象类的核心使命是 “被继承”，子类继承抽象类时需遵循严格规则，否则编译报错：

1. **子类必须重写抽象类的所有抽象方法**
   - 若子类是**非抽象类**，必须重写父类中所有抽象方法
   - 重写要求：方法签名（返回值、方法名、参数列表）与父类抽象方法完全一致（符合方法重写的语法规则）。
2. **若子类不重写所有抽象方法，需定义为抽象类**
   - 若子类仅重写部分抽象方法，剩余抽象方法仍未实现，则子类必须用`abstract`修饰，成为抽象类（此时子类也无法创建对象，需进一步被其他子类继承并重写剩余抽象方法）。

### 四、抽象类的核心作用场景

1. 当**父类明确所有子类都需要执行某个行为，但每个子类的行为实现逻辑不同**时，父类应将该行为定义为 “抽象方法”，并成为 “抽象类”，具体实现交给子类完成。

2. 使用抽象类的 2 大核心好处
   - 简化代码，避免冗余：若父类定义 “具体方法”（含方法体），但子类都会重写该方法（父类方法体无实际用途），会产生冗余代码；而抽象方法**无需编写方法体**
   - 强制规范，更好支持多态：**强制子类重写**和**统一父类接口**

3. 抽象类 vs 普通父类

不使用抽象类，仅用普通父类也能实现子类重写，但抽象类是更优雅

| 维度       | 抽象类（推荐）                     | 普通父类（不推荐）                   |
| ---------- | ---------------------------------- | ------------------------------------ |
| 方法体冗余 | 无（抽象方法无方法体）             | 有（父类方法体无效，子类必重写）     |
| 子类规范   | 强制重写（编译报错兜底）           | 不强制（子类可能漏写，运行时出问题） |
| 多态支持   | 天然适配（父类有方法，子类必实现） | 依赖子类自觉重写，风险高             |



```java
// 抽象类：作为模板类
abstract class People {
    // 模板方法：用final修饰，固定流程，不允许子类重写
    public final void writeEssay() {
        System.out.println("《我的爸爸》");
        System.out.println("我爸爸是一个好人，我特别喜欢他，他对我很好。");
        // 差异步骤：调用抽象方法，由子类实现
        writeBody();
        
        System.out.println("我爸爸真好，你有这样的爸爸吗？");
    }

    // 抽象方法：差异步骤，子类必须重写
    public abstract void writeBody();
}

//子类1：student
class Student extends People {
    // 仅重写差异步骤（正文）
    @Override
    public void writeBody() {
        System.out.println("2222");
    }
}

//子类2：teacher
class Teacher extends People {
    // 仅重写差异步骤（正文）
    @Override
    public void writeBody() {
        System.out.println("1111");
    }
}


//测试调用
public class Test {
    public static void main(String[] args) {
        // 学生写作文：调用父类模板方法
        People student = new Student();
        student.writeEssay();
        
        System.out.println("-------------------");
        
        // 老师写作文：调用父类模板方法
        People teacher = new Teacher();
        teacher.writeEssay();
    }
}
```





## 第五章 接口

### 一、接口的本质与定义

1. **本质定位**：接口是 Java 中一种**特殊的引用类型**，可理解为 “更抽象的抽象类”，但不归属为类，主要用于定义 “行为规范”（而非具体实现）。

2. 定义语法

   - 使用关键字`interface`定义，格式为：`public interface 接口名 {}`（`public`可省略，默认权限为public访问权限）。

     ```java
     // 接口A的定义
     public interface A {
         // 接口内容（常量、抽象方法）
     }
     ```

3. **核心特点**：接口不能直接创建对象（无构造器），必须通过 “实现类” 使用，类似抽象类的 “不能实例化” 特性，但抽象程度更高。

### 二、JDK8 之前的接口

传统接口（JDK8 及之前）仅允许定义两类成员，且修饰符有默认规则，无需手动写全：

| 成员类型 |                       修饰符规则                        |                           语法细节                           |                             示例                             |
| :------: | :-----------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|   常量   | 必须被`public static final`修饰（可省略，默认自动添加） | 1. 必须显式初始化（赋值）；<br>2. 命名规范为 “全大写 + 下划线分隔” | `String SCHOOL_NAME = "黑马程序员";`（等价于`public static final String SCHOOL_NAME = "黑马程序员";`） |
| 抽象方法 |   必须被`public abstract`修饰（可省略，默认自动添加）   | 1. 无方法体（仅声明方法签名）；<br>2. 不能写`{}`（否则报错） |     `void run();`（等价于`public abstract void run();`）     |

⚠️ 注意：接口中不能定义普通成员变量、普通方法，否则编译报错。

### 三、接口的实现

接口不能实例化，需通过 **“实现类”** 实现接口中的抽象方法，语法和规则如下：

1. 实现语法：使用关键字`implements`，格式为：`public class 实现类名 implements 接口1, 接口2, ... {}`

- 特点：**一个类可以同时实现多个接口**（弥补 Java “单继承” 的限制，类似 “多干爹” 的比喻，一个类可拥有多个接口的规范）。

- 示例：实现类C，同时实现接口A和B 

  ```java
  // 接口B的定义（含抽象方法play()）
  interface B {
      void play();
  }
  
  // 实现类C，同时实现A和B接口
  class C implements A, B {
      // 需重写A和B接口的所有抽象方法
      @Override
      public void run() {
          System.out.println("实现A接口的run方法");
      }
  
      @Override
      public void play() {
          System.out.println("实现B接口的play方法");
      }
  }
  ```

2. 实现类的强制规则

实现类必须满足以下条件之一，否则编译报错：

- **完全重写所有接口的抽象方法**：包括实现的多个接口中所有未实现的抽象方法（方法签名、返回值、参数列表需完全匹配，权限修饰符至少为`public`）。
- **定义为抽象类**：若实现类不想重写所有抽象方法，需用`abstract`修饰为抽象类（但抽象类仍不能实例化，需进一步定义子类重写剩余方法，开发中极少用此方式）。

### 四、接口的核心好处

1. 弥补类 “单继承” 的不足

Java 中**类只能单继承**（一个子类只能有一个直接父类），这会导致类的 “角色单一”，无法同时拥有多个维度的功能 —— 而接口通过 “多实现” 特性，完美解决此问题。

- 例：`Student`类继承`Person`类后，只能被视为 “人” 的角色，无法同时具备 “司机”“厨师” 等其他功能（若想让`Student`开车，单继承无法实现）。

- 语法：类在继承父类的同时，可通过`implements`关键字实现多个接口，格式为：
```java
  class 子类 extends 父类 implements 接口1, 接口2, ... {}
```

  ```java
  // 定义接口（代表不同角色/功能）
  interface Driver { void drive(); } // 司机接口（具备开车功能）
  interface Boyfriend { void buyBreakfast(); } // 男朋友接口（具备买早餐功能）
  
  // Student类：继承Person（父类），同时实现两个接口
  class Student extends Person implements Driver, Boyfriend {
      @Override
      public void drive() { System.out.println("学生开车"); }
      @Override
      public void buyBreakfast() { System.out.println("学生早餐"); }
  }
  ```


2. 支持 “面向接口编程”，实现解耦合

“面向接口编程” 是企业开发的核心思想，核心是**依赖接口而非具体实现类**，可灵活切换实现逻辑，降低代码间的依赖（解耦合）。

- 接口只定义 “必须具备的功能”（方法签名，无方法体），不关心功能的具体实现；不同类可按照接口规范，提供不同的实现逻辑。

- **灵活切换实现类，无需修改核心代码**：

  若系统需要 “一个会开车的角色”，只需定义`Driver`类型的引用，具体赋值为`Student`或`Teacher`对象，后续切换时仅需修改 “new 对象” 的代码，核心业务逻辑（如调用`drive()`）无需改动。

  ```java
  // 核心代码（依赖接口，不依赖具体类）
  Driver driver;
  driver = new Student(); // 先用学生开车
  // driver = new Teacher(); // 后续可直接切换为老师开车，无需改其他代码
  driver.drive(); // 调用开车功能，逻辑随实现类切换
  ```


### 五、接口案例

```java
//Test.java
package com.itheima.interfaceDemo;

public class Test {
    public static void main(String[] args) {
        Student[] allStudents = new Student[10];
        allStudents[0]=new Student("张三",'男',100);
        allStudents[1]=new Student("历史",'男',54);
        allStudents[2]=new Student("版本",'男',83);
        allStudents[3]=new Student("看看",'男',87);
        allStudents[4]=new Student("密码",'男',75);
        allStudents[5]=new Student("单独",'男',87);
        allStudents[6]=new Student("前趋",'男',98);
        allStudents[7]=new Student("单独",'男',88);
        allStudents[8]=new Student("啊啊",'男',55);
        allStudents[9]=new Student("学校",'男',87);

        ClassDataInter cdi=new ClassDataInterImpl_1(allStudents);
        cdi.printAllStudentInfos();
        cdi.printAverageScore();
    }
}

//ClassDataInter.java(impelment)
package com.itheima.interfaceDemo;

public interface ClassDataInter {
    void printAllStudentInfos();
    void printAverageScore();
}

//ClassDataInterImpl_1
package com.itheima.interfaceDemo;

public class ClassDataInterImpl_1 implements ClassDataInter{
    private Student[] students;//记住送来的全体学生对象信息

    //有参构造器
    public ClassDataInterImpl_1(Student[] students)
    {
        this.students=students;
    }
    //将构造器接收的参数值赋值给当前对象的students成员变量，从而保存信息
    @Override
    public void printAllStudentInfos() {
            System.out.println("111");
    }

    @Override
    public void printAverageScore() {
        System.out.println("222");

    }
}

//Student.java
package com.itheima.interfaceDemo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Student {
    private String name;
    private char sex;
    private int score;

}
```



### 六、JDK8 后接口新增的 3 种方法

JDK8 之前接口仅支持**抽象方法**（`public abstract`修饰，可省略）和**常量**（`public static final`修饰，可省略），JDK8 及之后新增 3 种方法，打破了接口 “纯规范” 的特性，增强了功能扩展性。

|   方法类型   |                         核心语法规则                         |                           调用方式                           |                         关键注意事项                         |
| :----------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| **默认方法** | 用`default`修饰，默认隐含`public`（可省略），必须有方法体例：`default void go() { System.out.println("默认方法执行"); }` | 只能通过**接口的实现类对象**调用（接口无实例，需借助实现类） | 1. 实现类可重写默认方法（重写时需去掉`default`）；2. 用于给接口新增功能且不强制实现类修改 |
| **私有方法** | 用`private`修饰，必须有方法体，JDK9 及以上支持：`private void run() { System.out.println("私有方法执行"); }` | 仅能在**接口内部的其他实例方法（默认方法）** 中调用，外部无法直接访问 |  用于抽取接口内部重复逻辑，提高代码复用性，对外隐藏实现细节  |
| **静态方法** | 用`static`修饰，默认隐含`public`（可省略），必须有方法体例：`static void show() { System.out.println("静态方法执行"); }` |       只能通过**接口名**直接调用（`接口名.方法名()`）        | 属于接口自身，不被子接口继承，也不被实现类继承，仅接口自身可调用 |

   **接口新增方法的核心作用**

- **增强接口功能**：突破 “仅抽象方法” 的限制，允许接口包含具体实现逻辑，使接口兼具 “规范” 和 “功能实现” 能力，更接近抽象类（但仍有本质区别）。

- **便于项目的维护和拓展**：若接口需新增功能，若用传统抽象方法，所有实现类必须强制重写（否则编译报错）；而用默认方法，实现类可直接使用新增功能，无需修改代码，尤其适合已上线项目的接口扩展（如 JDK 集合框架中`Collection`接口新增`forEach`默认方法）。

### 七、接口的注意事项

1. **接口与接口可以多继承**，一个接口可以同时继承多个接口（用`extends`连接，逗号分隔），继承后会合并所有父接口的抽象方法、默认方法（无冲突时）。

```java
  interface A { void a(); }
  interface B { void b(); }
  interface C extends A, B { // 多继承A和B
      void c();
  }
```

- 好处：简化实现类操作 —— 若实现类需遵循多个接口规范，可通过 “继承多个接口的子接口” 实现，无需直接实现多个父接口

2. 一个接口继承多个接口，如果多个接口中存在方法签名冲突，则此时不支持多继承，也不支持多实现

- 冲突场景：多个父接口存在**方法签名相同但返回值不同**的抽象方法 / 默认方法。
- 例外：若多个父接口的方法签名**完全相同**（方法名、参数列表、返回值均一致），则视为 “规范合并”，不冲突（例：A 和 B 都有`void show()`，C 继承 A、B 后仅需合并为一个`show()`方法）。

3. 一个类继承了父类，又同时实现了接口，若父类和接口中存在**同名同签名的方法**（父类为普通方法，接口为默认方法），子类会优先使用父类的方法

```java
  class Animal {
      void show() { System.out.println("父类方法"); }
  }
  interface A2 {
      default void show() { System.out.println("接口默认方法"); }
  }
  class Dog extends Animal implements A2 { // 继承Animal+实现A2
      public static void main(String[] args) {
          new Dog().show(); // 输出“父类方法”，优先父类
      }
  }
```

- 特殊需求：若需强制调用接口的默认方法，可在子类中用`接口名.super.方法名()`中转：

```java
  class Dog extends Animal implements A2 {
      void go() {
          A2.super.show(); // 调用接口A2的默认方法
      }
  }
```

  

4. 一个类实现了多个接口，若多个接口存在**同名同签名的默认方法**，实现类必须手动重写该方法，否则编译报错（接口无优先级，需明确 “使用哪个逻辑”）。

- ```java
  interface A3 { default void show() { System.out.println("A3方法"); } }
  interface B3 { default void show() { System.out.println("B3方法"); } }
  // 必须重写show()，否则报错
  class Dog2 implements A3, B3 {
      @Override
      public void show() {
          // 方案1：自定义逻辑
          System.out.println("自定义方法");
          // 方案2：调用指定接口的方法
          A3.super.show(); // 或 B3.super.show()
      }
  }
  ```

### 八、接口与抽象类对比

#### 8.1 相同点

1. 均为抽象形式，都可以有抽象方法，但无法直接创建对象，抽象类和接口都仅定义 “模板” 或 “规范”，不能通过`new`关键字实例化，必须通过子类（或实现类）间接使用。

2. 都可包含抽象方法抽象类中可定义`abstract`修饰的抽象方法（需子类重写）；接口在 JDK8 前仅支持抽象方法，JDK8 后新增默认方法、静态方法等，但仍保留抽象方法的核心功能。

3. 子类 / 实现类必须重写全部抽象方法

   - 继承抽象类的子类，若未重写父类所有抽象方法，自身必须声明为`abstract`抽象类（否则编译报错）；
   - 实现接口的类，若未重写接口所有抽象方法，自身也必须声明为`abstract`抽象类（否则编译报错）。

4. 均支持多态，实现解耦合

   两者都可作为 “父类型” 使用，子类 / 实现类对象可赋值给父类型引用，满足多态特性,如`抽象类名 变量 = new 子类();` `接口名 变量 = new 实现类();`，从而降低代码耦合度，提升灵活性。

#### 8.2 不同点

|    对比维度     |                   抽象类（Abstract Class）                   |                      接口（Interface）                       |
| :-------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|   定义关键字    |                   使用`abstract class`声明                   |                     使用`interface`声明                      |
| 成员权限与类型  | 支持所有成员（构造器、普通成员变量、静态变量、普通方法、抽象方法等），权限修饰符可自定义（`public`/`protected`/`private`等，需符合继承规则） | JDK8 前：仅支持`public static final`常量 + `public abstract`抽象方法；<br>JDK8 后：新增`default`默认方法、`static`静态方法；JDK9 后：新增`private`私有方法（仅接口内部调用） |
| 继承 / 实现规则 | 类只能**单继承**抽象类（Java 单继承机制限制），即一个类最多继承 1 个抽象类 | 类可以**多实现**接口，即一个类可同时实现多个接口（用`,`分隔）；且实现接口后仍可继承其他类 |
| 对类的 “侵入性” | 侵入性强：类继承抽象类后，无法再继承其他类，限制了类的扩展能力 | 侵入性弱：类实现接口后，仍可继承其他类或实现其他接口，灵活性更高 |

#### 8.3 应用场景

1. **抽象类：适合做 “模板父类”，侧重代码复用**
   - 当多个子类存在**共性属性和方法**时，可将共性内容抽取到抽象类中，子类仅实现差异化逻辑，提升代码复用性。
   - 典型案例：Java IO 流（如`InputStream`/`OutputStream`），父类定义流的核心属性（如缓存区）和通用方法（如`close()`），子类（如`FileInputStream`/`ByteArrayInputStream`）仅实现差异化的 “读 / 写” 逻辑，体现 “模板思想”。
2. **接口：适合做 “功能规范”，以及解耦合与多实现**
   - 当需要定义**跨类的功能标准**，且允许多个类灵活实现时，优先使用接口，尤其适合 “多实现” 场景。
   - 典型案例：Java 集合框架（如`List`/`Set`接口），接口仅定义集合的 “增删改查” 规范，不同实现类（如`ArrayList`/`LinkedList`、`HashSet`/`TreeSet`）提供不同底层实现，调用者面向接口编程，可

## 第六章 面向对象高级

### 一、代码块

代码块是类的五大成分之一（成员变量、构造器、方法、内部类、代码块），按是否有`static`修饰，分为**静态代码块**和**实例代码块**，二者语法、执行时机、作用均不同。

1.1 静态代码块

1. 语法格式

```java
public class 类名 {
    
    // 静态代码块：static修饰 + 大括号包裹，直接定义在类下
    static {
        // 代码逻辑
    }
}
```

2. 核心特点

- **归属**：属于 “类”（而非对象），与类一起加载。
- **执行时机**：类加载时**自动、优先执行**，且**仅执行 1 次**（因为类在程序运行中仅加载 1 次）。执行顺序早于`main`方法（即使静态代码块写在`main`方法之后，仍先执行，与代码位置无关）。

3. 作用：初始化类的静态资源

静态资源指属于类的成员（如静态变量、静态数组），适合在静态代码块中完成初始化，优势如下：

- 保证初始化逻辑仅执行 1 次（避免重复初始化，如静态数组初始化）；

- 代码可读性更高，明确标识 “提前加载的静态资源初始化”；

- 示例：初始化 54 张牌的静态数组（项目启动时仅初始化 1 次，后续直接使用）：

  ```java
  public class CodeDemo1 {
      // 静态数组（静态资源）
      static String[] cards = new String[54];
      
      // 静态代码块初始化静态数组
      static {
          cards[0] = "A";
          cards[1] = "2";
          // ... 其他牌初始化逻辑
      }
      
      public static void main(String[] args) {
          // 直接使用已初始化的静态数组
          System.out.println(Arrays.toString(cards));
      }
  }
  ```

  

1.2 实例代码块

1. 语法格式

```java
public class 类名 {
    // 实例代码块：无static修饰 + 大括号包裹，直接定义在类下
    {
        // 代码逻辑
    }
}
```

2. 核心特点

- **归属**：属于 “对象”（而非类），与对象创建绑定。
- **执行时机**：每次创建对象（`new`关键字）时**自动执行**，且**执行在构造器之前**（先执行实例代码块，再执行构造器）。
- **执行次数**：创建几次对象，就执行几次（与对象数量一致）。

3. 作用：初始化对象的实例资源

实例资源指属于对象的成员（如实例变量、实例数组），作用与构造器类似，均为对象初始化，示例：

```java
public class CodeDemo2 {
    // 实例变量（实例资源）
    String name;
    String[] directions;
    
    // 实例代码块：初始化实例资源
    {
        name = "it黑马"; // 所有对象的name默认初始化为"it黑马"
        directions = new String[]{"东", "南", "西", "北"}; // 初始化实例数组
        System.out.println("实例代码块执行");
    }
    
    // 构造器（实例代码块执行后才执行）
    public CodeDemo2() {
        System.out.println("构造器执行");
    }
    
    public static void main(String[] args) {
        new CodeDemo2(); // 输出：实例代码块执行 → 构造器执行
        new CodeDemo2(); // 再次输出：实例代码块执行 → 构造器执行（创建2次对象，执行2次）
    }
}
```



### 二、内部类

2.1 内部类基础概念

1. **定义**：若一个类定义在另一个类的内部，这个 “内部的类” 就称为内部类，外部的类称为 “外部类”。
2. **类比场景**：如 “汽车类” 内部包含 “发动机类”，若发动机类无需单独设计，可定义为汽车类的内部类，体现 “整体与部分” 的关联关系。
   - 成员内部类（无 static 修饰）
   - 静态内部类（有 static 修饰）
   - 局部内部类（定义在方法 / 构造器等局部范围）
   - 匿名内部类（项目中大量使用）

2.2 成员内部类

1. 本质与特点

- **归属**：属于**外部类的对象**（无 static 修饰，类似普通成员变量、成员方法）所持有，需依赖外部类对象存在。
- **访问权限**：可直接访问外部类的所有成员（静态成员 + 实例成员），无需额外操作。

2. 核心语法

（1）定义格式

```java
// 外部类
public class Outer {
    // 成员内部类（无static修饰）
    public class Inner {
        // 内部类的成员（变量、方法、构造器等，与普通类一致）
        private String name;
        public void show() {
            System.out.println("成员内部类方法");
        }
    }
}
```

（2）创建内部类对象

**必须先创建外部类对象**，再通过外部类对象创建内部类对象，格式：

```java
// 1. 创建外部类对象
Outer outer = new Outer();
// 2. 通过外部类对象创建内部类对象
Outer.Inner inner = outer.new Inner();

//3.直接创建
Outer.Inner oi=new Outer().new Inner();
```

（3）访问外部类成员的特殊语法

- **直接访问**：内部类方法中可直接调用外部类的静态成员（如`Outer.staticVar`）和实例成员（如`outerInstanceVar`）。

- 获取寄生的外部类对象：若内部类与外部类有同名成员，需通过`外部类名.this` 明确指定外部类对象，示例：

  ```java
  public class Person {
      private int heartbeat = 100; // 外部类实例成员
      
      // 成员内部类
      public class Heart {
          private int heartbeat = 80; // 内部类实例成员
          
          public void showHeartbeat() {
              System.out.println(heartbeat); // 访问内部类成员（80）
              System.out.println(Person.this.heartbeat); // 访问外部类成员（100）
          }
      }
  }
  ```

  

2.3 静态内部类

1. 本质与特点

- **归属**：属于**外部类本身**（有 static 修饰，类似静态变量、静态方法），不依赖外部类对象，仅与外部类的 “类级别” 关联。
- **访问权限**：仅可直接访问外部类的**静态成员**，无法直接访问外部类的实例成员（需手动创建外部类对象才能访问）。

2. 核心语法

（1）定义格式

```java
// 外部类
public class Outer {
    // 静态内部类（有static修饰）
    public static class Inner {
        // 内部类的成员（支持静态/实例成员，与普通类一致）
        private String name;
        public static void staticMethod() {
            System.out.println("静态内部类的静态方法");
        }
        public void instanceMethod() {
            System.out.println("静态内部类的实例方法");
        }
    }
}
```

（2）创建内部类对象

**无需创建外部类对象**，直接通过 “外部类名。内部类名” 创建，格式：

```java
// 直接创建静态内部类对象
Outer.Inner inner = new Outer.Inner();
```

（3）访问外部类成员的限制

- 直接访问外部类静态成员：`Outer.staticVar`（合法）；

- 直接访问外部类实例成员：`Outer.instanceVar`（非法，报错），需手动创建外部类对象：

  ```java
  public static class Inner {
      public void accessOuter() {
          // 合法：访问外部类静态成员
          System.out.println(Outer.staticVar);
          // 非法：直接访问外部类实例成员
          // System.out.println(Outer.instanceVar);
          // 合法：创建外部类对象后访问
          Outer outer = new Outer();
          System.out.println(outer.instanceVar);
      }
  }
  ```

  

2.4 局部内部类

1. 本质与特点

- **定义范围**：仅能定义在**方法、构造器、代码块**等局部范围内（类似局部变量）。
- 访问限制
  - 作用域仅限于所在的局部范围，出了范围无法使用；
  - 可访问外部类的成员（静态 + 实例），但访问所在局部范围的变量时，变量需被`final`修饰（JDK8 + 可省略 final，但本质仍为常量）。
- **实际用途**：几乎无实际开发价值，仅为语法完整性存在，是匿名内部类的 “铺垫”（匿名内部类本质是特殊的局部内部类）。

2. 定义示例

```java
public class Outer {
    public void method() {
        // 局部内部类（定义在method方法内）
        class Inner {
            public void show() {
                System.out.println("局部内部类方法");
            }
        }
        // 仅能在method方法内使用Inner类
        Inner inner = new Inner();
        inner.show();
    }
}
```



2.5 匿名内部类

1. 定义

- 匿名内部类是**特殊的局部内部类**（定义在方法或代码块等局部范围内），“匿名” 并非 “没有名字”，而是**程序员无需手动声明类名**，编译器会自动生成隐藏的类名。
  - 类比理解：如同 “匿名评价”，评价者有真实身份，只是不对外显示。

2. 语法

- 匿名内部类必须依赖 “已存在的父类” 或 “已存在的接口”，语法结构固定，核心是 “重写方法”：

```java
new 已存在的类名/接口名( 一般没有参数 ) {
    // 核心：重写父类/接口中的方法（必写，否则匿名内部类无意义）
    @Override
    方法返回值 方法名(参数列表) {
        方法体;
    }
};
```

- 特点：匿名内部类本质是一个子类，并会立即创建出一个子类对象，即不需要定义一个子类，在定义一个子类对象，而是一步直接创建
  - 若为 “已存在的类”（可抽象、可具体）：表示匿名内部类是该类的子类；
  - 若为 “已存在的接口”：表示匿名内部类是该接口的实现类（接口本身不能实例化，但匿名内部类通过重写方法满足实例化条件）。

```java
// 1. 定义抽象父类Animal
abstract class Animal {
    public abstract void cry(); // 抽象方法，必须被重写
}

// 2. 创建匿名内部类（本质：Animal的子类 + 子类对象）
// 本来抽象类不允许new对象
Animal cat = new Animal() {
    @Override
    public void cry() {
        System.out.println("猫喵喵叫"); // 重写cry方法，实现猫的叫声
    }
};

// 3. 调用方法（实际执行匿名内部类中重写的cry()）
cat.cry(); // 输出：猫喵喵叫
```

3. 核心使用形式：作为 “对象参数” 传递

匿名内部类的对象**通常作为方法的参数传递**，实现 “对象回调”（将对象传给方法，方法内部调用该对象的重写方法），避免单独定义子类 / 实现类。

- 场景示例：学生 / 老师参加游泳比赛

 步骤 1：定义约束接口（或父类）

```java
// 游泳接口：约束“必须实现游泳方法”
interface Swim {
    void swimming(); // 抽象方法，需实现
}
```

步骤 2：定义 “比赛方法”（接收 Swim 类型参数）

```java
public class Test {
    // 比赛方法：接收Swim对象，调用其swimming()方法
    public static void startCompetition(Swim swimmer) {
        System.out.println("比赛开始");
        swimmer.swimming(); // 调用传递进来的对象的重写方法（回调）
        System.out.println("比赛结束");
    }
}
```

步骤 3：用匿名内部类传递参数（无需定义 Student/Teacher 类）

```java
public static void main(String[] args) {
    // 方式1：先创建匿名内部类对象，再传参
    Swim student = new Swim() {
        @Override
        public void swimming() {
            System.out.println("学生用蛙泳游泳");
        }
    };
    Test.startCompetition(student); // 传学生对象

    // 方式2：直接将匿名内部类作为参数（更简洁，开发常用）
    Test.startCompetition(new Swim() {
        @Override
        public void swimming() {
            System.out.println("老师用狗爬式游泳");
        }
    }); // 传老师对象
}
```



4. 应用场景

- 调用别人提供的方法实现需求时，这个方法可以让我们传输一个匿名内部类对象给其使用

- 匿名内部类并非 “主动使用” 的技术，而是**在调用他人提供的功能时，对方要求传入特定类型（接口 / 抽象类）的对象，且该对象仅需使用一次**时，为简化代码而使用的解决方案。

  核心场景：**调用方法时，方法参数需传入 “接口 / 抽象类的实现对象”，且该对象无需复用**—— 此时直接用匿名内部类创建对象传入，无需单独定义实现类。



### 三、函数式编程

3.1 Lambda函数表达式

1. 函数式编程

- **引入背景**：JDK8 新增的编程特性，目前项目开发中应用广泛，是后续学习 `Stream 流` 的基础。
- **函数的定义**：此处的 “函数” 更接近**数学中的函数**（如 `f(x)=2x+1`），强调 “做什么”，输入相同则输出必然相同；而非 C 语言中与 “方法” 等同的概念。
- **Java 中的函数**：Java 中通过 `Lambda 表达式` 代表 “函数”，与 Java 传统的 “方法”（需依赖类 / 对象）是不同概念，不能混淆。

2. Lambda 表达式的核心作用

- **替代 “函数式接口的匿名内部类”**，简化代码结构，提升可读性。传统匿名内部类需显式声明接口 / 类、重写方法，代码冗余；Lambda 可直接抽取 “方法形参” 和 “方法体”，省略冗余结构。

3. Lambda 表达式的语法格式

```plaintext
(被重写方法的形参列表) -> {被重写方法的方法体代码}
```

- **`()` 括号**：对应匿名内部类中 “被重写方法的形参列表”（若形参为空，括号不可省略）。
- **`->` 箭头**：分隔形参列表与方法体，是 Lambda 的语法标识，无实际逻辑含义。
- **`{}` 大括号**：对应匿名内部类中 “被重写方法的方法体代码”（若方法体仅 1 行代码，大括号可省略，且分号可省略）。

4. Lambda 使用的关键前提：函数式接口

​	Lambda **不能简化所有匿名内部类**，仅能简化 “**函数式接口的匿名内部类**”，这是 Lambda 使用的核心前提。

- 函数式接口的定义

  - **严格条件**：**有且仅有一个抽象方法的接口**（可包含默认方法、静态方法，只要不影响 “函数式接口” 的判定即可）。

  ```java
  // 函数式接口注解（可选，但推荐加）
  @FunctionalInterface
  public interface Swim {
      // 仅一个抽象方法
      void swimming();
  }
  ```

- 函数式接口的验证：`@FunctionalInterface` 注解

  - **作用**：显式声明接口为**函数式接口**，编译器会自动校验 —— 若接口不符合 “仅有一个抽象方法” 的条件，会直接报错。

  - **必要性**：不加注解不影响接口本身是 “函数式接口”，但加注解能提升代码可读性，避免误修改（如新增抽象方法），更安全。

5. 实例：

```java
// 1. 定义函数式接口 Swin（有且仅有一个抽象方法 swimming）
@FunctionalInterface
interface Swim {
    void swimming();
}

public class LambdaDemo {
    public static void main(String[] args) {
        
        // 2. 传统匿名内部类创建 Swim 实例
        Swim s1 = new Swim() {
            @Override
            public void swimming() {
                System.out.println("学生游泳贼快");
            }
        };
        
        s1.swimming(); // 调用方法
    }
}
```

```java
//lambda简化
public class LambdaDemo {
    public static void main(String[] args) {
        // Lambda 简化：仅保留“形参列表”和“方法体”
        Swim s1 = () -> System.out.println("学生游泳贼快");
        s1.swimming(); // 调用方法，效果与匿名内部类完全一致
    }
}
```

6. Lambda 能简化的原理：上下文推断

​	Lambda 之所以能省略 “接口声明”“方法名” 等冗余信息，核心依赖 **Java 编译器的上下文推断机制**。通过上下文联动，编译器可自动补全 Lambda 省略的结构，确保代码逻辑正确。

7. Lambda表达式的省略规则

- 参数类型全部可以省略不写
- 如果只有一个参数，参数类型省略的同时“（）”也可以省略，但多个参数不能省略
- 如果lambda表达式中只有一行代码，大括号可以不写，同时要省略分号；如果这行代码是return语句，也必须去掉return



3.2 方法引用

1. 静态方法的引用

- 如果某个Lambda表达式里只是调用一个静态方法，并且“→”前后参数的形式一致，就可以使用静态方法引用。

- 静态方法引用的语法格式

```java
类名::静态方法名
```

- 符号含义：`::`是方法引用的核心运算符，代表 “引用” 某个类的静态方法。

- 注意：无需写括号`()`，也无需传递参数（参数会自动从 Lambda 上下文传递给静态方法）。

```java
public class Student {
    private String name;
    private int age;


    // 静态方法：比较两个学生的年龄（升序规则）
    public static int compareByAge(Student s1, Student s2) {
        return s1.getAge() - s2.getAge();
    }
}


//Test.java
import java.util.Arrays;

public class Test1 {
    public static void main(String[] args) {
        Student[] students = {
                new Student("张三", 20),
                new Student("李四", 18),
                new Student("王五", 22)
        };

        // Lambda表达式：调用静态方法compareByAge排序
        Arrays.sort(students, (o1, o2)->Student.compareByAge(o1, o2));
        // 简化后
        Arrays.sort(students, Student::compareByAge);

    }
}
```

2. 实例方法引用

- 如果某个Lambda表达式里只是通过对象名称调用一个实例方法，并且“→”前后参数的形式一致，就可以使用实例方法引用。

- 语法

  ```java
  对象实例 :: 实例方法名
  ```

  - 「`::`」是方法引用的运算符，作用是将「对象」与「它要调用的实例方法」关联起来。
  - 无需括号：实例方法名后**不接括号**（因仅引用方法，不直接调用，参数由上下文自动传递）。

3. 特定类型的方法引用

- 如果某个Lambda表达式里只是调用一个特定类型的实例方法，并且前面参数列表中的第一个参数是作为方法的主调，后面的所有参数都是作为该实例方法的入参的，则此时就可以使用特定类型的方法引用。
- 语法：

```java
特定类型::实例方法名
    
Arrays.sort(names, (String o1, String o2) -> {
    return o1.compareToIgnoreCase(o2);
});
// 进一步省略：参数类型（编译器推断）、大括号+return（单语句）
Arrays.sort(names, (o1, o2) -> o1.compareToIgnoreCase(o2));

// String是“特定类型”，compareToIgnoreCase是实例方法名
Arrays.sort(names, String::compareToIgnoreCase);
```



4. 构造器引用

- 构造器引用是对 “创建对象” 这一行为的简化表示，如果某个Lambda表达式里只是在创建对象，并且“→”前后参数情况一致，就可以使用构造器引用。

- 语法
  - **格式**：`类名::new`（类名必须是要创建对象的目标类，`::`为方法引用运算符，`new`代表调用该类的构造器）

```java
// 目标类：Car
class Car {
    private String name;
    // 与CarFactory接口抽象方法匹配的构造器
    public Car(String name) {
        this.name = name;
    }
}

// 函数式接口：CarFactory（仅一个抽象方法）
interface CarFactory {
    Car getCar(String name);
}

// 匿名内部类实现CarFactory接口
CarFactory cf = new CarFactory() {
    @Override
    public Car getCar(String name) {
        // 调用Car的构造器创建对象并返回
        return new Car(name);
    }
};
// 使用：通过cf获取Car对象
Car c1 = cf.getCar("奔驰");

// 构造器引用（类名::new）
CarFactory cf = Car::new;

// 使用方式不变，效果与前两种完全一致
Car c1 = cf.getCar("奔驰");
```



### 四、常用API

#### 4.1 String 类

1. **本质定义**
   - `String`是 Java 中的**字符串类**，用于表示 “人类可识别的文字数据”（如登录名、密码、文本内容等），遵循面向对象思想 —— 用`String`类的**对象**封装字符串数据。
   - 属于`java.lang`包，无需手动导入（JVM 自动导入该包下的类）。
2. **核心作用**
   - 第一步：**创建对象并封装字符串数据**（将需要处理的文字数据存入`String`对象）；
   - 第二步：**调用对象方法处理数据**（如比较内容、替换敏感词、截取字符串等），满足项目中对文字的各类操作需求。

|    创建方式    |                           语法示例                           |                             特点                             |                           适用场景                           |
| :------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|    直接定义    |                    `String s1 = "hello";`                    | 1. 语法简洁，无需`new`；<br>2. 自动存入**字符串常量池**（内存优化）；<br>3. 相同内容仅存 1 份，多变量共享 |     明确字符串内容的场景（如固定的系统默认值、常量文本）     |
| 调用构造器创建 | 1.  无参构造：`String s2 = new String();`（创建空字符串）<br>2.  有参构造（字符串）：`String s3 = new String("hello");`<br>3.  有参构造（字符数组）：`char[] arr = {'h','e','l','l','o'}; String s4 = new String(arr);`<br>4.  有参构造（字节数组）：`byte[] bytes = {97,98,99}; String s5 = new String(bytes);`（字节转字符串） | 1. 必须用`new`，每次`new`都会创建**新对象**；<br>2. 对象存入**堆内存**，不进入常量池；<br>3. 相同内容会创建多个对象，浪费内存 | 特定场景（如将字节数组 / 字符数组转为字符串，常见于通信、文件读取） |



```java
String s1 = "abc"; 
String s2 = "abc"; 
System.out.println(s1 == s2); // 结果：true（地址相同，复用常量池对象）
```

- **设计目的**：节约内存 —— 项目中可能多次使用相同字符串（如 “黑马程序员”），常量池复用避免重复创建对象。

```java
String s3 = new String("abc"); 
String s4 = new String("abc"); 
System.out.println(s3 == s4); // 结果：false（地址不同，两个独立对象）
```

- **注意**：构造器参数中的 “abc” 仍会存入常量池，但`new`出的对象本身在堆内存，与常量池中的 “abc” 是两个不同对象。

3. String 类核心常用方法

|                          方法名                          |                  语法示例                  |                             作用                             |
| :------------------------------------------------------: | :----------------------------------------: | :----------------------------------------------------------: |
|                   `equals(Object obj)`                   |              `s1.equals(s2)`               | 比较两个字符串的**内容是否相等**（区分大小写），返回`boolean` |
|         `equalsIgnoreCase(String anotherString)`         |         `s1.equalsIgnoreCase(s2)`          |               比较内容是否相等，**忽略大小写**               |
|                        `length()`                        |          `int len = s1.length();`          |                 获取字符串的长度（字符个数）                 |
|               `substring(int beginIndex)`                |      `String sub = s1.substring(3);`       |       从指定索引（`beginIndex`）开始，截取到字符串末尾       |
|        `substring(int beginIndex, int endIndex)`         |     `String sub = s1.substring(0,3);`      |     截取索引`[beginIndex, endIndex)`的内容（包前不包后）     |
| `replace(CharSequence target, CharSequence replacement)` | `String newStr = s1.replace("毛", "***");` |           将字符串中的`target`替换为`replacement`            |
|                `contains(CharSequence s)`                |  `boolean hasKey = s1.contains("Java");`   |                判断字符串是否包含指定内容`s`                 |

**关键注意点：`==`与`equals`的区别**

- `==`：比较**对象地址**（基本类型比较值，引用类型比较地址）；
- `equals`：`String`类重写了该方法，专门比较**字符串内容**（不关心地址）；
- 实战中判断字符串内容是否相等，**必须用`equals`**，不能用`==`（否则会因地址不同导致判断错误，如用户输入的字符串存堆内存，系统常量存常量池）。

#### 4.2 ArrayList

1. **本质**：一种**动态数组容器**，可理解为 “可伸缩的气球”—— 数据不足时自动扩容，无需手动处理数组长度。
2. 集合框架定位：属于`List`接口的实现类，是 Java 开发中最常用的集合之一，特点是：
   - 允许存储**重复数据**
   - 保证数据的**插入顺序**（先加的数据在前，后加的在后）
3. 泛型（Generic）
   - 作用：给集合 “贴标签”，约束其只能存储某一种数据类型（如`String`、`Student`对象）
   - 语法：`ArrayList<数据类型> 集合名 = new ArrayList<>();`

4. 创建 ArrayList 对象（初始化容器）

- **核心逻辑**：通过无参构造器创建 “空集合”，后续通过方法添加数据

- 语法（带泛型，推荐）：

  ```java
  // 约束集合只能存储String类型数据
  ArrayList<String> list = new ArrayList<>();
  ```

- 注意：若不写泛型（如`ArrayList list = new ArrayList()`），集合可存储任意类型数据（如`String`、`int`、`boolean`），但会导致类型不安全，开发中严禁使用。

5. 核心操作：增删改查（CRUD）

​	ArrayList 提供专门方法实现数据操作，无需手动处理索引移位、长度扩容，常用方法如下：

| 操作类型 |        方法示例         |                功能说明                |                 返回值 / 注意事项                  |
| :------: | :---------------------: | :------------------------------------: | :------------------------------------------------: |
|    增    |   `list.add("Java")`    |           向集合末尾添加数据           |                无返回值（直接添加）                |
|    增    | `list.add(1, "Python")` |       在指定索引（1）处插入数据        |         索引需合法（0 ≤ 索引 < 集合长度）          |
|    查    |      `list.get(0)`      |        按索引（0）获取对应数据         |    返回该索引处的数据（如上述示例返回 "Java"）     |
|    查    |      `list.size()`      |      获取集合中实际存储的数据个数      |     返回 int 类型（区别于数组的`length`属性）      |
|    删    |    `list.remove(2)`     |          按索引（2）删除数据           |                  返回被删除的数据                  |
|    删    |  `list.remove("Java")`  | 按 “数据内容” 删除（删除第一个匹配项） | 返回`boolean`：成功删返回`true`，无匹配返回`false` |
|    改    | `list.set(0, "Java8")`  |    将指定索引（0）的数据替换为新值     |                 返回被替换的旧数据                 |

5. 集合遍历：如何查看所有数据？

开发中很少 “单个索引查数据”，而是通过**循环遍历**获取所有元素，核心逻辑是 “用`size()`获取长度，用`get(index)`遍历每个索引”：

```java
ArrayList<String> list = new ArrayList<>();
list.add("赵敏");
list.add("Java");
list.add("Python");

// 遍历：从索引0到size()-1（避免越界）
for (int i = 0; i < list.size(); i++) {
    String data = list.get(i); // 按索引取数据
    System.out.println(data);  // 依次输出：赵敏、Java、Python
}
```



### 五、GUI编程

1. 概念

- **GUI 定义**：Graphical User Interface（图形用户界面），通过窗口、按钮、文本框等图形元素与用户交互，相比命令行界面更直观、友好。
- **核心优势**：增强用户体验，可用于开发桌面应用（如计算器、管理系统客户端），Java 提供原生 GUI 编程支持。

2. 企业应用现状

- Java GUI 的局限性：企业极少使用 Java 做 GUI 开发，原因包括：
  1. 开发效率低：实现简单界面需编写大量代码，代码臃肿（如百行代码仅生成一个基础窗口）；
  2. 技术趋势变化：商业领域更倾向于浏览器端（网页）或移动端（APP），桌面客户端需求减少。

3. Java GUI 核心技术库：AWT 与 Swing

**Swing 为实际学习和使用重点**，两者对比如下：

| 技术库                         | 核心特点                                                     | 依赖关系                   | 跨平台性                                                     | 现状                            |
| ------------------------------ | ------------------------------------------------------------ | -------------------------- | ------------------------------------------------------------ | ------------------------------- |
| AWT（Abstract Window Toolkit） | 提供原生 GUI 组件（如 Frame、Button）                        | 依赖操作系统本地窗口系统   | 差：Windows 下开发的程序可能无法在 macOS 运行（依赖系统自带组件） | 被淘汰，仅作了解                |
| Swing                          | 基于 AWT 改良，提供更丰富组件<br>（组件名前加 “J”，如 JFrame、JButton） | 轻量级组件，不依赖本地系统 | 好：自带组件库，可在不同系统运行且风格统一                   | 主流学习技术，用于实际 GUI 开发 |

Swing 常用核心组件

- 容器组件（用于承载其他组件）：
  - `JFrame`：顶层窗口容器（整个 GUI 应用的 “主窗口”）；
  - `JPanel`：面板容器（相当于 “桌布”，可嵌套在 JFrame 中，支持自适应布局，避免手动调整组件位置）。
- 功能组件：
  - `JButton`：按钮（如 “登录”“提交” 按钮）；
  - `JTextField`：文本输入框（用于输入用户名、密码等）；
  - `JTable`：表格组件（展示结构化数据，如员工列表）；
  - `ImageIcon`：图片组件（用于加载和显示图片）。

4. Swing 布局管理器(Layout Manager)

布局管理器用于控制组件在容器中的位置和大小，避免手动设置坐标，简化GUI设计。

- 流式布局（FlowLayout）

  - **排版规则**：组件按水平方向从左到右排列，一行排满后自动换行（类似文档换行逻辑）；

  - **适用场景**：简单组件排列（如多个按钮横向分布）。

- 边界布局（BorderLayout）

  - **排版规则**：将容器分为 5 个固定区域（东、南、西、北、中），每个区域可放置一个组件；

  - **适用场景**：需要划分明确功能区域的界面（如游戏窗口：“北” 放标题、“中” 放游戏画布、“西” 放功能按钮）。

- 网格布局（GridLayout）

  - **排版规则**：将容器划分为指定行数和列数的网格（如 2 行 3 列），组件按顺序填充网格，每个网格大小统一；

  - **适用场景**：组件需整齐排列的界面（如计算器按钮布局、数据表格头部）。

- 盒子布局（BoxLayout）

  - **排版规则**：按指定方向（X 轴：横向、Y 轴：纵向）排列组件，支持设置组件间间距；

  - **核心特点**：需配合`JPanel`使用，纵向排列能力强（弥补流式布局纵向排版的不足）；

  - **适用场景**：表单类界面（如登录界面：“用户名输入框”“密码输入框”“登录按钮” 纵向排列）。



### 六、事件处理

1. **事件（Event）的定义**

   - 用户对 GUI 界面的所有操作都称为 “事件”，例如：按钮点击、鼠标右键、键盘按键（如上下左右箭头）、鼠标双击 / 单击等。
   - 未处理的事件不会触发界面响应（如默认按钮点击无反应），需通过代码绑定处理逻辑。

2. **事件处理的核心角色：事件监听器（Event Listener）**

   - 作用：监听并响应特定类型的事件，是 Java GUI 事件处理的核心机制，对应接口或适配器类（如`ActionListener`、`KeyListener`）。
   - 分类：
     - 点击事件监听器：处理按钮点击等操作。
     - 按键事件监听器：处理键盘按键（如游戏上下左右控制）。
     - 鼠标行为监听器
     - ···

3. 事件处理的基础逻辑

   在 Java GUI 中，为组件（如按钮）绑定事件（如点击事件），本质是为组件关联一个**事件监听器对象**（需实现对应事件接口，如点击事件的`ActionListener`），并在监听器中重写事件处理方法（如`actionPerformed`）。

4. 事件绑定的三种具体写法

 **写法 1**：自定义事件监听器实现类（基础写法）

- 核心思路：通过创建一个**独立的类**，实现目标事件接口（如`ActionListener`），重写事件处理方法；再创建该类的对象，作为监听器绑定到组件上。

- 关键步骤

1. 定义实现类：创建类（如`MyActionListener`），实现`ActionListener`接口，重写`actionPerformed`方法（编写事件逻辑，如弹框）。
   - 若事件逻辑依赖外部对象（如弹框需依附`JFrame`窗口），可通过**构造器传参**将外部对象（如窗口）传入监听器类。
2. **绑定监听器**：创建实现类对象，通过组件的`addActionListener(监听器对象)`方法完成绑定。

```java
// 1. 自定义事件监听器实现类
class MyActionListener implements ActionListener {
    private JFrame frame; 
    public MyActionListener(JFrame frame) {
        this.frame = frame;
    }
    // 重写事件处理方法
    @Override
    public void actionPerformed(ActionEvent e) {
        // 事件逻辑：弹出消息框
        JOptionPane.showMessageDialog(frame, "success"）");
    }
}

// 2. 绑定监听器到按钮
JFrame frame = new JFrame("事件写法1示例");
JButton btn = new JButton("点击我");
// 创建监听器对象并传入窗口，绑定到按钮
btn.addActionListener(new MyActionListener(frame));
```

- 特点

  - 逻辑清晰，监听器与组件解耦，适合事件逻辑复杂、需复用的场景。

  - 需额外定义类，代码量稍多。

  

**写法 2**：匿名内部类（简洁写法）

- 核心思路

不定义独立的监听器类，而是在调用`addActionListener`方法时，**直接通过匿名内部类**实现事件接口并创建对象，简化代码。

- 关键步骤

1. 调用组件的`addActionListener`方法，参数直接写`new 事件接口()`。
2. 在匿名内部类中重写事件处理方法（`actionPerformed`）。

```java
JFrame frame = new JFrame("事件写法2示例");
JButton btn = new JButton("点击我");
// 匿名内部类实现ActionListener并创建对象
btn.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent e) {
        // 事件逻辑：弹出消息框
        JOptionPane.showMessageDialog(frame, "按钮被点击了（写法2）");
    }
});
```

- 特点

  - 代码简洁，无需额外定义类，适合事件逻辑简单、无需复用的场景。

  - 若逻辑复杂，匿名内部类会导致代码可读性下降。



**写法 3**：自定义窗口类实现事件接口（高级写法，项目常用）

- 核心思路

让**自定义窗口类**（继承`JFrame`，代表一个独立界面）同时实现事件接口（如`ActionListener`），此时窗口对象**既是界面容器，也是事件监听器**，简化对象管理。

- 关键步骤

1. 自定义窗口类：
   - 继承`JFrame`（成为窗口），实现`ActionListener`（成为监听器）。
   - 在类中定义组件（如按钮），通过构造器初始化窗口属性（标题、大小、关闭规则）和组件布局。
   - 重写`actionPerformed`方法（事件逻辑），并将窗口对象（`this`）绑定到按钮。

```java
// 1. 自定义窗口类：继承JFrame + 实现ActionListener
class MyLoginFrame extends JFrame implements ActionListener {
    // 构造器：初始化窗口和组件
    public MyLoginFrame() {
        // 窗口属性设置
        setTitle("事件写法3示例（自定义窗口）");
        setSize(300, 200);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null); // 居中显示

        // 创建按钮并绑定监听器（this即窗口对象，也是监听器）
        JButton btn = new JButton("登录");
        btn.addActionListener(this); // 绑定this（窗口=监听器）

        // 添加按钮到窗口
        add(btn);
    }

    // 重写事件处理方法（窗口作为监听器的逻辑）
    @Override
    public void actionPerformed(ActionEvent e) {
        // 事件逻辑：弹出消息框（this代表当前窗口，作为弹框依附对象）
        JOptionPane.showMessageDialog(this, "登录按钮被点击了（写法3）");
    }
}

// 2. 使用自定义窗口
public class Test {
    public static void main(String[] args) {
        // 创建窗口对象并显示
        new MyLoginFrame().setVisible(true);
    }
}
```

- 特点

  - 符合项目开发逻辑：一个类代表一个独立界面（如登录界面、主界面），便于管理（若项目有 50 个界面，可对应 50 个此类）。

  - 窗口与监听器合一，减少对象创建，代码更优雅。

  - 需理解 “一个对象多重角色”（窗口 + 监听器），对面向对象认知要求较高。