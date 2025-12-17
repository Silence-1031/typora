# Maven

## 第一章 Maven基础

### 第一节 Maven 简介

#### 一、Maven 核心定位

Maven 是一款**Java 项目管理工具**，核心解决 Java 项目开发中的资源管理与构建问题，采用**面向对象设计思想**，将整个项目抽象为 “项目对象模型（POM，Project Object Model）”，一个项目就是一个对象，通过 `pom.xml` 配置文件实现对项目的统一管理。

#### 二、Maven 解决的核心问题

在传统 Java 项目开发中，常面临以下痛点，Maven 针对性提供解决方案：

1. **Jar 包管理混乱**：传统项目需手动下载、复制 Jar 包，易出现版本冲突（Maven 通过 “依赖管理” 自动处理 Jar 包下载、版本匹配与冲突解决。
2. **跨环境构建繁琐**：Windows 开发环境与 Linux 服务器环境存在差异（如`String`类的`getBytes()`方法在不同系统下编码格式不同），传统手动打包部署易出错；Maven 提供 “标准构建生命周期”，支持跨平台自动化编译、测试、打包、部署，一条指令即可完成全流程。
3. **项目结构不统一**：不同开发工具（Eclipse、IDEA、NetBeans）默认项目结构不同，团队协作时需适配；Maven 定义统一的项目目录结构，所有 Java 开发者遵循同一标准，降低协作成本。

#### 三、Maven 三大核心功能

1. ##### 依赖管理（Jar 包管理）

 （1）核心概念

- **依赖传递**：项目依赖的 Jar 包若本身依赖其他 Jar 包，Maven 会自动下载间接依赖的 Jar 包。依赖管理的东西最终都是从中央仓库拿到的
- **仓库体系**：Maven 通过 “仓库” 存储 Jar 包，分为三级：
  - **本地仓库**：开发者电脑本地的 Jar 包存储目录（默认路径：`C:\Users\用户名\.m2\repository`），优先从本地仓库获取 Jar 包。
  - **私服仓库**：企业 / 团队内部搭建的共享仓库，存储团队自定义组件（如自研的财务模块、结算模块）或中央仓库难以获取的 Jar 包，团队成员统一从私服获取资源，提高效率。
  - **中央仓库**：Maven 官方维护的全球公共仓库（https://repo.maven.apache.org/maven2/），存储全球开源项目的 Jar 包，本地仓库和私服仓库中缺失的 Jar 包会自动从中央仓库下载。

 （2）依赖配置

- 通过`pom.xml`的`<dependencies>`标签配置项目依赖，示例：

```xml
<dependencies>
    <!-- 配置JUnit依赖，包含groupId（组织ID）、artifactId（项目ID）、version（版本）三大坐标 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope> <!-- 依赖范围：仅在测试阶段生效 -->
    </dependency>
</dependencies>
```

2. ##### 项目构建（生命周期与插件）

- Maven 本身不直接实现构建功能，而是通过 “插件” 完成各阶段任务（如编译依赖`maven-compiler-plugin`，打包依赖`maven-jar-plugin`），插件与生命周期阶段绑定，执行阶段时自动调用对应插件。

![image-20251016124521792](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251016124521792.png)

##### 3. 统一项目结构

- 提供**标准的、跨平台的**自动化项目构建方式

- Maven 定义标准的 Java 项目目录结构，所有文件需放在指定位置，示例（Java 工程）：

```plaintext
项目根目录
├── pom.xml          # Maven核心配置文件（项目对象模型定义）
├── src
│   ├── main         # 主程序目录
│   │   ├── java     # Java源代码目录（存放.java文件）
│   │   └── resources# 资源文件目录（存放配置文件，如application.properties）
│   └── test         # 测试程序目录
│       ├── java     # 测试源代码目录（存放测试类，如XXXTest.java）
│       └── resources# 测试资源文件目录
└── target           # 构建输出目录（存放编译后的字节码、打包文件等，自动生成）
```



### 第二节 仓库和坐标

- 仓库是 Maven 中**存储资源（主要是 Jar 包）的位置**，用于统一管理项目依赖的第三方库或自定义组件

#### 一、仓库的分类（核心）

仓库按 “存储位置” 分为**本地仓库**和**远程仓库**两大类，远程仓库又细分为 “中央仓库” 和 “私服”，具体差异如下表：

| 分类     | 存储位置                              | 维护者          | 核心特点                                                     |
| -------- | ------------------------------------- | --------------- | ------------------------------------------------------------ |
| 本地仓库 | 开发者自己的电脑                      | 开发者个人      | 1. 中央仓库 / 私服的 “子集”，仅保存本地项目已下载的资源；2. 优先读取，无资源时才从远程获取。 |
| 中央仓库 | Maven 官方云端服务器（非中国境内）    | Maven 开发团队  | 1. 全球开源 Jar 包的 “总库”，包含 99% 以上的公开开源组件；2. 不存储带版权的闭源组件（如 Oracle 驱动 Jar 包）。 |
| 私服     | 企业 / 团队内部的服务器（局域网为主） | 企业 / 团队自己 | 1. 部署在内部网络，访问速度远快于中央仓库；2. 一定范围内共享资源，仅对内部开放，不对外共享 |

![image-20251016135613014](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251016135613014.png)



#### 二、jar 包（资源）的获取流程

Maven 项目获取 Jar 包时，遵循 “**本地优先、远程补充**” 的逻辑，具体流程如下：

1. 项目需要某 Jar 包时，先检查**本地仓库**是否存在该资源；

2. 若本地仓库存在，直接读取使用；

3. 若本地仓库不存在，先请求

   私服（若配置了私服）：

   - 私服存在该资源：从私服下载到本地仓库，再供项目使用；
   - 私服不存在该资源：由私服自动从**中央仓库**下载该资源，保存到私服后，再同步到本地仓库；

4. 若未配置私服，则直接从**中央仓库**下载资源到本地仓库，再供项目使用。



#### 三、坐标

- maven中的坐标用于描述仓库中资源的位置

- 坐标组成

  - groupid：定义当前Maven项目隶属组织名称（通常是域名反写，例如：org.mybatis）

  - artifactid：定义当前Maven项目名称（通常是模块名称，例如CRM、SMS）

  - version：定义当前项目版本号

  - packaging：定义项目的打包方式

- 作用
  使用唯一标识，唯一性定位资源位置，通过该标识可以将资源的识别与下载工作交由机器完成



### 第三节 仓库配置

#### 一、本地仓库配置（资源下载位置）

- **默认位置**：`C:\Users\Silence\.m2\repository`
- **默认路径问题**：Maven 依赖包（如 Jar 包）会随项目增多而占用大量空间，长期使用会导致系统盘存储压力过大。

#### 二、远程镜像仓库配置（资源来源优化）

1. Maven 默认远程仓库问题

- **默认来源**：Maven 默认从**中央仓库（Central Repository）** 下载资源，其服务器位于国外，国内访问时存在**下载速度慢、频繁失败**的问题。

- 中央仓库配置位置：藏于 Maven 安装目录

  ```
  lib\maven-model-builder-3.6.3.jar
  ```

  （版本可能不同）中，需解压 Jar 包后，在

  ```
  org\apache\maven\model
  ```

  目录下找到

  ```
  pom-4.0.0.xml
  ```

  其中定义了中央仓库地址：

  ![image-20251016142135890](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251016142135890.png)

  

2. 配置国内镜像仓库（推荐阿里云）

-  **核心原理：镜像仓库替代**
- **镜像仓库（Mirror Repository）** 是对 “原始仓库”（如 Maven 中央仓库、公司内部私有仓库）的**副本或代理**，核心作用是优化依赖下载效率、解决原始仓库访问不稳定的问题，本质是为 Maven 提供更易访问的 “资源获取节点”。

- 配置后，Maven 会**优先从镜像仓库下载资源**，而非直接访问国外中央仓库。

- 配置步骤

1. 再次打开`settings.xml`文件，找到`<mirrors>`标签（镜像配置节点）；

2. 在<mirrors>标签内添加阿里云镜像配置（覆盖默认中央仓库访问）：

   ```xml
   <mirrors>
     <mirror>
       <id>aliyunmaven</id> <!-- 镜像唯一标识，自定义即可 -->
       <mirrorOf>central</mirrorOf> <!-- 关键：指定镜像“替代”中央仓库（central） -->
       <name>阿里云公共仓库</name> <!-- 镜像名称，可选 -->
       <url>https://maven.aliyun.com/repository/public</url> <!-- 阿里云镜像地址 -->
     </mirror>
   </mirrors>
   ```

3. 保存文件，配置生效：后续下载依赖时，Maven 会自动通过阿里云镜像获取资源，大幅提升速度。

   

#### 三、全局 Setting 与用户 Setting 的区别

| 类型         | 定义（作用范围）                                    | 默认路径                                              |
| ------------ | --------------------------------------------------- | ----------------------------------------------------- |
| 全局 Setting | 作用于**当前计算机的所有 Maven 实例**，所有用户共用 | Maven 安装目录 \conf\settings.xml                     |
| 用户 Setting | 作用于**当前登录用户**，仅对个人生效                | C 盘 \Users\ 当前用户名.m2\settings.xml（需手动创建） |



### 第五节 依赖配置

#### 一、依赖配置

##### 1. 依赖

- **依赖（Dependency）** 指的是当前项目运行、编译或测试时所需要的外部资源（通常是第三方库、框架的 JAR 包，或其他自定义模块）。这些资源并非项目自身代码，而是通过标准化配置引入，用于避免手动拷贝文件、简化资源管理
- 格式

```xml-dtd
<！--设置当前项目所依赖的所有jar-->
<dependencies>
	<！--设置具体的依赖-->
	<dependency>
		<！--依赖所属群组id-->
		<groupId>junit</groupId>
		<！--依赖所属项目id-->
		<artifactId>junit</artifactId>
		<！--依赖版本号-->
		<version>4.12</version>
	</dependency>
</dependencies>
```

##### 2. 依赖传递

![image-20251017130402842](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251017130402842.png)

- 依赖最核心的特性之一是**依赖传递**—— 当你引入一个依赖 A 时，如果 A 本身还依赖其他资源 B（即 B 是 A 的 “子依赖”），Maven 会自动将 B 也引入到你的项目中，无需你手动配置 B。
- 比如你引入`JUnit`（依赖 A），而`JUnit`运行需要`hamcrest-core`（依赖 B），Maven 会自动下载并管理`hamcrest-core`，你在项目中可以直接使用它，无需额外写`<dependency>`标签。

- 直接依赖：在当前项目中通过依赖配置建立的依赖关系
  间接依赖：被资源的资源如果依赖其他资源，当前项目间接依赖其他资源

- 依赖传递冲突问题

  当出现多层次的依赖时，可能会出现版本冲突的问题

  - 路径优先：当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高
  - 声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的
  - 特殊优先：当同级配置了相同资源的不同版本，后配置的覆盖先配置的

##### 3. 可选依赖

- 一种特殊的依赖配置，用于控制 “依赖是否会被传递给间接引用当前项目的其他项目”—— 简单来说，它是当前项目的 “私有依赖”，默认不随依赖传递暴露给下游项目，仅在当前项目内部生效

```xml
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.12</version>
	<optional>true</optional> -- 增加项目
</dependency>
```

##### 4. 排除依赖

- **排除依赖（Exclusions）** 是下游项目（依赖方）主动 “剔除” 上游项目（被依赖方）通过依赖传递带来的某个 / 某些间接依赖的机制

- 简单来说，当你依赖的项目 A 自动传递了一个你用不到、或与其他依赖冲突的间接依赖 B 时，你可以通过 “排除依赖” 主动断开 B 的引入，避免冗余或版本冲突问题。

- 排除依赖的配置方式：精准定位需剔除的依赖

  排除依赖的配置需在**下游项目的`pom.xml`** 中进行：在你依赖的 “上游项目坐标” 内，通过 `<exclusions>` 标签嵌套 `<exclusion>`，指定要排除的 “间接依赖坐标”（只需`groupId`和`artifactId`，无需`version`，因为要排除的是该依赖的所有版本）。

```xml
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.12</version>
	<exclusions>
		<exclusion>
			<groupId>org.hamcrest</groupId>
			<artifactId>hamcrest-core</artifactId>
		</exclusion>
	</exclusions>
</dependency>
```

##### 

##### 5. 依赖范围

- 用于控制依赖包jar在项目的不同生命周期（主程序编译、测试编译、项目打包、运行）中的可用性，核心作用是避免不必要的依赖冗余、解决版本冲突，并明确依赖的使用场景
- 依赖的jar包 默认情况可以在任何地方使用，可以通过scope标签设定其作用范围

- 作用范围：
  - 主程序范围有效（main文件夹范围内）
  - 测试程序范围有效（test文件夹范围内）
  - 是否参与打包（package指令范围内）

| 依赖范围（Scope）   | 主程序编译 / 运行        | 测试程序编译 / 运行 | 是否参与项目打包 | 典型使用场景                                                 |
| ------------------- | ------------------------ | ------------------- | ---------------- | ------------------------------------------------------------ |
| **compile（默认）** | ✅ 可用                   | ✅ 可用              | ✅ 参与打包       | 项目核心依赖，如日志框架（Log4j）、MyBatis、Spring 核心包等（主程序和测试都需要，打包后运行也依赖） |
| **test**            | ❌ 不可用                 | ✅ 可用              | ❌ 不参与打包     | 仅测试阶段使用的依赖，如 Junit（`@Test`注解）、测试工具类（仅测试代码用，主程序和最终打包无需包含） |
| **provided**        | ✅ 可用                   | ✅ 可用              | ❌ 不参与打包     | 运行环境已提供的依赖，如 Servlet API（Tomcat 服务器自带，打包时无需重复加入，避免版本冲突） |
| **runtime**         | ✅ 运行可用（编译不可用） | ✅ 测试可用          | ✅ 参与打包       | 编译阶段无需直接引用，但运行 / 测试阶段需要的依赖，如 MySQL JDBC 驱动（主程序仅用字符串指定驱动类名，编译不依赖，但运行需加载驱动包） |

- 依赖范围的传递性

   带有依赖范围的资源在进行传递时，作用范围会受到影响

1. 若**直接依赖的 scope 为 compile**
   - 间接依赖的 scope 为`compile` → 最终范围为`compile`；
   - 间接依赖的 scope 为`runtime` → 最终范围为`runtime`；
   - 间接依赖的 scope 为`test`/`provided` → 最终不传递（项目 A 无法使用该间接依赖）。
2. 若**直接依赖的 scope 为 test**：仅能传递间接依赖中 scope 为`test`的依赖，其他范围不传递。
3. 若**直接依赖的 scope 为 provided**：间接依赖的 scope 无论是什么，均不传递（因运行环境已提供，无需间接依赖）。



### 第六节 生命周期与插件

#### 一、项目构建生命周期

- Maven构建生命周期描述的是一次构建过程经历经历了多少个事件

![image-20251017164716627](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251017164716627.png)

- Maven对项目构建的生命周期划分为3套：
  - clean：清理工作
  - default：核心工作，例如编译、测试、打包、部署等
  - site：产生报告，发布站点等

##### 1.clean生命周期

- 核心作用是**清理项目构建过程中生成的各种临时文件和输出产物**，避免旧的构建文件干扰新构建，确保构建环境的 “干净”。

|   阶段名称   | 执行顺序  |     核心作用     |                           关键说明                           |
| :----------: | :-------: | :--------------: | :----------------------------------------------------------: |
| `pre-clean`  | 1（前置） | 清理前的准备工作 | 可自定义扩展（如备份旧构建文件、停止关联服务等），默认无任何操作 |
|   `clean`    | 2（核心） | 清理核心构建产物 | **最常用阶段**，默认删除项目根目录下的`target`文件夹（包含编译后的 class 文件、测试报告、打包产物如 JAR/WAR 等） |
| `post-clean` | 3（后置） | 清理后的收尾工作 | 可自定义扩展（如删除日志文件、清理临时目录等），默认无任何操作 |

##### 2.default生命周期

- **default 生命周期**是最核心、最常用的生命周期，它覆盖了从项目源码编译、测试、打包到部署的**完整构建流程**，是 Maven 实现 “自动化项目构建” 的核心载体。无论是开发中的代码编译，还是测试、发布产物，本质上都是在执行 default 生命周期的不同阶段。
- 在某个阶段即执行这个阶段前所有的操作

|         阶段名称          |        核心作用         |                 绑定的默认插件（Java 工程）                  |                           关键说明                           |
| :-----------------------: | :---------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|        `validate`         |     验证项目完整性      |                          无默认插件                          | 检查`pom.xml`配置是否正确（如坐标、依赖是否完整）、必要文件是否存在，确保构建可正常启动 |
|       `initialize`        |     初始化构建环境      |                          无默认插件                          | 初始化构建参数、创建临时目录、加载外部配置等（可自定义扩展，如设置环境变量） |
|    `generate-sources`     |       生成源代码        |                    无默认插件（需自定义）                    | 生成项目所需的额外源码（如通过`mybatis-generator`生成 DAO 层代码） |
|     `process-sources`     |       处理源代码        |              `maven-resources-plugin:resources`              | 复制项目主资源文件（`src/main/resources`）到编译目录（`target/classes`），并处理资源过滤（如替换`pom.xml`中的属性） |
|   `generate-resources`    |      生成资源文件       |                    无默认插件（需自定义）                    |       生成项目所需的额外资源文件（如动态生成配置文件）       |
|    `process-resources`    |      处理资源文件       |                 （继承自`process-sources`）                  |             整合主源码资源与生成的资源，准备编译             |
|       **`compile`**       |       编译主源码        |               `maven-compiler-plugin:compile`                | **核心阶段**：将`src/main/java`下的 Java 源码编译为字节码（`class`文件），输出到`target/classes` |
|     `process-classes`     | 处理编译后的 class 文件 |                    无默认插件（需自定义）                    | 对编译后的 class 文件做额外处理（如字节码增强、类加密，常用插件：`maven-shade-plugin`） |
|  `generate-test-sources`  |     生成测试源代码      |                    无默认插件（需自定义）                    |         生成测试所需的额外源码（如测试数据生成代码）         |
|  `process-test-sources`   |     处理测试源代码      |            `maven-resources-plugin:testResources`            | 复制测试资源文件（`src/test/resources`）到测试编译目录（`target/test-classes`） |
| `generate-test-resources` |    生成测试资源文件     |                    无默认插件（需自定义）                    |                  生成测试所需的额外资源文件                  |
| `process-test-resources`  |    处理测试资源文件     |               （继承自`process-test-sources`）               |        整合测试源码资源与生成的测试资源，准备测试编译        |
|    **`test-compile`**     |      编译测试源码       |             `maven-compiler-plugin:testCompile`              | 编译`src/test/java`下的测试源码，输出到`target/test-classes` |
|  `process-test-classes`   |   处理测试 class 文件   |                    无默认插件（需自定义）                    |       对测试 class 文件做额外处理（如测试字节码增强）        |
|        **`test`**         |      执行测试用例       |                 `maven-surefire-plugin:test`                 | **核心阶段**：运行测试用例（如 JUnit、TestNG 测试），生成测试报告（`target/surefire-reports`），测试失败会终止构建（可配置跳过） |
|     `prepare-package`     |       打包前准备        |                    无默认插件（需自定义）                    | 打包前的预处理（如修改配置文件、整理资源结构，Web 工程会在此阶段处理`WEB-INF`目录） |
|       **`package`**       |      打包项目产物       | Java 工程：`maven-jar-plugin:jar`Web 工程：`maven-war-plugin:war` | **核心阶段**：将编译后的资源打包为项目产物（JAR/WAR/EAR），输出到`target`目录（如`xxx.jar`、`xxx.war`） |
|  `pre-integration-test`   |     集成测试前准备      |                    无默认插件（需自定义）                    |      集成测试前的环境准备（如启动数据库、部署测试服务）      |
|    `integration-test`     |      执行集成测试       |           `maven-failsafe-plugin:integration-test`           | 运行集成测试用例（区别于`test`阶段的单元测试，集成测试需依赖外部环境，如数据库、接口） |
|  `post-integration-test`  |     集成测试后清理      |                    无默认插件（需自定义）                    |      集成测试后的环境清理（如关闭数据库、删除测试数据）      |
|         `verify`          |     验证产物完整性      |                `maven-failsafe-plugin:verify`                | 检查集成测试结果、验证产物是否符合质量要求（如代码覆盖率、产物格式校验） |
|       **`install`**       |   安装产物到本地仓库    |                `maven-install-plugin:install`                | **核心阶段**：将`package`阶段生成的产物（JAR/WAR）安装到本地 Maven 仓库（默认路径：`C:\Users\用户名\.m2\repository`），供本地其他项目依赖引用 |
|       **`deploy`**        |   部署产物到远程仓库    |                 `maven-deploy-plugin:deploy`                 | **核心阶段**：将产物部署到远程 Maven 仓库（如公司私服 Nexus），供团队其他成员或生产环境使用（需在`pom.xml`中配置远程仓库地址） |

##### 3.site阶段

- **site 生命周期**是专门用于**生成项目站点文档**的生命周期，其核心作用是将项目的配置信息、依赖说明、测试报告、API 文档等内容整合为结构化的静态 HTML 文档

|   阶段名称    | 执行顺序  |                           核心作用                           |                           关键说明                           |
| :-----------: | :-------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  `pre-site`   | 1（前置） |                     站点生成前的准备工作                     | 可自定义扩展（如清理旧站点文件、下载外部文档资源、准备生成所需的配置），默认无任何操作。 |
|    `site`     | 2（核心） |                 生成项目站点的核心 HTML 文档                 | **最常用阶段**：默认生成的文档包含项目基本信息（如`pom.xml`中的坐标、描述）、依赖列表、构建状态、SCM（版本控制）信息等；若绑定其他插件，可额外生成 API 文档（Javadoc）、测试报告（Surefire 报告）等。 |
|  `post-site`  | 3（后置） |                     站点生成后的收尾工作                     | 可自定义扩展（如压缩站点文件、验证 HTML 格式正确性、发送站点生成通知），默认无任何操作。 |
| `site-deploy` | 4（部署） | 将生成的站点文档部署到远程服务器（如公司内部文档服务器、GitHub Pages 等） | 需在`pom.xml`中配置远程站点地址（如`distributionManagement/site`），部署后团队成员可通过 URL 访问站点。 |



#### 二、插件

- 在 Maven中，**插件（Plugin）是实现特定功能的 “工具模块”**，它是 Maven 生命周期与实际操作之间的 “桥梁”——Maven 的生命周期阶段（如`compile`编译、`package`打包、`site`生成文档）本身不执行具体工作，而是通过 “绑定插件的目标（Goal）” 来完成任务
- 插件与生命周期内的阶段绑定，在执行到对应生命周期时执行对应的插件功能。默认maven在各个生命周期上绑定有预设的功能
- 通过插件可以自定义其他功能

```xml
<build>
	<plugins>
		<plugin>
            <!--插件的坐标-->
			<groupId>org.apache.maven. plugins</groupId>
			<artifactId>maven-source插in</artifactId>
			<version>2.2.1</version>
            <!--插件在什么时候执行-->
			<executions>
				<execution>
                    <!--goal字段不是每个插件都有，jar指把源码打包--，可以有多个goal>
					<goals>
						<goal>jar</goal>
					</goals>
					<phase>generate-test resources</phase>
				</execution>
			</exeutions>
		</plugin>
	</plugins>
</build>
```



## 第二章 Maven高级

### 第一节 分模块开发与设计

#### 一、工程模块与模块划分

![image-20251017171546549](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251017171546549.png)

##### 1.SSM 项目拆分

​	将原本 “一个工程包含所有代码” 的结构，拆分为 4 个独立模块：

- `pojo模块`：仅存放实体类（如`User.java`、`Order.java`），无业务逻辑；
- `dao模块`：仅处理数据访问（如 MyBatis 的 Mapper 接口、SQL 映射文件）；
- `service模块`：仅实现业务逻辑（如`UserService.java`，调用 dao 模块的方法）；
- `controller模块`：仅负责接口暴露（如 SpringMVC 的`UserController.java`，调用 service 模块）。

##### 2. 按 “职责分层” 划分

对应软件架构的 “分层思想”，将模块与 “三层架构 / 四层架构” 一一对应，是企业级 Java 项目的标准操作：

| 架构分层         | 对应模块        | 核心职责                                        | 依赖关系             |
| ---------------- | --------------- | ----------------------------------------------- | -------------------- |
| 表现层（API 层） | controller 模块 | 接收请求、返回响应（如 RESTful 接口、参数校验） | 依赖 service 模块    |
| 业务层           | service 模块    | 实现业务逻辑（如 “下单”“支付校验”）             | 依赖 dao 模块        |
| 数据访问层       | dao 模块        | 操作数据库（如 CRUD、事务控制）                 | 依赖 pojo 模块       |
| 实体层           | pojo 模块       | 定义数据结构（如数据库表对应的实体类）          | 无依赖（最底层模块） |





### 第二节 聚合

### 第三节 继承

### 第四节 属性

### 第五节 版本管理

### 第六节 资源配置

### 第七节 多环节开发配置

### 第八节 跳过测试

### 第九节 私服