# Java高级技术

## 第一章 单元测试

一、 单元测试的核心概念

1. **定义**：针对 Java 程序中**最小功能单元 —— 方法**，编写测试代码，验证其正确性的过程。
2. 为什么要做单元测试
   - 程序 90% 以上由方法构成，方法正确性是软件整体正确的核心保障；
   - 企业级项目中方法数量可能达数千甚至上万，人工验证无法覆盖；
   - 防止后续开发中（如其他程序员修改）意外破坏已有正确方法，需 “随时可测、实时验证”。

二、传统测试方式（main 方法测试）的问题

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251101141634207.png" alt="image-20251101141634207" style="zoom: 67%;" />

1. **无法自动化测试**：需手动逐个执行`main`方法，一个方法抛出异常会导致后续方法无法执行；
2. **无测试报告**：需程序员手动观察控制台输出判断结果，无法自动标记 “成功 / 失败”；
3. **无法一键批量测试**：多个类的方法需对应多个`main`方法，成千上万个方法需手动启动上万次，效率极低。

三、JUnit 单元测试框架

JUnit 是 Java 领域最经典、最基础的第三方开源单元测试框架（已集成到 IDEA 中，无需手动下载 jar 包），核心优势如下：

1. **灵活测试**：支持单个方法测试、单个类所有方法测试、整个模块所有方法测试，且**方法间独立执行**（一个方法失败不影响其他方法）；
2. **自动生成报告**：通过 “颜色标识” 直观展示结果（绿色 = 成功，红色 = 异常失败，黄色 = 业务结果不符），无需人工判断；
3. **一键批量执行**：可一次性执行项目中所有测试方法，上千个方法也能快速完成验证。

四、JUnit 框架的使用步骤

1. 环境准备：导入 JUnit 依赖

2. 编写测试类与测试方法

（1）**测试方法的 4 个强制要求**

| 要求       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| 访问修饰符 | 必须为`public`（保证框架可调用）                             |
| 参数列表   | 必须无参（测试方法无需外部传入参数）                         |
| 返回值类型 | 必须无返回值（测试结果通过框架报告展示，无需返回）           |
| 核心注解   | 方法上必须添加`@Test`注解（标记该方法为 JUnit 可执行的测试方法） |

 （2）测试方法的核心逻辑

测试方法内部需完成 “调用目标方法 + 验证结果”，分为两种场景：

- **场景 1：验证无返回值方法**（如打印方法）：调用目标方法后，观察控制台输出是否符合预期；

- 场景 2：验证有返回值方法（如计算、获取索引）：需通过`Assert`类的断言方法验证 “实际结果” 与 “预期结果” 是否一致，核心断言方法为：

  ```java
  // 断言实际结果与预期结果相等，若不相等则测试失败并显示提示信息
  Assert.assertEquals("断言失败提示信息", 预期结果, 实际结果);
  ```

  示例：测试 “获取字符串最大索引” 方法（正确逻辑应为 “长度 - 1”）：

  ```java
  @Test
  public void testMaxIndex() {
      // 1. 调用被测试方法（实际结果）
      int actual = StringUtil.getMaxIndex("ABCDEFG"); // 字符串长度7，正确索引应为6
      // 2. 断言（预期结果=6）
      Assert.assertEquals("获取最大索引业务异常，请检查", 6, actual);
  }
  ```

3. 执行测试与查看结果

（1）执行方式

- 执行单个测试方法：在该方法内部右键 → 选择 “Run 方法名”；
- 执行单个测试类所有方法：在测试类名处右键 → 选择 “Run 类名”；
- 执行整个模块所有方法：在模块名处右键 → 选择 “Run Tests”。

 （2）结果解读

| 颜色 |                             含义                             |
| :--: | :----------------------------------------------------------: |
| 绿色 |              测试通过（无异常，且断言结果一致）              |
| 红色 |      测试失败（执行过程中抛出异常，如空指针、数组越界）      |
| 黄色 | 断言失败（无异常，但实际结果与预期结果不符，代表业务逻辑错误） |



## 第二章 反射

### 2.1 概念

 一、反射的定义与本质

反射是 Java 高级技术的核心，源于 JDK 官方`java.lang.reflect`包

1. **定义**：反射允许以编程的方式访问已加载类的**字段（成员变量）、方法、构造器**等信息，并对这些成分进行操作。

![image-20251101145100375](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251101145100375.png)

2. 本质理解：

- 先将目标类（如`Student`类）加载到内存中；
- 把类的 “成分”（变量、方法、构造器）也视为 “对象”（如`Field`对象代表成员变量、`Method`对象代表方法）；
- 通过代码主动 “解剖” 这些成分对象，获取其信息（如变量名、方法参数）或执行操作（如调用方法、创建对象）。

二、反射的核心思想：万物皆对象

反射设计的核心逻辑是 “**类本身也是对象**”，这是理解反射的关键：

- 我们平时定义的类（如`Student`），在 Java 中会被封装成一个`Class`类型的对象（称为 “类对象”）；
- `Class`类是 JDK 提供的特殊类，它代表了 “类的模板”，一个类在内存中**仅会生成一个`Class`对象**（无论通过哪种方式获取，都指向同一个对象）；
- 只有先拿到类的`Class`对象，才能进一步解剖其内部的字段、方法、构造器。

三、获取 Class 对象的 3 种方式

获取`Class`对象是反射的**第一步**（相当于拿到 “类的钥匙”），共有 3 种常用方式，且三种方式获取的`Class`对象完全相同（内存中唯一）：

|          方式          |                         语法示例                         |                   适用场景                   |
| :--------------------: | :------------------------------------------------------: | :------------------------------------------: |
|       类名.class       |           `Class<Student> c1 = Student.class;`           |       已知类的具体类型，编译期可确定类       |
| Class.forName (全类名) |         `Class.forName("com.itheima.Student");`          | 仅知道类的全路径名（如配置文件中读取的类名） |
|    对象.getClass ()    | `Student s = new Student(); Class<?> c3 = s.getClass();` |    已有类的实例对象，需通过对象反推类信息    |

```java
public class ReflectDemo {
    public static void main(String[] args) throws ClassNotFoundException {
        //1.获取类对象
        Class c1=Student.class;
        //2.Class.forName("类的全类名")
        Class c2=Class.forName("demo_2.Student");
        //3.对象.getClass()
        Student s=new Student();
        Class c3=s.getClass();
    }
}

```



### 2.2 获取类中的成分

一、获取类的成分并操作

均优先使用带`declared`的 API（支持获取私有成分）。

1. 构造器（Constructor）的反射

 （1）获取构造器

|      需求      |                 API 方法                 |                  说明                  |
| :------------: | :--------------------------------------: | :------------------------------------: |
| 获取所有构造器 |       `getDeclaredConstructors()`        | 返回`Constructor[]`数组，含私有构造器  |
| 获取单个构造器 |  `getDeclaredConstructor(参数类型...)`   | 根据参数类型匹配（无参则不填），含私有 |
| 获取公开构造器 | `getConstructors()` / `getConstructor()` |      仅能获取`public`修饰的构造器      |

```java
// 1. 获取Dog类的所有构造器（含私有）
Constructor<?>[] allConstructors = dogClass.getDeclaredConstructors();

// 2. 获取Dog类的“String, int”参数的构造器（含私有）
Constructor<Dog> paramConstructor = dogClass.getDeclaredConstructor(String.class, int.class);

// 3. 获取Dog类的无参构造器（含私有）
Constructor<Dog> noParamConstructor = dogClass.getDeclaredConstructor();
```

（2）操作构造器：创建对象

构造器的核心作用是创建对象，通过`newInstance(参数值...)`方法实现，但需注意**私有构造器的权限问题**：

- 公开构造器：直接调用`newInstance`；
- 私有构造器：需先通过`setAccessible(true)`“暴力反射” 突破权限（临时生效，不改变原构造器权限）。

```java
// 1. 用无参构造器创建对象（私有构造器需突破权限）
noParamConstructor.setAccessible(true); // 突破私有权限
Dog dog1 = noParamConstructor.newInstance(); // 无参，不填参数

// 2. 用“String, int”参数构造器创建对象（公开构造器无需突破权限）
Dog dog2 = paramConstructor.newInstance("泰迪", 3); // 传入参数值
```

**关键提醒**：视频中强调 “反射创建对象看似冗余（不如直接`new`），但后续框架开发（如 Spring）中是核心机制”。

2. 成员变量（Field）的反射

 （1）获取成员变量

| 需求                   | API 方法                     | 说明                           |
| ---------------------- | ---------------------------- | ------------------------------ |
| 获取所有成员变量       | `getDeclaredFields()`        | 返回`Field[]`数组，含私有变量  |
| 获取单个成员变量       | `getDeclaredField(变量名)`   | 根据变量名匹配，含私有变量     |
| （不推荐）获取公开变量 | `getFields()` / `getField()` | 仅能获取`public`修饰的成员变量 |

```java
// 1. 获取Dog类的所有成员变量（含私有，如name、age、hobby）
Field[] allFields = dogClass.getDeclaredFields();

// 2. 获取Dog类的“hobby”成员变量（私有）
Field hobbyField = dogClass.getDeclaredField("hobby");
```

 （2）操作成员变量：取值（get）与赋值（set）

成员变量不能独立存在，必须依赖对象（“宿主”），操作前需先创建对象，且私有变量需突破权限：

- 赋值：`field.set(对象, 值)`；
- 取值：`field.get(对象)`（返回`Object`，需强转）。

```java
// 1. 先创建对象（用之前反射创建的dog1）
// 2. 给dog1的“hobby”变量赋值（私有变量需突破权限）
hobbyField.setAccessible(true); // 突破私有权限
hobbyField.set(dog1, "社交"); // 给dog1的hobby赋值为“社交”

// 3. 从dog1中获取“hobby”变量的值
String dog1Hobby = (String) hobbyField.get(dog1); 
System.out.println(dog1Hobby); // 输出：社交
```

**等价对比**：反射操作等价于传统的`dog1.setHobby("社交")`和`dog1.getHobby()`，只是语法逻辑反转（由 “对象调方法” 变为 “变量追对象”）。

3. 成员方法（Method）的反射

（1）获取成员方法

| 需求                   | API 方法                                 | 说明                                                |
| ---------------------- | ---------------------------------------- | --------------------------------------------------- |
| 获取所有成员方法       | `getDeclaredMethods()`                   | 返回`Method[]`数组，含私有方法（含 get/set 方法）   |
| 获取单个成员方法       | `getDeclaredMethod(方法名, 参数类型...)` | 根据方法名 + 参数类型匹配（无参则不填），含私有方法 |
| （不推荐）获取公开方法 | `getMethods()` / `getMethod()`           | 仅能获取`public`修饰的成员方法                      |

```java
// 1. 获取Dog类的所有成员方法（含私有，如eat()、eat(String)、getName()）
Method[] allMethods = dogClass.getDeclaredMethods();

// 2. 获取Dog类的无参方法“eat()”（私有）
Method eatNoParamMethod = dogClass.getDeclaredMethod("eat");

// 3. 获取Dog类的有参方法“eat(String)”（公开）
Method eatParamMethod = dogClass.getDeclaredMethod("eat", String.class);
```

（2）操作成员方法：调用方法（invoke）

方法需依赖对象调用，通过`method.invoke(对象, 参数值...)`实现，私有方法需突破权限，且支持接收方法返回值：

- 无参方法：`invoke(对象)`（不填参数）；
- 有参方法：`invoke(对象, 参数值...)`；
- 返回值：`invoke`的返回值即方法的返回值（无返回则为`null`，需强转）。

```java
// 1. 调用私有方法eat()（需突破权限）
eatNoParamMethod.setAccessible(true); // 突破私有权限
Object result1 = eatNoParamMethod.invoke(dog1); // 无参调用，result1为null（eat()无返回）

// 2. 调用公开方法eat(String)（无需突破权限）
Object result2 = eatParamMethod.invoke(dog1, "牛肉"); // 传入参数“牛肉”
System.out.println(result2); // 输出：狗说谢谢谢谢（假设eat(String)返回此字符串）
```

二、反射的核心特性：暴力反射（突破权限）

视频中反复强调 “暴力反射” 是反射的关键特性，用于访问`private`修饰的构造器、成员变量、成员方法：

- 核心 API：`setAccessible(true)`（所有反射成分的父类`AccessibleObject`的方法）；
- 作用：临时绕过 Java 的访问权限检查，仅在当前反射操作中生效，不改变原成分的权限修饰；
- 场景：访问私有构造器创建对象、修改私有变量值、调用私有方法（如单例模式的私有构造器可被反射突破）。

三、反射的逻辑本质：“关系反转”

- 传统方式：**程序员主动操作**（如`new Dog()`创建对象、`dog.setHobby()`赋值）；
- 反射方式：**元信息（Class/Constructor/Field/Method）主动找对象**（如`constructor.newInstance()`、`field.set(dog, 值)`）；
- 本质：操作逻辑从 “人找对象” 变为 “元信息追对象”，即 “关系反转”，因此称为 “反射”。



### 2.3 作用、应用场景

一、反射的四大核心作用

1. 基础作用：获取并操作类的全部成分

- **核心能力**：通过反射可获取类的所有成分（成员变量、方法、构造器等），并对其执行操作（如修改变量值、调用方法）。

2. 破坏封装性：访问类的私有成分

- **核心逻辑**：Java 的 “封装性” 本应限制私有成员（`private`修饰的变量、方法、构造器）的外部访问，但反射可通过 “暴力反射”（如`setAccessible(true)`）突破该限制。
- 关键结论
  - 反射可访问私有构造器，意味着**Java 单例模式并非绝对安全**（可通过反射创建新实例）；
  - 反射可读写私有变量、调用私有方法，需在实际开发中注意权限控制。

3. 绕过泛型约束：突破编译期泛型限制

- **前提知识**：Java 泛型是 “编译期语法糖”—— 编译后泛型标签会被擦除（如`List<String>`编译后变为`List<Object>`），仅在编译阶段约束数据类型。

- **反射原理**：反射工作在**运行时**，此时泛型已擦除，可通过反射调用类的方法（如`List`的`add()`），向泛型集合中添加非指定类型的数据。

- 案例演示

  ```java
  // 1. 定义泛型集合（编译期约束只能存String）
  List<String> list = new ArrayList<>();
  // 2. 通过反射获取List的Class对象（运行时泛型已擦除）
  Class<?> clazz = list.getClass();
  // 3. 获取List的add()方法（参数类型为Object）
  Method addMethod = clazz.getMethod("add", Object.class);
  // 4. 调用add()方法，添加非String类型数据（如Integer、Boolean）
  addMethod.invoke(list, 99); // 成功添加整数，绕过泛型约束
  addMethod.invoke(list, true); // 成功添加布尔值
  ```

4. 核心价值：支撑 Java 高级框架的通用设计

- **核心逻辑**：框架需具备 “通用性”（如对任意对象执行统一操作），而反射可在未知对象类型的情况下，动态解析对象的结构（类成分），从而实现通用功能。
- **主流框架关联**：Spring Boot、MyBatis 等框架的底层均依赖反射 —— 例如 Spring 的 “依赖注入（DI）”，通过反射扫描 Bean 的属性，自动注入依赖对象。

二、案例：对于任意一个对象，该框架都可以把对象的字段名和对应的值，保存到文件中去。

```java
public class SaveObjectFramework {
    public static void saveObeject(Object obj) throws Exception {
        //只有反射可以知道所获取的对象有多少个字段
        PrintStream ps=new PrintStream(new FileOutputStream("day-06/src/demo_2/obj.txt",true));
        Class c=obj.getClass();
        ps.println("=============="+c.getSimpleName()+"===========");
        Field[] fields=c.getDeclaredFields();

        for(Field f:fields){
            f.setAccessible(true);
            String fname=f.getName();
            Object fvalue=f.get(obj);
            ps.println(fname+"="+fvalue);
        }

        ps.close();
    }
}
```





## 第三章 注解

### 3.1 概述

 一、注解基础：概念与作用

1. 注解（Annotation）是 Java 代码中的**特殊标记**，语法以`@`开头，例如`@Override`（重写校验）、`@Test`（JUNIT 测试方法标记）、`@FunctionalInterface`（函数式接口标记）。

2. 作用：注解本身不直接执行逻辑，而是**为其他程序（如编译器、JVM、框架）提供标记信息**，让这些程序根据注解决定如何处理目标代码。

- 示例 1：`@Test`注解标记的方法，会被 JUNIT 框架识别并执行；未标记的方法则不执行。
- 示例 2：`@Override`注解告诉编译器 “校验该方法是否正确重写父类方法”，若重写错误（如方法名写错）则编译报错。

3. 注解的使用位置：注解可修饰 Java 中几乎所有成分，包括，

- 类、构造器、方法
- 成员变量、局部变量
- 方法参数等



二、自定义注解：语法与规则

Java 允许开发者自定义注解，用于满足特定业务场景（如框架开发、代码标记），核心语法与注意事项如下：

1. 基本格式：自定义注解通过`@interface`声明，本质是**继承了`java.lang.annotation.Annotation`接口的特殊接口**。

```java
// 自定义注解示例：MyBook注解
public @interface MyBook {
    // 1. 定义注解的"属性"（本质是接口的抽象方法）
    // 格式：返回值类型 属性名(); （必须带括号，区别于普通成员变量）
    String name();          // 无默认值的属性，使用时必须赋值
    int age() default 18;   // 有默认值的属性，使用时可省略赋值
    String[] address();     // 数组类型的属性
}
```

2. 注解属性的特殊规则

（1）注解的属性返回值只能是以下类型：

- 基本数据类型（int、boolean、char 等）
- String、Class、枚举（enum）
- 其他注解类型
- 以上类型的数组

（2）特殊属性`value`

若注解中**只有一个属性且属性名是`value`**，使用注解时可省略`value=`，直接赋值；若有多个属性或属性名非`value`，则必须显式写属性名。

```java
// 定义仅含value属性的注解
public @interface A {
    String value();
}

// 使用注解：可省略value=
@A("删除标记")  // 等价于 @A(value = "删除标记")
public class Test { }
```

（3）属性的默认值

通过`default`关键字为属性设置默认值，使用注解时若不赋值，则自动使用默认值；无默认值的属性必须显式赋值。

```java
// 有默认值的属性：age默认18
@MyBook(name = "Java编程思想", address = {"北京", "上海"})
public class Book { }

// 无默认值的属性：必须显式赋值
@MyBook(name = "Spring实战", age = 5, address = {"广州"})
public class Book2 { }
```

3. 自定义注解的本质

- 自定义注解本质是`interface MyBook extends java.lang.annotation.Annotation {}`
- 注解的 “属性” 本质是接口中的抽象方法，使用注解时赋值，本质是 “为抽象方法提供返回值”。

### 3.2 元注解

元注解是**用于修饰 “注解” 的注解**，作用是为自定义注解添加 “元信息”（如限制注解的使用位置、控制注解的保留周期）。

1. @Target：限制注解的使用位置

`@Target`用于声明 “自定义注解只能修饰哪些 Java 成分”，通过`ElementType`枚举指定位置，常用枚举值如下：

| ElementType 枚举值 |  可修饰的位置  |
| :----------------: | :------------: |
|        TYPE        | 类、接口、枚举 |
|       METHOD       |      方法      |
|       FIELD        |    成员变量    |
|     PARAMETER      |    方法参数    |
|    CONSTRUCTOR     |     构造器     |
|   LOCAL_VARIABLE   |    局部变量    |

**示例：限制`MyTest1`注解只能修饰方法**

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;

// 元注解@Target：限制MyTest1只能修饰方法
@Target(ElementType.METHOD)
public @interface MyTest1 { }

// 正确使用：修饰方法
public class Test {
    @MyTest1
    public void testMethod() { }

    // 错误使用：修饰类（编译报错，因为@Target限制了只能修饰方法）
    // @MyTest1
    public class InnerClass { }
}
```

若需注解可修饰多个位置，可通过数组形式指定多个`ElementType`值：

```java
// 允许MyTest1修饰“类”和“方法”
@Target({ElementType.TYPE, ElementType.METHOD})
public @interface MyTest1 { }
```

2. @Retention：控制注解的保留周期

`@Retention`用于声明 “注解在 Java 代码的哪个阶段保留”（即注解的生命周期），通过`RetentionPolicy`枚举指定，核心枚举值如下：

| RetentionPolicy 枚举值 |                 保留周期（生命周期）                  |                     适用场景                     |
| :--------------------: | :---------------------------------------------------: | :----------------------------------------------: |
|         SOURCE         |       仅保留在源码阶段，编译成 class 文件后删除       |           编译器校验（如`@Override`）            |
|    CLASS（默认值）     |        保留到 class 文件，但 JVM 运行时不加载         |                     极少使用                     |
|        RUNTIME         | 保留到 JVM 运行时，可通过反射获取注解信息（一直保存） | 框架开发（如 Spring、MyBatis），需运行时解析注解 |

框架开发中（如自定义权限注解、事务注解），需在运行时通过反射读取注解信息，因此必须用`@Retention(RetentionPolicy.RUNTIME)`：

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

// 元注解@Retention：保留到运行时
@Retention(RetentionPolicy.RUNTIME)
public @interface MyPermission {
    String value(); // 权限标识（如"admin"、"user"）
}
```



### 3.3 注解的解析

一、注解解析的定义

注解解析是指**判断类、方法、成员变量等程序元素上是否存在指定注解，若存在则提取注解中的内容（属性值）** 的过程。

- 核心目的：通过解析注解，为程序元素添加 “条件化逻辑”（存在注解则执行特殊处理，不存在则不处理），是框架（如 Spring、MyBatis）实现 “配置驱动” 的核心技术之一。

 二、注解解析的核心思想

解析注解的前提是**先获取目标程序元素的 “反射对象”**，再通过反射对象调用解析方法。具体对应关系如下：

| 目标程序元素 | 需先获取的反射对象 |
| :----------: | :----------------: |
|      类      |    `Class` 对象    |
|     方法     |   `Method` 对象    |
|   成员变量   |    `Field` 对象    |
|    构造器    | `Constructor` 对象 |

**关键原因**：`Class`、`Method`、`Field`、`Constructor` 等反射类均实现了 `AnnotatedElement` 接口，该接口提供了所有注解解析的核心方法。

三、核心 API（来自`AnnotatedElement`接口）

|                            方法名                            |                         方法作用                         |           返回值           |                           关键说明                           |
| :----------------------------------------------------------: | :------------------------------------------------------: | :------------------------: | :----------------------------------------------------------: |
| `isAnnotationPresent(Class<? extends Annotation> annotationClass)` |            判断当前元素是否存在指定类型的注解            |  `boolean`（true/false）   |               解析前常用此方法判断，避免空指针               |
|          `getAnnotation(Class<T> annotationClass)`           |             获取当前元素上指定类型的注解实例             | 泛型 `<T>`（注解类型实例） | 若注解不存在，返回`null`，需强转（部分场景因泛型设计可省略） |
|                  `getDeclaredAnnotations()`                  | 获取当前元素上 “直接声明” 的所有注解（不包含继承的注解） | `Annotation[]`（注解数组） |                 适合批量解析元素上的所有注解                 |
|                      `getAnnotations()`                      |        获取当前元素上的所有注解（包含继承的注解）        | `Annotation[]`（注解数组） | 实际开发中更常用`getDeclaredAnnotations()`，避免继承注解干扰 |



### 3.4 作用、应用场景

一、注解的核心作用

注解的本质是**对程序进行 “特殊标记”**，其核心价值在于：通过标记筛选目标元素（如方法、类、字段），让程序（或框架）能 “针对性处理” 标记元素，未标记元素则不执行特定逻辑 —— 这是注解所有应用场景的底层逻辑。

 二、注解的典型应用场景：模拟 JUnit 框架

JUnit 框架的核心功能是：**加了`@Test`注解的方法自动执行，未加注解的方法不执行**，其实现思路可复用于自定义注解场景

```java
public class TestRunner {
    public static void main(String[] args) throws Exception {
        // 1. 获取目标类的Class对象
        Class<?> clazz = AnnotationTest.class;
        // 2. 创建类的实例（用于调用非静态方法）
        Object obj = clazz.newInstance();
        // 3. 获取所有public方法
        Method[] methods = clazz.getMethods();
        
        // 4. 遍历方法，执行有@MyTest注解的方法
        for (Method method : methods) {
            // 判断方法是否有@MyTest注解
            if (method.isAnnotationPresent(MyTest.class)) {
                // 执行方法（无参，故传入null）
                method.invoke(obj, null);
            }
        }
    }
}
```

-  注解属性的价值：扩展控制逻辑

  通过给注解添加属性，可**进一步控制 “标记元素的执行规则”**，而非仅 “执行 / 不执行” 的二元判断。

```java
@MyTest(count = 5) // 执行5次
public void test3() {
    System.out.println("test3执行了");
}

for (Method method : methods) {
    if (method.isAnnotationPresent(MyTest.class)) {
        // 读取注解的count属性值
        MyTest myTest = method.getAnnotation(MyTest.class);
        int executeCount = myTest.count();
        
        // 按属性值循环执行方法
        for (int i = 0; i < executeCount; i++) {
            method.invoke(obj, null);
        }
    }
}
```



## 第四章 动态代理

 一、动态代理的基础认知

1. 定义与定位

- 动态代理是 Java 中的**设计模式**，属于 23 种设计模式中较难的一种，核心是通过 “代理对象” 间接访问 “被代理对象”，实现对被代理对象行为的增强或控制。
- 应用场景：后续大型技术（如 Spring AOP）及项目开发中高频使用，是框架底层核心技术之一。

2. 生活案例

以 “明星 - 经纪人” 为例，清晰区分**被代理对象**（明星）、**代理对象**（经纪人）的角色与职责：

| 角色               | 核心职责                                                     |
| ------------------ | ------------------------------------------------------------ |
| 被代理对象（明星） | 只负责核心业务（唱歌、跳舞），不处理非核心工作（准备设备、收钱）。 |
| 代理对象（经纪人） | 1. 对外提供与明星一致的行为（承接唱歌、跳舞业务）；2. 处理非核心工作（准备话筒 / 场地、收钱）；3. 核心业务触发时，调用明星执行。 |

- 关键结论：代理对象需 “模仿” 被代理对象的行为（保证对外接口一致），但只处理增强逻辑，核心业务仍由被代理对象执行。

二、动态代理的前置条件

动态代理的实现依赖**接口约束**，这是 Java 动态代理的核心规则，不可省略：

1. 定义行为接口：声明被代理对象需对外提供的核心行为（如明星的 “唱歌、跳舞”），接口是代理对象与被代理对象的 “行为契约”。
2. 被代理对象实现接口：被代理类（如`Star`类）必须实现上述接口，保证核心行为的具体实现。

3. `Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)`是动态代理的核心方法

| 参数名     | 作用                                                         | 示例写法                                  |
| ---------- | ------------------------------------------------------------ | ----------------------------------------- |
| loader     | 类加载器，用于加载 JVM 底层动态生成的 “代理类”（不可见），固定写法即可。 | `ProxyUtil.class.getClassLoader()`        |
| interfaces | 代理类需实现的接口数组，直接获取被代理对象的接口，保证行为一致。 | `star.getClass().getInterfaces()`         |
| h          | InvocationHandler 匿名内部类，定义代理的 “增强逻辑”（前置 / 后置处理）。 | 重写`invoke`方法，编写增强 + 核心调用逻辑 |

Java 通过`java.lang.reflect.Proxy`类（反射包下）实现动态代理，核心是调用其静态方法`Proxy.newProxyInstance(...)`生成代理对象，具体步骤分 4 步：

步骤 1：创建被代理类与接口

1. 行为接口（StarService）：约束核心行为

   ```java
   public interface StarService {
       void sing(String songName); // 唱歌
       String dance(); // 跳舞（有返回值）
   }
   ```

2. 被代理类（Star）：实现接口，编写核心业务逻辑

   ```java
   public class Star implements StarService {
       private String name; 
   
       // 构造器、get/set方法（略）
   
       // 实现唱歌方法（核心业务）
       @Override
       public void sing(String songName) {
           System.out.println(name + "表演唱歌：" + songName);
       }
   
       // 实现跳舞方法（核心业务）
       @Override
       public String dance() {
           System.out.println(name + "表演跳舞");
           return "谢谢"; // 返回结果
       }
   }
   ```

步骤 2：创建代理工具类（封装代理逻辑）

为避免代理代码分散，通常定义工具类（如`ProxyUtil`），提供静态方法生成代理对象，核心是调用`Proxy.newProxyInstance(...)`

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyUtil {
    // 静态方法：为指定明星对象生成代理对象，返回类型为行为接口（StarService）
    public static StarService createProxy(Star star) {
        // 调用Proxy.newProxyInstance生成代理对象，需传入3个参数
        StarService proxy = (StarService) Proxy.newProxyInstance(
                // 参数1：类加载器（用于加载底层生成的代理类，固定写法）
                ProxyUtil.class.getClassLoader(),
                // 参数2：代理类需实现的接口（直接获取被代理对象实现的接口，保证行为一致）
                star.getClass().getInterfaces(),
                // 参数3：InvocationHandler（匿名内部类，定义代理的增强逻辑）
                new InvocationHandler() {
                    /**
                     * 代理对象调用任何方法时，都会触发invoke方法执行
                     * @param proxy 代理对象本身（一般不用）
                     * @param method 被调用的方法（如sing、dance）
                     * @param args 被调用方法的参数（如sing的songName）
                     * @return 方法执行结果（需与被代理方法返回值一致）
                     * @throws Throwable 异常处理
                     */
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        Object result = null;
                        // 1. 增强逻辑：根据方法名区分不同行为的前置处理（代理的核心工作）
                        if ("sing".equals(method.getName())) {
                            // 唱歌的前置增强：准备话筒、收钱20万
                            System.out.println("准备话筒，收钱20万");
                        } else if ("dance".equals(method.getName())) {
                            // 跳舞的前置增强：准备场地、收钱100万
                            System.out.println("准备场地，收钱100万");
                        }

                        // 2. 调用被代理对象的核心方法（触发明星执行核心业务）
                        result = method.invoke(star, args); // 反射调用被代理对象的方法

                        // 3. 后置增强（可选，如记录日志等，本案例暂不实现）
                        return result; // 返回方法执行结果
                    }
                }
        );
        return proxy; // 返回生成的代理对象
    }
}
```

 步骤 3：使用代理对象

通过工具类获取代理对象后，**不直接调用被代理对象的方法**，而是调用代理对象的方法，触发增强逻辑 + 核心业务逻辑：

```java
public class Test {
    public static void main(String[] args) {
        // 1. 创建被代理对象（明星：张若楠）
        Star zhang = new Star("张若楠");

        // 2. 通过工具类获取代理对象
        StarService proxy = ProxyUtil.createProxy(zhang);

        // 3. 调用代理对象的方法（触发增强逻辑+核心业务）
        proxy.sing("红昭愿"); // 输出：准备话筒，收钱20万 → 张若楠表演唱歌：红昭愿
        String danceResult = proxy.dance(); // 输出：准备场地，收钱100万 → 张若楠表演跳舞：魅力四射
        System.out.println(danceResult); // 输出：谢谢谢谢（代理返回的结果）
    }
}
```

 四、动态代理的执行流程

调用代理对象方法时，完整流程如下：

1. **触发代理方法**：如`proxy.sing("红昭愿")`；
2. **回调 invoke 方法**：JVM 底层自动调用`InvocationHandler`的`invoke`方法，并传入 3 个参数（代理对象、当前方法、方法参数）；
3. **执行增强逻辑**：在`invoke`中，根据方法名（`method.getName()`）执行前置增强（如收钱、准备设备）；
4. **调用核心业务**：通过反射`method.invoke(star, args)`，触发被代理对象（明星）执行核心方法；
5. **返回结果**：将核心方法的执行结果返回给代理对象，再由代理对象返回给调用者。

五、关键问题与总结

1. **为什么必须用接口？**

   代理对象需通过接口 “模仿” 被代理对象的行为，保证对外提供的方法一致（多态特性），若无接口，JVM 无法确定代理对象需实现的行为，动态代理无法生成。

2. **增强逻辑写在哪？**

   所有增强逻辑（前置：收钱、准备设备；后置：日志、事务等）都写在`InvocationHandler`的`invoke`方法中，通过判断`method.getName()`区分不同方法的增强逻辑。

3. **动态代理的优势？**

   - **消除代码冗余**：将 “性能统计” 等通用逻辑抽离到代理中，所有业务方法复用同一套代理逻辑，无需重复编写。
   - **职责单一化**：被代理对象仅关注核心业务，非核心功能（如性能统计、日志、事务）由代理统一处理，代码结构更清晰。
   - **功能自动增强**：新增业务方法（如`select2()`）时，无需额外编写增强逻辑，代理会自动对新方法生效（只要新方法属于接口定义的方法）。
   - **低侵入性**：无需修改被代理对象的代码，即可通过代理新增功能，符合 “开闭原则”（对扩展开放，对修改关闭）。

六、动态代理与 Spring 框架、AOP 的关联

- **Spring 的本质：代理工厂**
  - 类比：将 Spring 理解为 “高级代理工厂”，开发者将 Java 对象（被代理对象）交给 Spring 管理，Spring 会返回一个 “增强后的代理对象”。
  - 例：若将`UserServiceImpl`交给 Spring，Spring 可自动为其生成代理对象，代理对象可新增 “事务管理、日志记录、权限校验” 等功能，无需开发者手动编写代理代码。
- **AOP（面向切面编程）的底层：动态代理**
  - AOP 思想：将 “性能统计、日志、事务” 等**横切逻辑**（跨越多个业务方法的通用逻辑）抽离为 “切面”，在不修改业务代码的情况下，将切面逻辑 “织入” 到业务方法的指定位置（如方法前、方法后）。

七、动态代理的通用化改造（泛型优化）

视频中为解决 “代理仅支持`UserService`” 的局限性，通过**泛型**将代理工具类改造为通用版本：

- 改造前：代理方法仅接收`UserService`类型的被代理对象，返回`UserService`类型的代理对象。
- 改造后：使用泛型`<T>`，代理方法定义为`public static <T> T createProxy(T target)`，支持任意实现接口的被代理对象（如`BookService`、`OrderService`），返回对应接口类型的代理对象。
- 核心原理：泛型自动适配被代理对象的接口类型，代理逻辑（如性能统计）与具体业务接口解耦，实现 “一套代理代码适配所有接口”。