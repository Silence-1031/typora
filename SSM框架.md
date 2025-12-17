## SSM框架

### 第一章 Spring

- Spring技术是JavaEE开发必备技能，企业开发技术选型命中率>90%

- 专业角度

  - 简化开发，降低企业级开发的复杂性

     **IOC、AOP、事务处理**

  - 框架整合，高效整合其他技术，提高企业级应用开发与运行效率

​		**MyBatis，MyBatis-plus，Struts，Struts2，Hibernate···**

#### 第一节 初识Spring

##### 1.Spring家族

- 官网 :`spring.io`
  Spring发展到今天已经形成了一种开发的生态圈，Spring提供了若干个项目，每个项目用于完成特定的功能

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-16 110940.png)

- 主要学习spring framework、spring boot、spring cloud

##### 2. 发展历史

![image-20251016111228941](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251016111228941.png)

​	Spring 框架诞生于 2002 年，由 Rod Johnson 创建，旨在解决当时 Java 企业级开发中复杂性和低效性的问题。Rod Johnson 在其著作《Expert One-on-One J2EE》中首次提出了基于依赖注入（DI）和控制反转（IoC）的轻量级解决方案，这成为 Spring 的核心思想。

​	2003 年，Spring 框架正式发布，最初被称为 Interface21。2004 年，Spring 1.0 版本推出，专注于依赖注入和面向切面编程（AOP）。此后，Spring 逐步发展，成为 Java 领域最流行的开发框架之一。



#### 第二节 Spring Framework系统架构

- Spring Framework是Spring生态圈中最基础的项目，是其他项目的根基

![image-20251016111910420](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251016111910420.png)

##### 1. Core container 核心容器

- Spring 框架的 “最核心模块”，所有其他模块均依赖它运行；
- 本质是 “管理 Java 对象的容器”，是 Spring “管对象” 技术的核心载体。

##### 2. AOP 面向切面编程和 Aspects AOP思想实现

- AOP（面向切面编程）：一种编程思想，能 “不修改原始代码” 为程序增强功能；
- Aspect：对 AOP 思想的成熟实现，Spring 推荐使用（开发时需同时导入 Spring AOP 和 Aspect 坐标）。

##### 3. Data Access 数据访问/Data Integration 数据集成

- 负责 “数据层相关技术”，支持 Spring 与第三方数据框架（如 MyBatis）整合；
- 包含**事务（Transaction）** 模块：Spring 提供高效的事务控制方案，是该模块的重点内容。

##### 4. Web 开发

- 支持 Spring 进行 Web 开发

##### 5. Test 单元测试与集成测试

- 提供 “单元测试与集成测试” 的辅助工具



#### 第三节 核心概念

##### 一. 问题背景

- **硬编码依赖**：业务层代码中通过`new`主动创建数据层对象（如`new DaoImpl()`），导致两层代码强耦合；
- **修改成本高**：若需替换数据层实现（如换用新的`NewDaoImpl`），必须修改业务层源代码，进而引发 “重新编译、测试、部署” 等一系列繁琐操作；
- **核心诉求**：程序开发需追求 “低耦合”，但直接删除`new`操作会导致 “空指针异常（NoPointerException）”（因缺少实际对象），由此引出 Spring 的解决方案。

##### 二. IoC （Inversion of Control）控制反转

- 概念：使用对象时，由主动new来产生对象转换为由外部提供对象，此过程中对象创建控制权由程序转移到外部

- 与 Spring 的关系

  Spring 框架**实现了 IOC 思想**：它提供了一个 “外部载体” 来承担对象创建的职责，这个载体就是 “IOC 容器”。即由IoC容器来提供对象

##### 三. IOC容器

1. 定义

​	IOC 容器（又称 “Spring 容器”）是 Spring 框架内部提供的**对象管理容器**，是 IOC 思想中 “外部” 的具体实现。

2. 核心职责

- 负责**对象的创建、初始化及管理**

作用逻辑

- 开发者无需在代码中`new`对象，只需从 IOC 容器中 “取对象”；
- 不仅数据层对象（Dao），业务层对象（Service）等都可交由 IOC 容器管理。

##### 四、Bean——IOC 容器管理的对象

1. 定义

​	Bean 是指**由 IOC 容器创建并管理的对象**，是 Spring 框架中 “对象” 的特定称谓。

2. 本质

​	Bean 就是程序中的普通对象，但区别在于：

- 普通对象：由开发者`new`创建，归开发者管理；
- Bean：由 IOC 容器创建，归容器管理。

##### 五、DI（依赖注入）

1. 问题引入

​	仅靠 IOC 容器创建 Bean 仍存在缺陷：若 A Bean（如 Service）运行时依赖 B Bean（如 Dao），IOC 容器仅创建 A Bean 会导致 A Bean 因缺少 B Bean 而无法运行（仍会报空指针异常）。

2. 定义

​	DI（Dependency Injection，依赖注入）是指：**IOC 容器在创建依赖关系的 Bean 时，自动将依赖的 Bean 注入到目标 Bean 中**。

- 例：IOC 容器创建 Service Bean 时，发现它依赖 Dao Bean，会自动将容器中的 Dao Bean “注入” 到 Service Bean 中，确保 Service Bean 拥有可使用的 Dao Bean。

3. 核心目的

​	解决 Bean 之间的依赖问题：通过 “自动注入依赖”，确保 Bean 运行时所需的所有依赖都已就绪，避免空指针异常，同时进一步强化解耦（依赖关系由容器管理，而非代码硬编码）。

4. 与 IOC 的关系

- IOC 是 “对象创建控制权转移”，解决 “是否主动`new`对象” 的问题；
- DI 是 “依赖关系自动绑定”，解决 “Bean 依赖如何满足” 的问题；
- 两者结合：IOC 容器先创建所有 Bean，再通过 DI 绑定 Bean 间的依赖，最终实现 “开发者取到的 Bean 可直接运行”，达成 “**充分解耦**” 的目标。



dao-data access object 