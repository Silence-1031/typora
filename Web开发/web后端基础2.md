# Web后端基础2

## 第七章 事务管理

### 第一节 Spring事务

一、数据库事务基础回顾

1. 事务的定义

事务是**一组不可分割的数据库操作集合**，这组操作具有 “原子性”—— 要么同时成功（全部执行生效），要么同时失败（全部回滚，无任何影响），本质是**保证数据的一致性和完整性。**

2. 事务的核心操作（SQL 层面）

事务的控制需通过 3 步 SQL 指令完成，是 Spring 事务管理的底层逻辑基础：

1. **开启事务**：执行 `START TRANSACTION` 或 `BEGIN` 指令，标记后续操作进入事务范围；
2. **提交事务**：若所有操作执行成功，执行 `COMMIT` 指令，将事务内的操作永久写入数据库；
3. **回滚事务**：若任意操作执行失败（出现异常），执行 `ROLLBACK` 指令，撤销事务内所有已执行的操作，恢复到事务开启前的状态。

二、Spring 事务管理核心（@Transactional 注解）

Spring 框架通过封装事务操作，提供了简化的事务管理方案，核心是 `@Transactional` 注解，无需手动编写 `COMMIT`/`ROLLBACK` 逻辑。

1. @Transactional 注解的作用

- **自动开启事务**：在标注该注解的方法执行前，Spring 自动开启事务；
- 自动提交 / 回滚
  - 若方法无异常正常执行结束，Spring 自动提交事务；
  - 若方法执行过程中抛出异常，Spring 自动回滚事务；
- **解决数据一致性问题**：将多步数据库操作（如 “删部门 + 删部门下员工”）纳入同一事务，保证要么全成、要么全败。

2. @Transactional 注解的使用场景

并非所有方法都需要事务管理，仅需作用于**业务层（Service）的多步增删改操作**：

- ✅ 需加注解的场景：一个业务方法包含多个数据库操作（如 “解散部门” 需删除部门表数据 + 删除员工表中该部门的员工数据）；
- ❌ 无需加注解的场景：
  - 单步增删改操作：MySQL 默认自动提交事务，单步操作成功即提交，失败即回滚；
  - 查询操作：查询不会修改数据，无需事务控制。

3. @Transactional 注解的作用范围

注解可作用于**方法、类、接口**，优先级和推荐用法如下：

| 作用位置 | 效果                                 | 推荐度                                          |
| -------- | ------------------------------------ | ----------------------------------------------- |
| 方法上   | 仅当前方法受事务管理                 | ⭐⭐⭐（推荐）：精准控制需事务的方法，避免资源浪费 |
| 类上     | 该类中所有方法均受事务管理           | ⭐⭐：适合类中所有方法均需事务的场景              |
| 接口上   | 接口所有实现类的所有方法均受事务管理 | ⭐：不推荐，接口应专注定义方法，事务属于实现细节 |

4. 事务日志配置（可选）

为了直观观察 Spring 事务的执行过程（如 “开启事务”“回滚事务”），可在 SpringBoot 的 `application.yml` 配置文件中添加日志开关：

```yaml
logging:
  level:
    org.springframework.jdbc.support.JdbcTransactionManager: DEBUG
```

配置后，控制台会输出事务相关日志（如 “Transaction rolled back because of exception”），便于问题排查。



### 第二节 @Transaction的属性

#### 2.1 rollback属性

一、Spring 事务默认回滚规则

Spring 事务管理的核心注解`@Transactional`在**默认情况下，仅对 “运行时异常（RuntimeException 及其子类）” 触发事务回滚**，非运行时异常（如直接抛出的`Exception`）不会触发回滚，事务会正常提交。

关键案例对比

| 异常类型     | 具体示例                                             | 是否触发默认回滚 | 原理                                                         |
| ------------ | ---------------------------------------------------- | ---------------- | ------------------------------------------------------------ |
| 运行时异常   | `int a = 1 / 0`（算术运算异常`ArithmeticException`） | 是               | `ArithmeticException`是`RuntimeException`的子类，符合默认回滚规则 |
| 非运行时异常 | 直接`throw new Exception("出错了")`                  | 否               | 直接抛出的`Exception`属于 “受检异常”，默认不触发回滚，事务会提交 |

二、`rollbackFor`属性的作用与使用

当需要让**非运行时异常也触发事务回滚**（或指定特定异常回滚）时，需通过`@Transactional`的`rollbackFor`属性配置，其核心作用是 “明确指定哪些异常类型会触发事务回滚”。

1. 基础语法

在`@Transactional`注解中添加`rollbackFor`属性，值为目标异常的`.class`对象，示例：

```java
// 配置“所有异常（Exception及其子类）都触发回滚”
@Transactional(rollbackFor = Exception.class)
public void deleteDepartment(Long id) {
    // 业务逻辑：删除部门 + 删除部门下的员工
    departmentMapper.deleteById(id);
    // 模拟非运行时异常
    if (true) {
        throw new Exception("删除部门异常");
    }
    employeeMapper.deleteByDepartmentId(id);
}
```

2. 核心效果

配置`rollbackFor = Exception.class`后，无论抛出的是**运行时异常**还是**非运行时异常**，事务都会触发回滚，确保业务操作的原子性（要么全部成功，要么全部回滚）。

3. 实操验证（课程案例）

- **未配置`rollbackFor`时**：抛出`Exception`后，日志显示 “transaction commit”（事务提交），数据库中 “部门被删除但员工未删除”，数据不一致。
- **配置`rollbackFor = Exception.class`后**：抛出`Exception`后，日志显示 “transaction rollback”（事务回滚），数据库中 “部门和员工都未删除”，数据保持一致。



#### 2.2 propagation属性

一、事务传播行为的核心概念

事务传播行为是指**当一个事务方法（如`b()`）被另一个事务方法（如`a()`）调用时，被调用的事务方法（`b()`）如何进行事务控制**的规则。

- 场景示例：`a()`方法（加`@Transactional`）内部调用`b()`方法（加`@Transactional`），需确定`b()`是 “加入`a()`的事务” 还是 “新建独立事务”，这一决策由`propagation`属性控制。

二、6 种常见事务传播行为类型

`propagation`属性有 6 种取值，视频重点强调 “默认值`REQUIRED`” 和 “实战常用`REQUIRES_NEW`”，具体说明如下：

| 传播行为取值       | 核心含义                                                     | 适用场景                                                     |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `REQUIRED`（默认） | **需要事务**：- 若当前存在事务（如`a()`已开启事务），则加入该事务；- 若当前无事务，则新建事务。 | 绝大多数常规业务（如 “删除部门 + 删除部门下员工”，需保证操作原子性，要么全成、要么全滚）。 |
| `REQUIRES_NEW`     | **需要新事务**：- 无论当前是否存在事务，都会**挂起当前事务（若有）**，新建一个独立事务；- 新事务执行完毕后，再恢复原事务（若有）。 | 需 “事务隔离” 的场景（如 “记录操作日志”，无论主业务（解散部门）成功 / 失败，日志必须留存）。 |
| `SUPPORTS`         | **支持事务**：- 若当前有事务，加入事务；- 若当前无事务，在 “无事务状态” 下运行。 | 非核心辅助操作（如 “查询统计数据”，有无事务不影响结果正确性）。 |
| `NOT_SUPPORTED`    | **不支持事务**：- 无论当前是否有事务，都在 “无事务状态” 下运行；- 若当前有事务，先挂起事务，执行完后恢复。 | 无需事务的非核心操作（如 “记录非关键日志到本地文件”，不依赖数据库事务）。 |
| `MANDATORY`        | **必须有事务**：- 若当前有事务，加入事务；- 若当前无事务，直接抛出异常。 | 强依赖事务的核心操作（如 “转账扣减余额”，必须在事务中执行，否则不允许操作）。 |
| `NEVER`            | **必须无事务**：- 若当前无事务，正常运行；- 若当前有事务，直接抛出异常。 | 绝对不允许在事务中执行的操作（如 “读取静态配置文件”，事务会增加不必要开销）。 |

三、实战案例：解散部门 + 记录操作日志

视频通过 “解散部门需记录操作日志（无论成功 / 失败）” 的需求，对比`REQUIRED`和`REQUIRES_NEW`的差异，验证传播行为的实际效果，步骤如下：

1. 需求与初始问题

- 需求：解散部门（删除部门 + 删除部门下员工）+ 记录操作日志，**无论解散部门成功 / 失败，日志必须留存**。
- 初始代码问题：
  - 解散部门方法（`deleteDept()`）和记录日志方法（`insertLog()`）均加`@Transactional`，默认`propagation=REQUIRED`；
  - 若`deleteDept()`执行中抛出异常（如模拟 “除零错误”），`insertLog()`会 “加入`deleteDept()`的事务”，导致事务回滚时 “日志也被回滚”，不符合需求。

2. 解决方案：配置`propagation=REQUIRES_NEW`

- 关键修改：在`insertLog()`方法的`@Transactional`中指定`propagation=Propagation.REQUIRES_NEW`，代码示例：

  ```java
  @Transactional(propagation = Propagation.REQUIRES_NEW)
  public void insertLog(DeptLog deptLog) {
      deptLogMapper.insert(deptLog); // 插入日志到数据库
  }
  ```

- 效果验证：

  1. `deleteDept()`开启事务后，调用`insertLog()`时，会 “挂起`deleteDept()`的事务”，新建独立事务执行日志插入；
  2. 日志插入完成后，新事务提交（日志永久留存）；
  3. 恢复`deleteDept()`的事务，若后续抛出异常，仅`deleteDept()`的事务回滚（删除部门 / 员工操作取消），日志不受影响。

3. 辅助优化：`try-finally`保证日志执行

为确保 “即使`deleteDept()`抛出异常，`insertLog()`仍会执行”，需将`insertLog()`放入`finally`代码块（`finally`块无论是否抛异常，都会执行）：

```java
@Transactional
public void deleteDept(Integer id) {
    try {
        // 1. 删除部门
        deptMapper.deleteById(id);
        // 2. 删除部门下员工（模拟异常：int i = 1/0;）
        empMapper.deleteByDeptId(id);
    } finally {
        // 3. 记录日志（无论try块成功/失败，都会执行）
        DeptLog deptLog = new DeptLog();
        deptLog.setCreateTime(new Date());
        deptLog.setDescription("删除部门ID：" + id);
        deptLogService.insertLog(deptLog);
    }
}
```



## 第八章 AOP基础

### 第一节 入门

一、AOP 核心概念

1. **定义**：AOP（Aspect Oriented Programming）即**面向切面编程**（或面向方面编程），本质是**面向特定方法编程**，可在不修改原始方法代码的前提下，对原始方法进行功能增强或功能改变。
2. **核心思想**：解决传统编程中 “重复代码冗余” 问题（如统计多个方法执行耗时、统一日志记录等场景），将通用逻辑（如耗时统计）从业务方法中抽离，通过特定机制作用于目标方法。
3. **与动态代理的关系**：AOP 是一种编程思想，**动态代理技术是 AOP 思想最主流的实现方式**；Spring AOP 底层正是基于动态代理技术，对 Spring 容器管理的 Bean 的特定方法进行编程。

![image-20251115104251807](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115104251807.png)

二、Spring AOP 快速入门

1. 引入 AOP 依赖:在`pom.xml`中添加 Spring Boot AOP 起步依赖，Spring Boot 会自动加载 AOP 相关核心组件：

```xml
<!-- Spring Boot AOP起步依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

2. 编写 AOP 程序

（1）创建切面类

需添加 2 个关键注解，将类标识为 “Spring Bean” 且 “AOP 切面类”：

- `@Component`：将类交给 Spring IOC 容器管理，成为容器中的 Bean；
- `@Aspect`：声明该类是 AOP 切面类，用于封装通用逻辑（如耗时统计）。

（2）定义 “通知方法”（通用逻辑载体）

- **通知类型**：此处使用`@Around`（环绕通知），可在目标方法执行前、执行后都插入逻辑（适合耗时统计场景）；
- **参数要求**：方法形参必须包含`ProceedingJoinPoint`对象，通过其`proceed()`方法触发原始目标方法执行；

（3）指定 “切入点”（目标方法范围）

通过`@Around`注解的**切入点表达式**（如`execution(* com.itheima.service.*.*(..))`），明确通用逻辑作用的目标方法范围。

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

// 1. 标识为Spring Bean + AOP切面类
@Component
@Aspect
public class TimeAspect {
    // 日志对象（用于输出耗时信息）
    private static final Logger log = LoggerFactory.getLogger(TimeAspect.class);

    // 2. 环绕通知：统计方法执行耗时，切入点为service包下所有类的所有方法
    @Around("execution(* com.itheima.service.*.*(..))")
    public Object recordTime(ProceedingJoinPoint joinPoint) throws Throwable {
        // 2.1 目标方法执行前：记录开始时间（毫秒值）
        long startTime = System.currentTimeMillis();

        // 2.2 执行原始目标方法（如service层的list()、getById()等）
        Object result = joinPoint.proceed();

        // 2.3 目标方法执行后：记录结束时间，计算耗时
        long endTime = System.currentTimeMillis();
        long costTime = endTime - startTime;

        // 2.4 输出耗时信息（包含目标方法签名）
        log.info("方法 {} 执行耗时：{} 毫秒", joinPoint.getSignature(), costTime);

        // 2.5 返回原始方法的返回值（确保业务正常响应）
        return result;
    }
}
```

3. 切入点表达式基础:`execution(返回值范围 包名.类名/接口名.方法名(参数范围))`

- ```java
  execution(* com.itheima.service.*.*(..))
  ```

  - `*`（第一个）：返回值不限（可改为`java.lang.String`等具体类型）；
  - `com.itheima.service`：目标方法所在的包（如改为`com.itheima.controller`则作用于控制层）；
  - `*`（第二个）：包下所有类 / 接口（如改为`DeptService`则仅作用于该类）；
  - `*`（第三个）：类 / 接口下所有方法（如改为`list`则仅作用于`list()`方法）；
  - `(..)`：方法参数不限（无参数、单个参数、多个参数均可）。

三、AOP 的优势与应用场景

1. 核心优势

- **代码无侵入**：不修改原始业务方法代码，即可增强功能；
- **减少重复代码**：通用逻辑（如耗时统计、日志、权限校验）只需编写一次；
- **开发效率提升**：专注业务逻辑开发，通用逻辑通过 AOP 统一管理；
- **维护便捷**：通用逻辑需修改时，仅需调整切面类，无需改动所有目标方法。

2. 典型应用场景

- 方法执行耗时统计
- 统一操作日志记录（记录 “谁、何时、执行了哪个方法、传入参数、返回值”）；
- 权限控制（如判断用户是否有权执行目标方法）；
- Spring 事务管理（底层基于 AOP：目标方法执行前开启事务，执行后提交 / 回滚）。



### 第二节 核心概念

 一、AOP 核心概念

| 概念名称 |     英文      |                           核心定义                           |                 案例对应（统计方法耗时场景）                 |
| :------: | :-----------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  连接点  |  Join Point   | **可以被 AOP 控制的所有方法**（Spring AOP 中仅支持方法级别的连接点），封装了方法执行时的相关信息。 | 入门程序中所有业务方法（如部门管理的`list`、`delete`、`add`等方法），均属于可被 AOP 控制的连接点。 |
|  切入点  |   Point Cut   | **实际被 AOP 控制的方法**，即 “需要增强的方法”，通过 “切入点表达式” 定义匹配规则。 | 若仅统计`DeptServiceImpl`类的`list`方法耗时，则`list`方法是切入点；若统计该类所有方法，则所有方法均为切入点。 |
|   通知   |    Advice     | **抽取出来的共性功能代码**，即 “要做的增强操作”，最终以方法形式存在，需指定 “执行时机”。 | 统计耗时的逻辑：“方法执行前记录开始时间→方法执行后记录结束时间→计算耗时”，这部分代码封装的方法就是通知（视频中为 “环绕通知”）。 |
|   切面   |    Aspect     | **通知与切入点的对应关系**，描述 “对哪个方法（切入点）、在什么时候、做什么增强（通知）”，是 AOP 的核心 “配置载体”。 | “对`DeptServiceImpl.list`方法（切入点），在方法执行前后（环绕时机），执行耗时统计（通知）”，这一整套对应关系就是切面。 |
| 目标对象 | Target Object | **通知所作用的原始对象**，即被增强的业务对象（如`DeptServiceImpl`实例）。 | 耗时统计通知作用在`DeptServiceImpl`对象上，该对象就是目标对象。 |

![image-20251115112850917](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115112850917.png)

二、AOP 执行流程（底层：动态代理）

- 核心是 “代理对象替代目标对象，实现功能增强”：

1. **生成代理对象**：程序运行时，Spring AOP 基于动态代理技术，为目标对象自动生成 “代理对象”。
2. 代理对象增强逻辑：代理对象会 “包裹” 目标对象的原始方法，将通知逻辑嵌入执行流程：
   - 步骤 1：执行通知的前置逻辑（如记录方法开始时间）；
   - 步骤 2：调用目标对象的原始方法（如`DeptServiceImpl.list`的业务逻辑）；
   - 步骤 3：执行通知的后置逻辑（如记录结束时间、计算耗时）。
3. **注入代理对象**：Spring 容器中，不再注入原始的目标对象，而是注入代理对象；后续代码调用业务方法时，实际调用的是代理对象的增强方法。

> 在 Controller 中注入的`DeptService`实例是代理对象，调用`list`方法时，先进入通知的断点（记录开始时间），再进入目标对象的`list`方法，最后回到通知的断点（计算耗时），与上述流程完全一致。

### 第三节 通知类型

一、Spring AOP 支持的 5 种通知类型

Spring AOP 提供 5 种通知类型，核心区别在于**执行时机**和**触发条件**，每种类型对应专属注解

|       通知类型       |       注解        |                      执行时机                      |                           核心特点                           |
| :------------------: | :---------------: | :------------------------------------------------: | :----------------------------------------------------------: |
|       前置通知       |     `@Before`     |              目标方法**运行之前**执行              |     无返回值，仅在目标方法执行前触发，不影响目标方法本身     |
|       环绕通知       |     `@Around`     | 目标方法**运行前后**均可执行（需手动调用目标方法） | 功能最强：可控制目标方法是否执行、修改参数 / 返回值；需显式调用 `proceed()` 方法触发目标方法 |
| 后置通知（最终通知） |     `@After`      |              目标方法**运行之后**执行              | 无论目标方法是否正常执行（即使抛异常），都会触发，常用于资源释放 |
|      返回后通知      | `@AfterReturning` |      目标方法**正常执行完毕并返回结果后**执行      |       仅在目标方法无异常时触发，可获取目标方法的返回值       |
|    抛出异常后通知    | `@AfterThrowing`  |        目标方法**执行过程中抛出异常后**执行        |           仅在目标方法抛异常时触发，可捕获异常信息           |

二、关键注意事项与执行逻辑验证

1. 通知执行逻辑

| 测试场景             | 执行的通知类型                                               | 未执行的通知类型               | 原因分析                                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------ | ------------------------------------------------------------ |
| 目标方法**正常执行** | 前置通知、环绕通知（前半段）、返回后通知、后置通知、环绕通知（后半段） | 抛出异常后通知                 | 返回后通知仅在无异常时触发，异常通知仅在有异常时触发（二者互斥） |
| 目标方法**抛出异常** | 前置通知、环绕通知（前半段）、抛出异常后通知、后置通知       | 返回后通知、环绕通知（后半段） | 异常中断目标方法执行，环绕通知后半段代码无法执行；返回后通知因异常不触发 |

2. 环绕通知的特殊要求

环绕通知是唯一需要手动控制目标方法执行的通知类型，必须满足 2 个条件：

- **手动触发目标方法**：需在通知方法参数中声明 `ProceedingJoinPoint` 对象，并调用其 `proceed()` 方法（否则目标方法不会执行）。
- **返回值类型为 Object**：`proceed()` 方法会返回目标方法的执行结果，需将该结果返回（若设为 `void`，调用方将无法获取目标方法的返回值）。

三、切入点表达式的抽取与复用

入门阶段需在每个通知注解中重复编写切入点表达式（如 `execution(* com.itheima.service.impl.*.*(..))`），存在代码冗余问题，解决方案是**抽取公共切入点表达式**：

- 抽取步骤

1. 定义一个**无参、返回值为 void** 的方法（方法名自定义，如 `pt()`）。

2. 在该方法上添加`@Pointcut`注解，并在注解中编写公共切入点表达式。

   ```java
   // 抽取公共切入点表达式
   @Pointcut("execution(* com.itheima.service.impl.*.*(..))")
   public void pt() {} // 方法体为空，仅用于承载 @Pointcut 注解
   ```

3. 在其他通知注解中，通过「方法名 ()」引用该表达式：

   ```java
   // 引用抽取的切入点表达式
   @Before("pt()")
   public void beforeAdvice(JoinPoint joinPoint) {
       System.out.println("前置通知执行...");
   }
   ```

   

- 跨切面类引用规则

1. **当前切面类内引用**：方法权限修饰符可设为 `private` 或 `public`（推荐 `private`，避免外部误调用）。

2. 其他切面类引用：需将抽取方法的权限修饰符改为`public`，并通过「全类名。方法名 ()」引用：

```java
// 在其他切面类中引用
@Around("com.itheima.aspect.MyAspect.pt()") // 全类名.方法名()
public Object aroundAdvice(ProceedingJoinPoint pjp) throws Throwable {
    // 环绕通知逻辑
}
```



### 第四节 通知顺序

一、核心问题背景

当项目中存在**多个切面类**，且这些切面的切入点表达式均匹配到**同一个目标方法**时，目标方法执行过程中会触发所有切面的通知方法。此时需明确：**不同切面的通知（如前置、后置）究竟按什么顺序执行**。

二、默认执行顺序

默认情况下，通知执行顺序由**切面类的类名字母 / 数字排序**决定，且前置通知与后置通知的顺序逻辑相反，具体规则如下：

| 通知类型            | 执行顺序规则                                    | 示例验证（切面类：MyAspect2、MyAspect3、MyAspect4） |
| ------------------- | ----------------------------------------------- | --------------------------------------------------- |
| 前置通知（@Before） | 类名排序越靠前，通知越先执行（字母 / 数字升序） | 执行顺序：MyAspect2 → MyAspect3 → MyAspect4         |
| 后置通知（@After）  | 类名排序越靠前，通知越后执行（字母 / 数字降序） | 执行顺序：MyAspect4 → MyAspect3 → MyAspect2         |

**关键说明**

- 类名排序遵循常规字符排序规则（如数字`2`<`3`<`4`，字母`A`<`B`<`C`）；
- 若修改切面类名（如将`MyAspect2`改为`MyAspect5`），重新运行后通知顺序会随类名排序变化，进一步验证该规则。

三、自定义执行顺序（基于 @Order 注解）

默认类名排序方式繁琐且不易管理，Spring 提供`@Order`注解可直接控制切面优先级，进而定义通知执行顺序，规则如下：

1. @Order 注解使用方式

在**切面类上**添加`@Order(数字)`，通过数字指定优先级（数字为整数，可正可负）。

2. 执行顺序规则

| 通知类型            | 执行顺序规则                           | 示例验证（切面类 +@Order：4 (@1)、5 (@2)、3 (@3)） |
| ------------------- | -------------------------------------- | -------------------------------------------------- |
| 前置通知（@Before） | **数字越小，优先级越高，通知越先执行** | 执行顺序：4 (@1) → 5 (@2) → 3 (@3)                 |
| 后置通知（@After）  | **数字越小，优先级越高，通知越后执行** | 执行顺序：3 (@3) → 5 (@2) → 4 (@1)                 |

**示例场景**

若需让`MyAspect4`的前置通知先执行，`MyAspect5`次之，`MyAspect3`最后执行：

- 在`MyAspect4`类上加`@Order(1)`；
- 在`MyAspect5`类上加`@Order(2)`；
- 在`MyAspect3`类上加`@Order(3)`；
- 运行结果：前置通知按`4→5→3`执行，后置通知按`3→5→4`执行，完全符合自定义需求。



### 第五节 切入点表达式

#### 5.1 execution表达式

一、切入点表达式的核心作用

切入点表达式是 AOP 开发的 “定位器”，用于**明确项目中哪些目标方法需要应用自定义的通知（如前置通知、后置通知等）** ，是连接 “通知逻辑” 与 “目标方法” 的关键桥梁。

两类常用表达式：`execution`（基于方法签名匹配）和`annotation`（基于注解匹配）

![image-20251115121627757](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115121627757.png)

二、execution 表达式的完整语法

![image-20251115121724664](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115121724664.png)

1. 各部分含义解析

|    语法组成     |                             说明                             |                示例                 |
| :-------------: | :----------------------------------------------------------: | :---------------------------------: |
|   访问修饰符    |         可选，如`public`、`private`等，**通常省略**          |              `public`               |
|     返回值      |  必选，指定方法返回值类型，可通过通配符`*`表示 “任意返回值”  |        `void`、`String`、`*`        |
|   包名。类名    | 可选，指定目标方法所在的包和类，**不建议省略**（避免匹配范围过大） |   `com.it黑马.service.DptService`   |
|     方法名      |        必选，指定目标方法名，可通过通配符匹配部分名称        |  `delete`、`find*`（以 find 开头）  |
|    参数类型     | 必选，指定方法参数的全类名，无参数留空，可通过通配符匹配任意参数 | `(java.lang.Integer)`、`()`（无参） |
| throws 异常类型 | 可选，指定方法声明抛出的异常（非实际抛出的异常），**通常省略** |        `throws SQLException`        |

2. 可省略的部分

语法中带`?`的部分可省略，实际开发中常见省略场景：

- 访问修饰符：如省略`public`，直接写 “返回值 包名.类名。方法名 (参数)”；
- 包名.类名：可省略（但不推荐），仅保留 “返回值 方法名 (参数)”，会导致匹配范围过大；
- throws 异常类型：几乎不指定，因多数场景无需通过异常筛选目标方法。

三、execution 表达式的关键通配符

`execution`支持两个核心通配符，用于灵活匹配多个目标方法，解决 “单个表达式匹配多个方法” 的需求：

1. 通配符`*`：**单个**独立的任意符号

- **作用**：匹配 “单个层级、单个类型” 的任意内容，可用于返回值、包名、类名、方法名、参数类型；
- 使用场景
  1. 匹配任意返回值：`* com.itheima.service.DptService.delete(...)`（返回值为任意类型）；
  2. 匹配单个任意包 / 类：`void com.itheima.*.DptService.delete(...)`（第 3 级包为任意）；
  3. 匹配任意方法名前缀 / 后缀：`void com.itheima.service.DptService.find*(...)`（匹配所有以`find`开头的方法）；
  4. 匹配单个任意参数：`void com.it黑马.service.DptService.delete(*)`（方法**有且仅有 1 个任意类型参数**）。

2. 通配符`..`：任意层级 / 任意数量的内容

- **作用**：仅用于两个场景 ——“任意层级的包” 和 “任意数量、任意类型的参数”；
- 使用场景
  1. 匹配任意层级的包：`void com..DptService.delete(...)`（`com`包下任意层级的子包都匹配，如`com.it黑马.service`、`com.service`）；
  2. 匹配任意参数（含无参）：`void com.it黑马.service.DptService.delete(..)`（方法无参、1 个参数、多个参数均匹配）。

3. 通配符组合示例

| 需求                              | 表达式示例                              |
| --------------------------------- | --------------------------------------- |
| 匹配`DptService`所有方法          | `* com.it黑马.service.DptService.*(..)` |
| 匹配`service`包下所有类的所有方法 | `* com.it黑马.service.*.*(..)`          |
| 匹配项目中所有方法（不推荐）      | `* *(..)`                               |

四、多方法匹配：表达式组合（`||`/`&&`/`!`）

当需要用**一个表达式匹配多个无关联的方法**（如同时匹配`list()`和`delete(Integer)`）时，可通过逻辑运算符组合多个基础表达式：

- **`||`（或）**：匹配满足任意一个表达式的方法；
- **`&&`（与）**：匹配同时满足两个表达式的方法（较少用）；
- **`!`（非）**：匹配不满足表达式的方法（较少用）。

示例：同时匹配`list()`和`delete(Integer)`

```java
execution(* com.it黑马.service.DptService.list()) || execution(* com.it黑马.service.DptService.delete(java.lang.Integer))
```

五、实践书写建议

1. 方法命名规范，降低匹配难度

开发中统一方法命名风格（如查询方法以`find`开头、更新方法以`update`开头），可通过`*`通配符快速匹配一类方法，示例：

匹配所有查询方法：`* com.it黑马.service.*.find*(..)`。

2. **基于接口描述，增强扩展性**

优先通过 “接口” 而非 “实现类” 编写表达式，如：

- 推荐：`* com.it黑马.service.DptService.delete(..)`（基于`DptService`接口）；
- 不推荐：`* com.it黑马.service.impl.DptServiceImpl.delete(..)`（基于实现类，若实现类替换则表达式失效）。

3. 缩小匹配范围，避免性能损耗

- 不省略 “包名。类名”：避免`* *(..)`这类 “匹配所有方法” 的写法，会导致 AOP 扫描所有方法，降低性能；
- 慎用`..`通配包：如需匹配子包，尽量明确包层级（如`com.it黑马.service..*`），而非直接`com..*`。



#### 5.2 annotation表达式

一、@annotation 表达式的核心作用

用于**匹配被特定注解标识的方法**，仅对添加了目标注解的方法生效。

- 场景适配：当需要匹配的方法无统一命名规则（如同时匹配`list`和`delete`方法），`execution`需组合多个表达式（繁琐），而`@annotation`只需通过 “注解标识” 即可快速匹配，简化配置。

二、使用步骤（完整实操流程）

1. 自定义 “标识性注解”

需添加两个元注解（描述注解的注解），确保注解能在运行时生效且作用于方法：

| 元注解       | 作用说明                                                     | 配置值                                |
| ------------ | ------------------------------------------------------------ | ------------------------------------- |
| `@Retention` | 指定注解的生效阶段（源码 / 编译期 / 运行时），AOP 需**运行时识别**，故必须设为`RUNTIME` | `@Retention(RetentionPolicy.RUNTIME)` |
| `@Target`    | 指定注解可作用的位置（类 / 方法 / 字段等），此处仅需作用于方法 | `@Target(ElementType.METHOD)`         |

**示例代码**：

```java
// 自定义注解：MyLog（仅作标识，无需属性）
@Retention(RetentionPolicy.RUNTIME)  // 运行时生效
@Target(ElementType.METHOD)         // 仅作用于方法
public @interface MyLog {
    // 无额外属性，仅用于标记方法
}
```

2. 在目标方法上添加自定义注解

需被 AOP 拦截的方法，直接添加步骤 1 定义的注解（如`@MyLog`）：

```java
// 示例：给list和delete方法添加@MyLog，仅这两个方法会被AOP匹配
public class BusinessService {
    @MyLog  // 标记该方法，会被@annotation表达式匹配
    public void list() {
        // 业务逻辑
    }

    @MyLog  // 标记该方法，会被@annotation表达式匹配
    public void delete() {
        // 业务逻辑
    }

    // 未加@MyLog，不会被匹配
    public void add() {
        // 业务逻辑
    }
}
```

3. 编写 @annotation 切入点表达式

表达式格式：`@annotation(自定义注解的全类名)`，直接指向步骤 1 定义的注解。

**示例代码（AOP 切面类）**：

```java
@Aspect  // 标识为切面类
@Component
public class MyAspect {
    // 切入点表达式：匹配所有添加了@MyLog注解的方法
    @Pointcut("@annotation(com.at.heimat.aop.MyLog)")  // 全类名需准确
    public void myPointcut() {}

    // 通知方法（如前置通知），关联切入点
    @Before("myPointcut()")
    public void beforeAdvice() {
        System.out.println("AOP通知执行：被@MyLog标记的方法即将运行");
    }
}
```

三、关键特性与优势

1. **灵活性高**：新增需拦截的方法时，无需修改切入点表达式，仅需在方法上添加`@MyLog`注解即可。
2. **语义清晰**：通过注解直接标识目标方法，代码可读性比复杂的`execution`组合表达式更高。
3. **精准匹配**：仅对添加注解的方法生效，避免`execution`表达式可能出现的 “误匹配”（如按命名规则匹配时误包含其他方法）。

四、与 execution 表达式的对比

| 对比维度   | @annotation 表达式                              | execution 表达式                                       |
| ---------- | ----------------------------------------------- | ------------------------------------------------------ |
| 匹配逻辑   | 按 “方法是否有特定注解” 匹配                    | 按 “方法的访问修饰符、返回值、类名、方法名、参数” 匹配 |
| 适用场景   | 无规则方法（如 list、delete）、动态调整拦截范围 | 有统一规则的方法（如所有以 “find” 开头的查询方法）     |
| 配置复杂度 | 简单（仅需注解全类名）                          | 复杂（需拼接完整方法描述，多方法需组合表达式）         |
| 动态扩展性 | 高（新增方法仅需加注解）                        | 低（新增方法需修改表达式）                             |



### 第六节 连接点

一、连接点的基础定义

1. 核心概念:连接点是 “可被 AOP 控制的方法”，在 Spring AOP 中特指方法的执行（即目标对象中所有可能被 AOP 拦截并增强的方法）。

2. 抽象类：JoinPoint

   Spring 通过`org.aspectj.lang.JoinPoint`接口对连接点进行抽象，开发者可通过该接口的实现类对象，获取目标方法执行时的关键信息（如类名、方法名、参数、返回值等）。

二、不同通知类型中的连接点使用差异

Spring AOP 的通知类型分为 “环绕通知” 和 “其他四种通知（前置、后置、返回后、异常后）”，二者获取连接点的方式不同，核心区别如下：

| 通知类型                      | 连接点抽象类        | 核心特点                                                     |
| ----------------------------- | ------------------- | ------------------------------------------------------------ |
| 环绕通知（Around）            | ProceedingJoinPoint | 1. 是`JoinPoint`的**子类**，拥有`JoinPoint`的所有功能；2. 额外提供`proceed()`方法，用于 “放行原始目标方法执行”；3. 必须使用该类才能获取环绕通知中的连接点信息。 |
| 前置 / 后置 / 返回后 / 异常后 | JoinPoint           | 1. 直接使用`JoinPoint`接口；2. 需在通知方法形参中声明`JoinPoint`类型参数；3. 无法获取目标方法返回值（前置通知）或需通过特定方式获取（返回后通知）。 |

![image-20251115131004971](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115131004971.png)

![image-20251115131026080](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115131026080.png)

三、连接点的核心功能：获取目标方法信息

通过`JoinPoint`（或其子类`ProceedingJoinPoint`）的方法，可获取目标方法执行时的 5 类关键信息，视频中通过环绕通知演示了具体实现：

|       需获取的信息        |                         核心方法调用                         |                             说明                             |
| :-----------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     1. 目标对象的类名     |         `joinPoint.getTarget().getClass().getName()`         |     `getTarget()`获取目标对象实例，再通过反射获取类名。      |
|    2. 目标方法的方法名    |             `joinPoint.getSignature().getName()`             | `getSignature()`获取方法签名（包含方法名、参数类型等），再提取方法名。 |
| 3. 目标方法的入参（数组） |                    `joinPoint.getArgs()`                     |  返回`Object[]`数组，需通过`Arrays.toString()`格式化输出。   |
| 4. 目标方法的执行（放行） |          `proceedJoinPoint.proceed()`（仅环绕通知）          | 调用该方法触发原始目标方法执行，返回值为目标方法的返回结果。 |
|    5. 目标方法的返回值    | 接收`proceed()`方法的返回值（如`Object result = proceedingJoinPoint.proceed()`） | 仅环绕通知可直接获取；其他通知需通过`AfterReturning`的`returning`参数获取。 |

四、关键注意事项

1. 包导入正确:`JoinPoint`必须导入`org.aspectj.lang.JoinPoint`包，避免导入其他同名类导致错误。
2. 环绕通知的返回值处理:环绕通知中若未返回`proceed()`的结果（如直接返回`null`），会导致目标方法的返回值丢失；需将`result`作为环绕通知的返回值返回，否则上层调用无法获取结果。
3. 不同通知的信息获取限制
   - 前置通知：因在目标方法执行前触发，**无法获取返回值**；
   - 异常后通知：仅能获取异常信息，无法获取正常返回值。



## 第九章 Springboot原理

### 第一节 配置优先级

一、SpringBoot 常见配置方式

![image-20251115132235751](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115132235751.png)

SpringBoot 支持 5 种核心属性配置方式，可分为 “配置文件类” 和 “外部参数类” 两大类：

| 类别       | 配置方式               | 格式 / 语法示例                          | 适用场景                               |
| ---------- | ---------------------- | ---------------------------------------- | -------------------------------------- |
| 配置文件类 | application.properties | `server.port=8081`（键值对直接赋值）     | 简单属性配置，格式直观                 |
| 配置文件类 | application.yml        | `server: port: 8082`（缩进式层级结构）   | 复杂配置（如嵌套属性），企业主流       |
| 配置文件类 | application.yaml       | 同 yml（仅文件名后缀差异，语法完全一致） | 与 yml 功能等效，使用频率较低          |
| 外部参数类 | Java 系统属性          | 前缀 `-D`，如 `-Dserver.port=9000`       | 开发环境临时配置（IDEA 中 VM Options） |
| 外部参数类 | 命令行参数             | 前缀 `--`，如 `--server.port=10010`      | 项目打包后运行时动态配置               |

二、配置优先级核心规则（从高到低）

当多种方式配置**同一属性**（如 `server.port`）时，优先级高的配置会覆盖优先级低的，最终生效顺序如下（从高到低）：

1. **命令行参数**
   - 优先级最高，无论项目是开发中（IDEA 的 Program Arguments）还是已打包（运行 jar 时追加参数），均会覆盖其他配置。
   - 示例：`java -jar xxx.jar --server.port=10010`（打包后运行，端口 10010 生效）。
2. **Java 系统属性**
   - 优先级次之，需通过 `-D` 前缀指定，支持开发环境（IDEA 的 VM Options）和打包后运行。
   - 示例：IDEA 中配置 VM Options 为 `-Dserver.port=9000`，启动后端口 9000 生效（若未配置命令行参数）。
3. **application.properties**
   - 配置文件中优先级最高，键值对格式，直接覆盖 yml/yaml 的同名配置。
   - 示例：若 3 个配置文件均配 `server.port`，仅 properties 的`8081`生效。
4. **application.yml**
   - 配置文件中优先级次之，缩进式层级结构，覆盖 yaml 的同名配置。
   - 示例：注释 properties 后，yml 的`8082`生效。
5. **application.yaml**
   - 配置文件中优先级最低，与 yml 语法完全一致，仅文件名差异。
   - 示例：注释 properties 和 yml 后，yaml 的`8083`生效。

三、关键实操细节

1. **IDEA 中配置外部参数**
   - Java 系统属性：进入 `Edit Configurations` → 配置 `VM Options`（如 `-Dserver.port=9000`）。
   - 命令行参数：进入 `Edit Configurations` → 配置 `Program Arguments`（如 `--server.port=10010`）。
2. **项目打包后配置外部参数**
   - 前提：SpringBoot 项目打包需依赖 `spring-boot-maven-plugin`（官方骨架创建的项目自动引入，无需手动添加）。
   - 打包操作：执行 Maven 生命周期的 `package` 命令，生成可运行 jar 包（含所有依赖）。
   - 运行时配置：
     - Java 系统属性：`java -Dserver.port=9000 -jar xxx.jar`
     - 命令行参数：`java -jar xxx.jar --server.port=10010`
   - ![image-20251115132625507](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115132625507.png)
3. **开发规范建议**
   - 项目中**统一使用一种配置文件格式**，企业主流选择 `application.yml`（层级清晰，支持复杂配置）。
   - 避免同一属性在多配置文件中重复定义，减少优先级冲突排查成本。



### 第二节 Bean管理

#### 2.1 获取Bean

一\ 3 种手动获取 Bean 的方式

要手动获取 Bean，**第一步需先获取 IOC 容器对象（`ApplicationContext`）**通过`ApplicationContext`提供的`getBean()`重载方法，实现 3 种获取 Bean 的方式，具体对比如下：

|       获取方式       |         核心参数         |             优点              |               缺点               |
| :------------------: | :----------------------: | :---------------------------: | :------------------------------: |
| 根据 Bean 的名称获取 | Bean 的默认 / 自定义名称 | 可避免 “同类型多个 Bean” 冲突 | 返回值为`Object`，需强制类型转换 |
| 根据 Bean 的类型获取 |    Bean 的 Class 对象    |    无需强制转换，类型安全     |   若同类型有多个 Bean，会报错    |
| 根据名称 + 类型获取  |    名称 + Class 对象     | 兼顾 “避免冲突” 和 “类型安全” |    需同时指定两个参数，略繁琐    |

```java
class SpringbootWebConfig2ApplicationTests {
	@Autowired
	private Context0申请Context applicationContext; //IOC容器对象
    
	@ Test
	public void testGetBean(){
		//根据bean的名称获取
		DeptController bean1=(DeptController)applicationContext.getBean(s:"deptController");
		System.out.println(bean1);
		//根据bean的类型获取
		DeptController bean2= applicationContext.getBean(DeptController.class);
		System.out.println(bean2);
		//根据bean的名称及类型获取
		DeptController bean3= applicationContext.getBean(s:"deptController", DeptController.class);
		System.out.println(bean3);
	}
}
```

**关键补充：Bean 的默认名称规则**

若声明 Bean 时未通过`@Component("自定义名称")`指定名称，**默认名称为 “类名首字母小写”**（如`DeptController`的默认 Bean 名称是`deptController`），这是 “根据名称获取 Bean” 的核心前提。

三、Bean 的默认特性：默认单例（Singleton）

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115134010377.png" alt="image-20251115134010377" style="zoom:67%;" />

- **默认情况下，Spring IOC 容器中的 Bean 是单例的**：即容器初始化时只创建 1 个 Bean 实例，后续无论调用多少次`getBean()`，获取的都是同一个实例。

四、特殊说明：Bean 的初始化时机

“Spring Boot 启动时创建所有 Bean” 并非绝对，需满足两个条件：

1. Bean 的作用域为默认的 “单例（Singleton）”；
2. Bean 未配置 “延迟加载”（即未添加`@Lazy`注解）。

若不满足上述条件，Bean 的初始化时机会延迟（如多例 Bean 在每次`getBean()`时创建），具体逻辑将在 “Bean 的作用域” 和 “延迟加载” 章节详细讲解。



#### 2.2 Bean的作用域

一、Bean 作用域的核心概念

Bean 的作用域定义了 **Bean 在 Spring IoC 容器中的生命周期和实例数量规则**，即容器中同名称的 Bean 是 “单例”（全局唯一）还是 “多例”（每次使用新建），或在特定环境（如 Web）下的实例管理规则。

二、Spring 支持的 5 种 Bean 作用域

Spring 共提供 5 种作用域，其中 **前 2 种为通用场景（重点掌握），后 3 种仅在 Web 环境生效（了解即可）**，具体如下表：

| 作用域类型            |     适用环境     |                           核心特点                           |
| --------------------- | :--------------: | :----------------------------------------------------------: |
| **singleton（单例）** | 所有环境（默认） | 1. 整个 IoC 容器中，同名称的 Bean 仅存在 **1 个实例**；2. 默认在 **容器启动时初始化**（提前创建，全局复用）。 |
| **prototype（多例）** |     所有环境     | 1. 每次通过 `getBean()` 获取 Bean 或注入时，**都会创建新实例**；2. 初始化时机：**首次使用时创建**（容器启动不预创建）。 |
| request               |     Web 环境     |   每个 HTTP 请求对应 1 个 Bean 实例，请求结束后实例销毁。    |
| session               |     Web 环境     | 每个用户会话（Session）对应 1 个 Bean 实例，会话失效后实例销毁。 |
| application           |     Web 环境     | 整个 Web 应用（ServletContext）对应 1 个 Bean 实例，应用停止后实例销毁。 |

三、Bean 作用域的配置方式

通过 Spring 提供的 **`@Scope` 注解** 配置 Bean 的作用域，直接标注在 Bean 类上（如 Service、Controller 类），语法如下：

```java
// 1. 配置为单例（默认，可省略 @Scope 注解）
@Scope("singleton") // 或 @Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
@Controller
public class DptController { ... }

// 2. 配置为多例
@Scope("prototype") // 或 @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
@Controller
public class DptController { ... }
```

四、Bean 的初始化时机（重点）

初始化时机决定 Bean 何时被创建，核心受 **作用域类型** 和 **`@Lazy` 延迟注解** 影响，分两种场景：

1. 单例 Bean（singleton）的初始化时机

- **默认行为**：容器启动时自动初始化 Bean（预创建实例，存入容器，后续直接复用）。

  演示验证：测试类循环 10 次调用 `getBean()`，控制台仅输出 1 次 Bean 构造方法（容器启动时执行）。

- **延迟初始化（`@Lazy` 注解）**：若需 “首次使用时再创建”，在 Bean 类上添加 `@Lazy` 注解，此时：

  - 容器启动时不初始化 Bean；
  - 首次调用 `getBean()` 或注入时，才执行构造方法创建实例；
  - 后续调用 `getBean()` 复用已创建的单例实例（仅初始化 1 次）。

  ```java
  @Lazy // 延迟初始化
  @Scope("singleton") // 单例（默认，可省略）
  @Controller
  public class DptController { 
      // 构造方法（用于观察初始化时机）
      public DptController() {
          System.out.println("DptController 构造方法执行");
      }
  }
  ```

2. 多例 Bean（prototype）的初始化时机

- 固定行为:无延迟初始化，每次调用`getBean()`或注入时，都会新建实例（构造方法每次执行）。

  ⚠️ 注意：多例 Bean 不支持`@Lazy`注解（即使添加，也不会改变 “每次使用新建” 的规则）。

五、开发注意事项（关键）

1. **默认作用域是 singleton**：大部分场景下，Bean 无需配置 `@Scope`，默认单例即可满足需求（如 Service、Dao 层，无状态对象适合单例复用）。
2. **多例 Bean 的使用场景**：仅当 Bean 是 **有状态对象**（如包含成员变量且变量值会随请求变化）时，才需配置为 prototype（如 Web 层的部分临时数据载体）。
3. **单例 Bean 的线程安全**：单例 Bean 是全局共享的，若存在成员变量且被多线程修改，会有线程安全问题；解决方式：避免在单例 Bean 中定义可变成员变量，或使用线程安全的容器（如 `ThreadLocal`）。



#### 2.3 第三方Bean

一、核心场景：为何需要 “第三方 Bean 管理”

1. 自定义 Bean 与第三方 Bean 的区别
   - 自定义 Bean（如 `@Controller`/`@Service`/`@Dao` 标注的类）：可直接在类上添加 `@Component` 及其衍生注解，让 Spring IOC 容器自动扫描管理。
   - 第三方 Bean（如 `dom4j` 依赖中的 `SAXReader` 类）：类来自外部依赖包，文件为**只读状态**，无法手动添加 `@Component` 注解，需特殊方式声明。

二、核心实现：第三方 Bean 的声明方式

1. 基础方式：通过 `@Bean` 注解声明（不推荐直接写在启动类）

- 操作步骤

  1. 在 Spring Boot **启动类中**定义一个**返回第三方类对象**的方法（如返回 `SAXReader`）。
  2. 在方法上添加 `@Bean` 注解：Spring 会自动执行该方法，并将返回值作为 Bean 存入 IOC 容器。

- 代码示例

  ```java
  @SpringBootApplication
  public class MyApplication {
      // 声明第三方 Bean：SAXReader（dom4j 依赖中的类）
      @Bean
      public SAXReader saxReader() {
          return new SAXReader(); // 构造第三方类对象并返回
      }
      
      public static void main(String[] args) {
          SpringApplication.run(MyApplication.class, args);
      }
  }
  ```

2. 规范方式：通过 “配置类 + `@Bean`” 声明（推荐）

- **核心原则**：保持启动类 “纯粹性”（仅负责启动应用），第三方 Bean 统一在**配置类**中管理。

- 操作步骤

  1. 创建配置类：类上添加 `@Configuration` 注解，标识该类为 “Bean 配置类”。
  2. 在配置类中定义返回第三方对象的方法，并用 `@Bean` 注解标注。

- 代码示例

  ```java
  // 1. 配置类（单独放在 config 包下，便于管理）
  @Configuration // 标识为配置类，Spring 会扫描并执行其中的 @Bean 方法
  public class CommonConfig {
      // 2. 声明第三方 Bean：SAXReader
      @Bean
      public SAXReader saxReader() {
          return new SAXReader();
      }
  }
  ```

- **关键说明**：`@Configuration` 注解的作用是告诉 Spring “该类包含 Bean 的配置逻辑”，Spring 启动时会自动解析此类中的 `@Bean` 方法。

三、第三方 Bean 的核心细节

1. Bean 的名称规则

- 自定义名称：通过`@Bean`的`name`或`value`属性指定（两者是别名关系，效果一致）

  ```java
  @Bean(name = "mySaxReader") // 或 @Bean(value = "mySaxReader")
  public SAXReader saxReader() {
      return new SAXReader();
  }
  ```

- **默认名称**：若未指定 `name/value`，Bean 的默认名称为**方法名**（如上述方法名 `saxReader`，则 Bean 名称为 `saxReader`）。

- **验证方式**：通过 `ApplicationContext.getBean("Bean名称")` 可获取 Bean，验证名称是否正确（若名称错误会抛出 `NoSuchBeanDefinitionException`）。

2. 第三方 Bean 的依赖注入（声明时依赖其他 Bean）

- **场景**：若第三方 Bean 初始化时需要依赖 IOC 容器中的其他 Bean（如自定义的 `UserService`），无需额外注解，直接在 `@Bean` 方法的**形参**中声明依赖即可。

- **原理**：Spring 会自动根据 “形参类型” 在 IOC 容器中匹配对应的 Bean，并完成注入。

  ```java
  @Configuration
  public class CommonConfig {
      // 第三方 Bean（SAXReader）依赖自定义 Bean（UserService）
      @Bean
      public SAXReader saxReader(UserService userService) {
          // 可在初始化 SAXReader 时使用 userService 的逻辑
          System.out.println("依赖的 UserService：" + userService);
          return new SAXReader();
      }
  }
  ```

四、第三方 Bean 的使用：依赖注入

声明后的第三方 Bean，与自定义 Bean 的使用方式完全一致，可通过 `@Autowired` 直接注入使用：

```java
@SpringBootTest
public class ThirdPartyBeanTest {
    // 注入第三方 Bean：SAXReader（无需手动 new，直接从 IOC 容器获取）
    @Autowired
    private SAXReader saxReader;

    @Test
    public void testParseXml() throws DocumentException {
        // 直接使用注入的 SAXReader 解析 XML（无需 new SAXReader()）
        Document document = saxReader.read("src/main/resources/test.xml");
        // 后续解析逻辑...
    }
}
```

五、关键原则：Bean 声明方式的选择

|            场景            |       推荐        |                        核心注解                        |
| :------------------------: | :---------------: | :----------------------------------------------------: |
| 自定义类（项目内手写的类） |   类上直接标注    | `@Component`、`@Controller`、`@Service`、`@Repository` |
| 第三方类（外部依赖中的类） | 配置类 + 方法标注 |               `@Configuration` + `@Bean`               |





### 第三节 Spring boot原理

一\ Spring 框架的痛点与 SpringBoot 的诞生

1. Spring 框架的繁琐之处
   - **依赖配置繁琐**：在`pom.xml`中需自行寻找项目所需依赖，还要匹配依赖的配套依赖及对应版本，否则易出现版本冲突。
   - **框架配置复杂**：使用 Spring 开发需在配置文件中进行大量 Bean 声明与配置，导致入门难度大、学习成本高。
2. **SpringBoot 的定位**：Spring 官方在 Spring 4.x 版本后推出 SpringBoot，用于简化 Spring 框架开发（非替代），是目前 Spring 家族子项目中最流行的框架，官方推荐基于其构建 Java 项目以提升开发效率。

二、SpringBoot 简化开发的核心功能

SpringBoot 能简化开发，核心依赖两大底层功能：

- **起步依赖**：解决 Spring 框架中依赖配置繁琐的问题，大幅简化`pom.xml`中依赖配置。
- **自动配置**：简化框架使用时 Bean 的声明与配置，引入起步依赖后，项目常见配置已默认完成，可直接使用（本视频暂未深入讲解，后续展开）。



#### 3.1 起步依赖

1. **传统 Spring 开发的依赖困境**：若用原始 Spring 开发 Web 程序，需手动引入多个依赖（如`spring-webmvc`、Servlet、Jackson、AspectJ 等），且需确保所有依赖版本匹配，否则可能出现版本冲突。

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115140604736.png" alt="image-20251115140604736" style="zoom:50%;" />

2. Spring Boot 起步依赖的优势：开发对应功能只需引入一个对应的起步依赖即可。例如：

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115140625081.png" alt="image-20251115140625081" style="zoom:50%;" />

- 开发 Web 功能：引入`spring-boot-starter-web`。
- 开发 AOP 功能：引入`spring-boot-starter-aop`。

3. 核心原理：Maven 依赖传递

   - **定义**：若 A 依赖 B，B 依赖 C，C 依赖 D，那么引入 A 依赖后，B、C、D 依赖会自动传递引入项目。

   - **起步依赖的实现逻辑**：SpringBoot 的起步依赖（如`spring-boot-starter-web`）已集成该功能开发所需的所有常见依赖，引入起步依赖后，其他所需依赖会通过 Maven 依赖传递自动引入，无需手动配置，且已默认解决版本匹配问题。



#### 3.2 自动配置

一、自动配置的核心定义

自动配置是 SpringBoot 的核心特性之一，指**SpringBoot 项目启动时，除开发者自定义的 Bean（如 Controller、Service、Dao）外，框架会自动创建内置的配置类及对应的 Bean 对象，并将其注入到 Spring IOC 容器中**。

其核心价值在于：开发者无需手动声明框架功能相关的 Bean（如 Web 开发、事务管理、AOP 所需的基础 Bean），可直接从 IOC 容器中获取并使用，极大简化配置操作，提升开发效率。![image-20251115141023492](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251115141023492.png)

二、核心问题：第三方依赖的 Bean 为何默认不生效？

SpringBoot 项目中，启动类上的`@SpringBootApplication`注解自带**组件扫描功能**，但扫描范围仅限**启动类所在包及其子包**（如`com.itheima`）。

第三方依赖的包（如`com.example`）不在此范围内，即使类上标注了`@Component`（普通 Bean）或`@Configuration`（配置类），也无法被扫描到，因此 Bean 不会被加载到 IOC 容器。

三、4 种解决方案：加载第三方依赖的 Bean

方案 1：@ComponentScan 手动指定扫描包

1. 原理:通过`@ComponentScan`注解显式指定要扫描的包，覆盖默认扫描范围，让第三方依赖的包被 Spring 扫描到。

2. 实操:在 SpringBoot 启动类上添加注解：

```java
@SpringBootApplication
// 同时指定第三方包（com.example）和当前项目包（com.it黑马）
@ComponentScan(value = {"com.example", "com.it黑马"})
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

3. 优缺点

- **优点**：实现简单，直接控制扫描范围。
- **缺点**：繁琐（引入多个第三方依赖时，需手动添加所有包）、性能低（大面积扫描可能包含非 Bean 类）。
- **SpringBoot 态度**：不采用。



方案 2：@Import 直接导入类（3 种形式）

`@Import`注解的核心作用：**强制将指定类加载到 IOC 容器**，无需类上标注`@Component`或`@Configuration`，支持 3 种导入形式：

**形式 1：导入普通 Bean 类**

直接导入第三方依赖中的普通 Bean（如`TokenParser`），Spring 会自动将其注册为 IOC 容器中的 Bean。

**实操**：

```java
@SpringBootApplication
// 导入普通Bean类
@Import({TokenParser.class})
public class Application { ... }
```

**效果**：测试时可从 IOC 容器中获取`TokenParser`实例。



**形式 2：导入配置类**

导入第三方依赖中的`@Configuration`配置类（如`HeaderConfig`），配置类本身及其中定义的 Bean（如`HeaderParser`、`HeaderGenerator`）会被一并加载到 IOC 容器。

**实操**：

```java
@SpringBootApplication
// 导入配置类
@Import({HeaderConfig.class})
public class Application { ... }
```

**效果**：测试时可获取`HeaderParser`和`HeaderGenerator`实例。



**形式 3：导入 ImportSelector 接口实现类**

`ImportSelector`是 Spring 提供的接口，核心方法`selectImports()`返回**需要加载的类的全类名字符串数组**，支持动态指定加载的类（如从配置文件读取类名）。

步骤 1：实现 ImportSelector 接口

第三方依赖中提供实现类（如`MyImportSelector`）：

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        // 返回需要加载的配置类全类名（此处为HeaderConfig）
        return new String[]{"com.example.HeaderConfig"};
    }
}
```

步骤 2：@Import 导入实现类

```java
@SpringBootApplication
// 导入ImportSelector实现类
@Import({MyImportSelector.class})
public class Application { ... }
```

**效果**：Spring 会执行`selectImports()`方法，加载返回的`HeaderConfig`，进而加载其内部的 Bean。



方案 3：@EnableXXX 注解（Spring Boot 官方方案）

- 原理:第三方依赖自行封装`@EnableXXX`注解（如`@EnableHeaderConfig`），该注解内部集成`@Import`，预先指定需要加载的类（如`ImportSelector`实现类）。开发者只需在启动类上添加`@EnableXXX`，即可一键开启第三方依赖的自动配置，无需关注具体加载哪些类。

- 实操

1. **第三方依赖定义 @Enable XXX 注解**：

```java
// 第三方依赖提供的注解，内部集成@Import
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Import(MyImportSelector.class) // 预先指定ImportSelector实现类
public @interface EnableHeaderConfig {
}
```

1. **开发者使用 @EnableXXX**：

```java
@SpringBootApplication
// 一键开启第三方依赖的自动配置
@EnableHeaderConfig
public class Application { ... }
```

- 优缺点

  - **优点**：优雅、便捷（开发者无需记忆具体类 / 包，第三方自行维护）。

  - **Spring Boot 态度**：核心方案（如`@EnableWebMvc`、`@EnableTransactionManagement`均采用此模式）。



3.3 自动配置原理

一、自动配置分析入口：`@SpringBootApplication`注解

SpringBoot 应用的启动类（引导类）上必须标注`@SpringBootApplication`，它是自动配置的 “总入口”，其底层**封装了 3 个关键注解**（忽略 4 个元注解，即修饰注解的注解），各注解功能如下：

|          注解名称          |                           核心作用                           |                     底层依赖 / 延伸说明                      |
| :------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| `@SpringBootConfiguration` | 声明当前启动类是**配置类**，允许在启动类中通过`@Bean`声明 Bean | 内部仅封装`@Configuration`（Spring 原生配置类注解）和`@Indexed`（加速启动，无需关注） |
|      `@ComponentScan`      | 实现**组件扫描**，默认扫描 “启动类所在包及其子包” 下的`@Component`等注解类 | 无需额外配置，SpringBoot 自动完成基础包扫描，避免手动指定扫描路径 |
| `@EnableAutoConfiguration` | **自动配置的核心注解**，触发 SpringBoot 自动加载第三方 / 内置配置类 | 内部通过`@Import`导入`ImportSelector`实现类，是加载配置类的关键逻辑 |

二、自动配置核心逻辑：`@EnableAutoConfiguration`的底层实现

`@EnableAutoConfiguration`是自动配置的 “引擎”，其核心逻辑是**通过`@Import`导入配置类**，具体流程分为 3 步：

1. 导入`ImportSelector`实现类

`@EnableAutoConfiguration`的源码中包含`@Import(AutoConfigurationImportSelector.class)`，其中：

- `AutoConfigurationImportSelector`是`ImportSelector`接口的实现类，该接口的核心方法是`selectImports()`；
- `selectImports()`的返回值是**String 类型数组**，数组中存储的是 “需要加载到 IOC 容器的配置类全类名”。

2. 加载自动配置类：读取 2 个关键配置文件

`AutoConfigurationImportSelector`的`selectImports()`方法会调用工具类，最终读取**2 个固定路径的配置文件**，获取配置类全类名：

|                         配置文件路径                         |                             作用                             |                         版本差异说明                         |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|                 `META-INF/spring.factories`                  | 早期 SpringBoot（2.7.x 之前）的自动配置文件，存储配置类全类名 |     SpringBoot 3.x 版本后**彻底移除**，仅 2.7.x 版本兼容     |
| `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` | 2.7.x 及之后版本的**主要配置文件**，存储所有自动配置类全类名 | 内容更清晰，默认包含 140 + 个自动配置类（如`GsonAutoConfiguration`、`DataSourceAutoConfiguration`） |

> 补充：这些配置文件通常存在于 SpringBoot 的自动配置依赖（`spring-boot-autoconfigure`）或第三方起步依赖（如 MyBatis 的`mybatis-spring-boot-autoconfigure`）中，无需开发者手动编写。

3. 配置类加载到 IOC 容器

SpringBoot 启动时，会将上述 2 个文件中配置的 “自动配置类全类名” 封装到`selectImports()`的返回数组中，再通过`@Import`注解将这些**自动配置类加载到 IOC 容器**，完成初始化。

案例：`GsonAutoConfiguration`的自动配置

- 开发者引入`spring-boot-starter-web`依赖后，无需手动声明`Gson`Bean；
- 原因：`AutoConfiguration.imports`文件中包含`GsonAutoConfiguration`（Gson 的自动配置类）；
- 该配置类上标注`@Configuration`，内部通过`@Bean`声明`Gson`对象，因此 IOC 容器会自动创建`Gson`Bean，开发者可直接`@Autowired`注入使用。

三、自动配置的 “灵活性”：条件装配（`@Conditional`系列注解）

`AutoConfiguration.imports`中配置了 140 + 个自动配置类，但**并非所有类都会被加载到 IOC 容器**，核心原因是 **“条件装配”**—— 自动配置类中的`@Bean`方法会标注`@Conditional`系列注解，只有满足条件时，Bean 才会被注册。

四、自动配置整体流程总结（宏观梳理）

1. 启动 SpringBoot 应用，触发启动类上的`@SpringBootApplication`；
2. `@SpringBootApplication`中的`@EnableAutoConfiguration`通过`@Import`导入`AutoConfigurationImportSelector`；
3. `AutoConfigurationImportSelector`的`selectImports()`方法读取`META-INF`下的 2 个配置文件，获取自动配置类全类名；
4. 这些自动配置类被加载到 IOC 容器，类中的`@Bean`方法通过`@Conditional`系列注解判断是否注册 Bean；
5. 开发者无需手动配置，即可直接使用 IOC 容器中的 Bean（如 Gson、DataSource 等），实现 “自动配置”。



#### 3.4 Conditional条件装配

一、@Conditional 注解核心作用

- **本质定义**：`@Conditional`是 Spring 中的**条件装配注解**，`Condition`意为 “条件”，核心作用是 “按指定条件判断，条件成立则将 Bean 注册到 IOC 容器，不成立则不注册”。
- 作用范围
  1. **方法级**：仅对当前方法声明的 Bean 生效（控制单个 Bean 的注册）；
  2. **类级**：对整个配置类（`@Configuration`标注的类）生效（控制该类中所有 Bean 的注册）。
- **注解性质**：`@Conditional`是**元注解**（即 “注解的注解”），Spring 基于它衍生出多个 “开箱即用” 的子注解，无需自定义条件逻辑即可满足常见场景。

二、3 个核心衍生注解

|          注解名称           |                           核心作用                           |                     关键属性 / 配置方式                      | 应用场景举例                                                 |
| :-------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | ------------------------------------------------------------ |
|    `@ConditionalOnClass`    | 判断当前环境中是否存在**指定类的字节码文件**（即是否引入对应依赖），存在则注册 Bean | `value`：直接指定类对象（如`value = Jwt.class`）<br>`name`：指定类的全类名（字符串形式，如`name = "io.jsonwebtoken.Jwt"`） | SpringBoot 自动配置：引入`spring-boot-starter-web`依赖后，才注册`DispatcherServlet`等 Web 相关 Bean |
| `@ConditionalOnMissingBean` | 判断当前 IOC 容器中是否**不存在指定 Bean**，不存在则注册当前 Bean（避免 Bean 重复定义） | `value`：按 Bean 类型判断（如`value = HeaderParser.class`）<br>`name`：按 Bean 名称判断（如`name = "headerParser"`） | 定义 “默认 Bean”：若用户未自定义`RedisTemplate`，则使用 SpringBoot 默认提供的`RedisTemplate` |
|  `@ConditionalOnProperty`   | 判断配置文件（如`application.yml`）中是否存在**指定属性名及对应属性值**，存在则注册 Bean | `name`：配置项的属性名（如`name = "app.name"`）<br>`havingValue`：配置项的属性值（如`havingValue = "itcast"`） | 开关式配置：配置`spring.datasource.url`等属性后，才注册`DataSource`Bean；配置`mybatis.mapper-locations`后才加载 MyBatis 映射文件 |

三、实操验证逻辑

1. `@ConditionalOnClass`验证

- **步骤 1**：在`HeaderParser`的 Bean 方法上添加`@ConditionalOnClass(name = "io.jsonwebtoken.Jwt")`（依赖 JWT 工具类）；
- **步骤 2**：初始状态：`pom.xml`引入 JWT 依赖→单元测试能获取`HeaderParser`Bean（条件成立）；
- **步骤 3**：修改状态：注释 JWT 依赖→单元测试报错`NoSuchBeanDefinitionException`（条件不成立，Bean 未注册）。

2. `@ConditionalOnMissingBean`验证

- **步骤 1**：在`HeaderParser`的 Bean 方法上添加`@ConditionalOnMissingBean(HeaderParser.class)`；
- **步骤 2**：初始状态：容器中无自定义`HeaderParser`→单元测试能获取 Bean（条件成立）；
- **步骤 3**：修改状态：手动新增一个`HeaderParser`的自定义 Bean→单元测试无法获取默认 Bean（条件不成立，默认 Bean 不注册）。

3. `@ConditionalOnProperty`验证

- **步骤 1**：在`HeaderParser`的 Bean 方法上添加`@ConditionalOnProperty(name = "app.name", havingValue = "itcast")`；
- **步骤 2**：初始状态：`application.yml`配置`app.name: itcast`→单元测试能获取 Bean（条件成立）；
- **步骤 3**：修改状态：将`app.name`改为`itcast2`→单元测试无法获取 Bean（条件不成立，Bean 未注册）。

四、SpringBoot 自动配置的底层关联

揭示`@Conditional`注解的核心地位：

1. **自动配置类的加载**：SpringBoot 通过`@EnableAutoConfiguration`注解，加载`META-INF/spring.factories`或`META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`文件中定义的 “自动配置类”（如`GsonAutoConfiguration`、`DataSourceAutoConfiguration`）；
2. **条件过滤的关键**：所有自动配置类中声明 Bean 时，均大量使用`@Conditional`衍生注解（如`@ConditionalOnClass`、`@ConditionalOnMissingBean`）；
3. **最终效果**：SpringBoot 并非加载所有自动配置类的 Bean，而是通过`@Conditional`判断 “是否满足依赖 / 配置条件”，仅注册符合条件的 Bean—— 这也是 SpringBoot“自动配置、按需加载” 的核心实现。



## 第十章 maven高级

### 第一节 分模块设计与开发

1. 什么是分模块设计

分模块设计是指在 Java 项目设计阶段，将一个完整的项目按照功能拆分成若干个独立的子模块，每个子模块专注于特定功能的开发与维护。

2. 为什么需要分模块设计（不分模块的问题）

若项目不进行分模块设计，会面临两大核心问题：

- **项目管理与维护困难**：所有业务代码集中在一个项目中，随着业务扩张（如大型电商项目），几十甚至几百号开发人员共同操作同一项目，代码冲突、版本管理、功能迭代效率会大幅降低。
- **组件复用性差**：项目中自定义的通用工具类、通用组件（如实体类、工具方法）无法单独提取供其他项目组使用。若直接依赖整个项目，会引入冗余业务代码，既影响性能（加载不必要的类），又存在业务代码泄露的安全风险。

3. 分模块设计的优势

- **便于项目管理与维护**：按功能拆分后，可按模块分配开发团队（如一组负责订单模块，一组负责购物车模块），降低协作成本，提升迭代效率。
- **提升组件复用性**：通用模块（如实体类、工具类）可独立封装，其他项目或模块通过依赖坐标即可使用，避免重复开发。
- **降低耦合度**：模块间通过明确的依赖关系交互，而非直接依赖整体项目，减少代码耦合，提升项目稳定性。

4. 分模块设计实战（以 “TliasWebManagement” 项目为例）

（1）模块拆分思路

针对原有项目中的通用资源，拆分为 3 个核心模块：

| 模块名称           | 功能定位   | 包含内容                                                     |
| ------------------ | ---------- | ------------------------------------------------------------ |
| TliasPojo          | 实体类模块 | 原项目中`pojo`包下的所有实体类（如分页结果类`PageResult`、统一响应类`Result`等） |
| TliasUtils         | 工具类模块 | 原项目中`utils`包下的所有工具类（如 JWT 工具类、阿里云 OSS 操作工具类等） |
| TliasWebManagement | 业务模块   | 原项目中的核心业务代码（部门管理、员工管理、登录认证等），依赖上述两个通用模块 |

（2）模块创建与配置步骤

1. **创建通用模块（以 TliasPojo 为例）**
   - 新建 Maven 模块（无需选择 Spring Initializer，因仅需存放实体类，无需 Spring 框架）。
   - 配置模块坐标（如`groupId: com.itheima`，`artifactId: tlias-pojo`）。
   - 在`src/main/java`下创建与原项目一致的包结构（如`com.itheima.pojo`），复制原项目中的实体类。
   - 引入依赖：实体类中若使用 Lombok 注解（如`@Data`、`@NoArgsConstructor`），需在该模块的`pom.xml`中引入 Lombok 依赖。
2. **创建工具类模块（TliasUtils）**
   - 步骤与 TliasPojo 一致，复制原项目`utils`包下的工具类。
   - 引入依赖：根据工具类需求引入对应依赖（如 JWT 依赖、阿里云 OSS 依赖、Spring Web 依赖（若工具类依赖 Web 相关 API）、Lombok 依赖（若工具类使用 Lombok 注解））。
3. **配置业务模块依赖**
   - 在`TliasWebManagement`的`pom.xml`中，引入 TliasPojo 和 TliasUtils 的依赖坐标，替代原项目中的`pojo`和`utils`包（引入后删除原项目中的对应包，避免冲突）。
4. **验证拆分结果**
   - 启动`TliasWebManagement`项目，访问核心功能（如部门列表查询、员工分页查询），若功能正常运行，说明模块拆分与依赖配置正确。

5. 关键注意事项

- **设计优先于编码**：分模块设计必须在项目开发初期完成，明确模块划分与依赖关系，而非待项目开发完成后再拆分（本次实战为演示拆分过程，实际开发需遵循 “先设计后编码” 原则）。
- **依赖管理清晰**：模块间仅保留必要依赖（如业务模块仅依赖通用模块，通用模块不依赖业务模块），避免循环依赖。
- **版本一致性**：确保各模块的依赖版本统一



### 第二节 继承

#### 2.1 继承关系

 一、Maven 继承的核心作用

解决**多模块项目中依赖重复配置**的痛点：

- 场景：拆分后的多模块（如视频中的 pg、youtube、web management 模块）需重复配置相同依赖（如 lombok），大型项目中重复配置会导致维护繁琐。
- 解决方案：通过 “父工程” 统一管理公共依赖，子工程继承父工程后自动获取依赖，无需重复配置，实现 “一处配置，多处复用”。

![image-20251116164840792](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251116164840792.png)

二、Maven 继承与 Java 继承的类比

Maven 继承设计借鉴 Java 类继承逻辑，核心规则一致：

|     特性     |          Java 类继承           |                 Maven 工程继承                 |
| :----------: | :----------------------------: | :--------------------------------------------: |
|   继承关系   |  类与类之间（如 A extends B）  |        工程与工程之间（子工程→父工程）         |
|   继承限制   | 单继承（一个类只能有一个父类） |    单继承（一个子工程只能有一个直接父工程）    |
| 多层继承支持 |         支持（A→B→C）          | 支持（子工程→自定义父工程→Spring Boot 父工程） |
|   核心作用   |       复用父类属性、方法       |          复用父工程的依赖、插件等配置          |

三、Maven 工程的 3 种打包方式

父工程的打包方式必须设置为`pom`，这是 Maven 继承的基础

| 打包方式 |                      适用场景                      |                           核心特点                           |                示例                 |
| :------: | :------------------------------------------------: | :----------------------------------------------------------: | :---------------------------------: |
|  `jar`   | 普通 Java 模块（工具类、实体类）、Spring Boot 项目 | 可通过`java -jar`直接运行（Spring Boot 内嵌 Tomcat），可被其他模块依赖 | pg 模块（实体类）、Spring Boot 项目 |
|  `war`   |       传统 Web 项目（如 SSM、Servlet 项目）        |   需部署到外部 Tomcat 才能运行，不支持`java -jar`直接启动    |          早期 SSH/Web 项目          |
|  `pom`   |                  父工程、聚合工程                  | 不包含 Java 代码，仅用于管理依赖 / 模块，是继承 / 聚合的 “配置载体” |   taligun-parent（自定义父工程）    |

四、子工程配置继承关系

在子工程（pg、youtube、web management）的`pom.xml`中，通过`<parent>`标签指定自定义父工程：

1. **配置父工程坐标**：填写自定义父工程的`groupId`、`artifactId`、`version`。

2. 配置`relativePath`：指定父工程`pom.xml`的相对路径（关键，避免 Maven 找不到父工程），示例：

   ```xml
   <parent>
       <groupId>com.itheima</groupId> <!-- 自定义父工程的groupId -->
       <artifactId>taligun-parent</artifactId> <!-- 自定义父工程的artifactId -->
       <version>1.0-SNAPSHOT</version> <!-- 自定义父工程的版本 -->
       <!-- 相对路径：子工程目录→上一级目录→自定义父工程目录→pom.xml -->
       <relativePath>../taligun-parent/pom.xml</relativePath>
   </parent>
   ```

3. **简化子工程坐标**：子工程会自动继承父工程的`groupId`，因此可删除子工程自身的`<groupId>`标签（否则 Maven 会提示 “冗余警告”）。



#### 2.2 版本锁定

一、版本锁定的核心背景与问题

在 Maven 分模块项目中，若多个子模块依赖同一组件（如 jwt、阿里云 OSS 等），会面临以下痛点：

1. **版本不一致风险**：子模块需单独配置依赖版本，手动维护易导致不同子模块版本差异，引发兼容性问题；
2. **升级维护繁琐**：若需升级依赖版本（如将 jwt 从 0.9.1 升至 0.9.2），需逐个检查所有子模块的`pom.xml`文件修改，模块越多越容易遗漏；
3. **管理效率低下**：版本号零散分布在各子模块，无法集中查看和管控。

为解决上述问题，Maven 提供**版本锁定**功能，实现依赖版本的统一管理。

二、版本锁定的核心实现：`dependencyManagement`标签

1. 标签作用

在父工程的`pom.xml`中通过`<dependencyManagement>`标签，**统一声明依赖的版本规则**，但不直接引入依赖（仅管 “版本”，不管 “依赖引入”）。

2. 配置步骤

步骤 1：父工程中配置`dependencyManagement`

在父工程的`pom.xml`里，通过`<dependencyManagement>`包裹需统一管理的依赖，明确指定版本号。示例：

```xml
<!-- 父工程pom.xml -->
<dependencyManagement>
    <dependencies>
        <!-- 统一管理gwt依赖版本 -->
        <dependency>
            <groupId>com.example</groupId>
            <artifactId>gwt</artifactId>
            <version>0.9.1</version> <!-- 统一版本 -->
        </dependency>
        <!-- 统一管理阿里云OSS依赖版本 -->
        <dependency>
            <groupId>com.aliyun.oss</groupId>
            <artifactId>aliyun-sdk-oss</artifactId>
            <version>3.15.1</version> <!-- 统一版本 -->
        </dependency>
    </dependencies>
</dependencyManagement>
```

步骤 2：子工程中引入依赖（无需指定版本）

子工程需使用依赖时，仅需配置`<dependency>`的`groupId`和`artifactId`，**无需写`<version>`标签**，版本会自动继承父工程`dependencyManagement`中声明的版本。示例：

```xml
<!-- 子工程pom.xml -->
<dependencies>
    <!-- 引入gwt依赖，版本继承父工程的0.9.1 -->
    <dependency>
        <groupId>com.example</groupId>
        <artifactId>gwt</artifactId>
        <!-- 无需写version标签 -->
    </dependency>
</dependencies>
```

3. 核心特点

- **版本统一管控**：修改依赖版本时，仅需修改父工程`dependencyManagement`中的版本号，所有子工程自动同步；
- **不强制引入依赖**：`dependencyManagement`仅声明版本，子工程需显式引入才会加载依赖（避免不必要的依赖冗余）；
- **优先级明确**：子工程若手动指定`version`，会覆盖父工程的统一版本（特殊场景需求）。

三、版本管理优化：自定义属性（集中维护版本号）

当父工程`dependencyManagement`中管理的依赖较多时，版本号仍会零散分布，可通过**Maven 自定义属性**进一步优化，实现版本号的 “集中维护”。

1. 配置步骤

步骤 1：父工程中定义自定义属性

在父工程`pom.xml`的`<properties>`标签中，为每个依赖版本定义 “属性名 - 版本值” 映射，属性名需有业务含义（如`gwt.version`）。示例：

```xml
<!-- 父工程pom.xml -->
<properties>
    <!-- 自定义属性：gwt版本 -->
    <jwt.version>0.9.1</gwt.version>
    <!-- 自定义属性：阿里云OSS版本 -->
    <aliyun.oss.version>3.15.1</aliyun.oss.version>
    <!-- 自定义属性：lombok版本 -->
    <lombok.version>1.18.24</lombok.version>
</properties>
```

步骤 2：引用自定义属性

在`dependencyManagement`的依赖中，通过`${属性名}`引用上述定义的版本，替代硬编码的版本号。示例：

```xml
<!-- 父工程pom.xml -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.example</groupId>
            <artifactId>gwt</artifactId>
            <version>${gwt.version}</version> <!-- 引用自定义属性 -->
        </dependency>
        <dependency>
            <groupId>com.aliyun.oss</groupId>
            <artifactId>aliyun-sdk-oss</artifactId>
            <version>${aliyun.oss.version}</version> <!-- 引用自定义属性 -->
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version> <!-- 引用自定义属性 -->
        </dependency>
    </dependencies>
</dependencyManagement>
```

2. 核心优势

- **版本集中修改**：需升级版本时，仅需修改`<properties>`中的属性值，所有引用该属性的依赖自动同步；
- **可读性提升**：通过属性名可快速识别依赖类型，避免版本号硬编码导致的混淆；
- **维护效率高**：版本号集中在`<properties>`中，无需在`dependencyManagement`中逐个查找修改。

四、关键对比：`dependencyManagement` vs `dependencies`

两者均用于依赖配置，但核心作用完全不同，是 Maven 依赖管理的高频考点：

| 对比维度       | `<dependencyManagement>`       | `<dependencies>`                                             |
| -------------- | ------------------------------ | ------------------------------------------------------------ |
| **核心作用**   | 统一管理依赖版本，不引入依赖   | 直接引入依赖，若未指定版本则继承父工程版本                   |
| **依赖加载**   | 不自动加载依赖到项目中         | 自动加载依赖到项目 classpath                                 |
| **版本优先级** | 声明版本规则，子工程可覆盖     | 子工程若指定版本，覆盖父工程规则                             |
| **使用场景**   | 父工程统一管控子模块的依赖版本 | 子工程引入具体依赖，或父工程引入 “所有子模块必用” 的依赖（如 lombok） |



### 第三节 聚合

一、问题背景：分模块开发的 “手动构建痛点”

当项目拆分为多个模块（如案例中的`web-management`（Web 模块）、`pojo`（实体类模块）、`service`（服务模块））且存在依赖关系时，手动构建会面临以下问题：

1. **依赖缺失导致构建失败**：若直接打包`web-management`（依赖`pojo`和`service`），Maven 会到本地仓库寻找这两个模块的 Jar 包，若未提前安装则构建失败；
2. **需按依赖顺序手动操作**：必须先安装`parent`（父工程）→ 再安装`pojo`/`service`（被依赖模块）→ 最后打包`web-management`，步骤繁琐；
3. **模块越多越复杂**：大型项目模块多、依赖关系复杂时，手动维护构建顺序易出错、效率极低。

二、聚合核心概念：一键解决多模块构建

1. 聚合定义：将项目的多个模块 “整合为一个整体”，通过**聚合工程**统一触发构建操作（编译、打包、安装等），实现 “一键构建所有模块”，无需手动维护依赖顺序。

2. 聚合工程特性

- **无业务功能的 “空工程”**：仅需一个`pom.xml`文件，无需`src`目录（无 Java 代码、资源文件）；
- **通常与父工程复用**：实际开发中，聚合工程会直接使用 “继承” 中的`parent`工程（既做父工程统一依赖版本，又做聚合工程管理模块构建），避免重复创建工程。

三、聚合实现步骤（IDEA 环境）

核心仅需**1 步配置**，基于已有的`parent`工程（父工程），在pom.xml 中配置模块

在`parent`的`pom.xml`中，通过`<modules>`标签指定需聚合的模块，语法如下：

```xml
<!-- 聚合配置：指定需聚合的模块 -->
<modules>
    <!-- 模块路径：../模块名（../表示与parent工程同级目录） -->
    <module>../tina-pojo</module>    <!-- 被依赖的实体类模块 -->
    <module>../tina-service</module>  <!-- 被依赖的服务模块 -->
    <module>../tina-web-management</module>  <!-- 最终Web模块 -->
</modules>
```

- **路径规则**：若模块与聚合工程（parent）在同一级目录，路径用`../模块名`；若模块在聚合工程内部，直接写`模块名`；
- **配置顺序无关**：`<module>`标签的书写顺序不影响构建顺序，Maven 会自动根据模块间的依赖关系调整构建顺序（先构建`pojo`→再`service`→最后`web-management`）。

四、聚合核心特性：自动处理依赖顺序

Maven 聚合的关键优势是 **“智能排序”**：无论`<modules>`标签中模块的书写顺序如何，Maven 都会在构建前分析模块间的依赖关系，按 “被依赖模块优先构建” 的原则执行。

五、继承与聚合对比

|     维度     |                   继承（Inheritance）                   |                    聚合（Aggregation）                     |
| :----------: | :-----------------------------------------------------: | :--------------------------------------------------------: |
| **核心作用** |        统一管理子模块的依赖版本（简化依赖配置）         |           统一构建多模块（一键编译、打包、安装）           |
| **配置位置** | 在**子模块**的 pom.xml 中，通过`<parent>`标签指定父工程 | 在**聚合工程**的 pom.xml 中，通过`<modules>`标签指定子模块 |
| **感知关系** |           父工程无法感知哪些子模块继承了自己            |               聚合工程明确知道聚合了哪些模块               |
| **工程类型** |             父工程是 “空工程”（仅 pom.xml）             |             聚合工程是 “空工程”（仅 pom.xml）              |
| **打包方式** |              父工程打包类型为`pom`（必须）              |              聚合工程打包类型为`pom`（必须）               |
| **实际复用** |       通常与聚合工程复用（用同一个 parent 工程）        |          通常与父工程复用（用同一个 parent 工程）          |



### 第四节 私服

一、私服的基础概念与核心作用

1. 什么是私服？

- 本质：**架设在公司局域网内部的特殊远程仓库**，属于企业内部专属的 Maven 仓库服务。
- 定位：介于 “本地仓库” 和 “中央仓库” 之间，既可以存储企业内部开发的资源，也可以代理外部中央仓库。

2. 为什么需要私服？

- 解决**团队内部资源共享**：企业内 A 项目组开发的通用模块（如工具类模块`taleg-utils`），无法通过 “本地仓库” 直接共享给 B 项目组，私服提供了统一的内部资源存储地址。
- 统一资源管理：企业内部所有开发人员连接同一台私服，确保依赖版本一致，避免 “版本混乱” 问题。

二、Maven 依赖的查找顺序

当项目需要某个依赖时，Maven 会按以下优先级依次查找，直到找到依赖或报错：

1. **本地仓库**：开发者电脑本地的`.m2/repository`目录，优先查找已下载的依赖。
2. **私服**：本地仓库未找到时，访问企业内部私服查找（私服会先检查自身缓存）。
3. **中央仓库**：私服也未找到时，由私服自动代理连接中央仓库下载，下载后会缓存到私服，供后续团队成员复用。

![image-20251116171426381](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251116171426381.png)



三、私服的仓库分类

私服内部主要分为 3 类仓库，不同仓库存储的资源类型不同，核心区别与 “项目版本” 强关联：

| 仓库类型          | 存储内容                                           | 对应项目版本            | 版本特征                                   |
| ----------------- | -------------------------------------------------- | ----------------------- | ------------------------------------------ |
| **Release 仓库**  | 企业内部稳定、可正式发布的资源（如上线模块）       | 发行版本（Release 版）  | 版本号无`SNAPSHOT`后缀，如`1.0.0`          |
| **Snapshot 仓库** | 企业内部开发中、不稳定的资源（如测试模块）         | 快照版本（Snapshot 版） | 版本号含`SNAPSHOT`后缀，如`1.0.0-SNAPSHOT` |
| **Central 仓库**  | 代理中央仓库下载的第三方依赖（如 MyBatis、Spring） | 第三方开源依赖          | 无特定版本规则，跟随开源项目版本           |

> 关键规则：项目版本为`SNAPSHOT`时，资源自动上传到`Snapshot仓库`；版本无`SNAPSHOT`时，自动上传到`Release仓库`。

四、私服的核心操作：资源上传与下载

1. 资源上传：将本地模块上传到私服

|        配置步骤        |           配置文件           |                           配置内容                           | 核心作用                                                   |
| :--------------------: | :--------------------------: | :----------------------------------------------------------: | ---------------------------------------------------------- |
|    配置私服访问权限    | Maven 全局配置`settings.xml` | 在`<servers>`标签中配置`Release仓库`和`Snapshot仓库`的用户名、密码（由企业运维提供）。 | 验证开发者身份，确保只有授权人员能上传资源。               |
|    配置上传仓库地址    |        项目`pom.xml`         | 在`<distributionManagement>`标签中，分别指定`Release仓库`和`Snapshot仓库`的 URL。 | 告诉 Maven：当前模块要上传到私服的哪个仓库。               |
| 配置私服镜像（下载用） | Maven 全局配置`settings.xml` | 在`<mirrors>`标签中配置 “私服仓库组” 的 URL（仓库组是多个仓库的集合，如包含 Release、Snapshot、Central 仓库）。 | 告诉 Maven：下载依赖时优先访问私服，而非直接访问中央仓库。 |

```xml
1.设置私服的访问用户名/密码（settings.xml中的servers中配置）
<server>
	<id> maven-releases</id>
    <username>admin</username>
	<password>admin</password>
</server>

<server>
	<id>maven-snapshots</id>
    <username>admin</username>
	<password>admin</password>
</server>

2. maven工程的pom文件中配置上传（发布）地址
<distributionManagement>
	<repository>
		<id> maven-releases</id>
		<url> http://192.168.150.101:8081/repository/maven-releases/</url>
	</repository>
	<snapshotRepository>
		<id> maven-snapshots</id>
		<url> http://192.168.150.101:8081/repository/maven-snapshots/</url>
	</snapshotRepository>
</distributionManagement>

3.设置私服依赖下载的仓库组地址 (settings. xml中的mirrors、profiles中配置)
<mirror>
	<id> maven-public</ id>
	<mirror0f>*</mirror0f>
	<url> http://192.168.150.101:8081/repository/ maven-public/</url>
</mirror>

</profile>
	<id>allow-snapshots</id>
	<activation>
		<activeByDefault>true</activeByDefault>
	</activation>
	<repositories>
		<repository>
			<id>maven-public</id>
			<url>http://192.168.150.101:8881/repository/maven-public/</url>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
	</repositories>
</profile>
```



上传指令：执行 Maven 生命周期

在 IDEA 中找到项目的 Maven 生命周期，双击执行 **`deploy`** 指令：

- 作用：先将模块打包并安装到本地仓库，再自动上传到私服对应的仓库（根据版本自动匹配 Release/Snapshot 仓库）。
- 验证：上传成功后，可通过私服网页（如访问`http://192.168.150.101:8081`）查看对应仓库下的资源。

2. 资源下载：从私服获取依赖（如 B 项目组使用`taleg-utils`）



五、注意事项

1. **私服部署**：企业内只需搭建 1 台私服，所有开发人员共享，无需个人搭建，开发者仅需 “使用私服”（配置地址和权限）。

2. **配置来源**：私服的 URL、用户名、密码由企业运维或项目负责人提供，开发者无需手动编写，直接复制配置文档即可（避免配置错误）。

   