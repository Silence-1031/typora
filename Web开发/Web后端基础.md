# Web后端基础

## 第一章 Maven

### 第一节 简介

一、Maven 基础概念

1. **定义**

   Maven 是**Apache 软件基金会旗下的开源项目**，是一款专门用于**管理和构建 Java 项目的工具**，在企业级 Java 开发中应用广泛（几乎所有 Java 项目开发都依赖 Maven）。

2. **Apache 软件基金会补充**：成立于 1999 年 7 月，是全球最大、最受欢迎的**非盈利开源软件基金会**，专注于支持开源项目发展。

二、核心作用

1. 方便快捷的**依赖管理**

- **传统依赖管理的痛点**：使用第三方 JAR 包（如 commons-IO 工具包）时，需手动从官网下载 JAR 包，再拷贝到项目`lib`目录，版本切换需重复下载 + 替换，操作繁琐。
- **Maven 的解决方案**：仅需在 Maven 核心配置文件`pom.xml`中，通过`<dependencies>`标签配置 JAR 包的 “名称 + 版本”，Maven 会**自动联网下载 JAR 包并导入项目**；版本切换仅需修改配置文件中的版本号，刷新后即可生效。

2. 标准化的跨平台项目构建流程

- **传统项目构建的痛点**：Java 项目运行需经历 “编译（javac 指令）→测试→打包→发布” 等步骤，若无工具辅助，需手动执行指令；大型项目模块多，步骤重复且易出错，且不同系统（Windows/Linux/Mac）指令可能不兼容。
- **Maven 的解决方案**：Maven 定义了标准化的构建生命周期（LIFECYCLE），提供`compile`（编译）、`package`（打包）等现成指令，**一键完成对应操作**，且指令跨平台兼容（各系统通用）。

3. 统一的项目结构

- **传统项目结构的痛点**：不同开发工具（Eclipse、IDEA、MyEclipse）创建的 Java 项目目录结构存在差异，导致项目无法跨工具直接导入（如 Eclipse 项目不能直接导入 IDEA），增加团队协作的开发 / 维护成本。

- **Maven 的解决方案**：定义一套**统一的标准项目结构**，无论使用何种工具，基于 Maven 创建的项目结构完全一致，可跨工具直接导入（通用且无需调整），降低协作成本。

- 标准结构详情

  | 目录 / 文件          | 作用                                              |
  | -------------------- | ------------------------------------------------- |
  | `src/main/java`      | 存放项目核心 Java 源代码                          |
  | `src/main/resources` | 存放项目核心配置文件（如 application.properties） |
  | `src/test/java`      | 存放测试相关的 Java 代码（如单元测试类）          |
  | `src/test/resources` | 存放测试相关的配置文件                            |
  | `pom.xml`            | Maven 核心配置文件（依赖、构建等配置）            |

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251108161833473.png" alt="image-20251108161833473" style="zoom:50%;" />

三、Maven 核心结构

Maven 的功能依赖于三大核心模型协同工作，具体如下：

| 核心模型                   | 作用说明                                                     | 关键载体 / 配置                                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 项目对象模型（POM）        | 描述项目基本信息（如组织名、项目名、版本）、依赖、构建规则等 | `pom.xml`配置文件                                            |
| 依赖管理模型（Dependency） | 定义项目依赖的第三方 Jar 包，包括依赖的`groupId`（组织标识）、`artifactId`（项目标识）、`version`（版本） | `pom.xml`中的`<dependencies>`标签                            |
| 项目构建生命周期           | 规定项目构建的 “标准步骤”，每个步骤对应一个 “阶段”，底层通过**插件**实现具体功能（如`compile`插件负责编译、`test`插件负责测试） | 常见阶段：compile（编译）→ test（测试）→ package（打包）→ install（安装到本地仓库） |

四、Maven 仓库（Jar 包存储与查找）

（1）仓库的作用

用于存放项目依赖的 Jar 包、插件等资源，解决 “依赖从哪里来” 的问题。

（2）仓库的分类（三类）

1. **本地仓库**：开发者电脑上的本地目录（如`D:\develop\maven\mvn_repo`），是 Maven 查找依赖的 “第一站”，下载后的 Jar 包会缓存到本地，下次直接复用。
2. **中央仓库**：Maven 团队维护的 “全球唯一公共仓库”（地址固定），存放几乎所有主流开源 Jar 包，但服务器在国外，下载速度较慢。
3. **私服（远程仓库）**：企业 / 团队内部搭建的私有仓库（如阿里云私服），仅内部成员可访问；作用是 “加速下载”（避免直连中央仓库）和 “管理内部私有 Jar 包”。

（3）依赖查找顺序（关键逻辑）

1. 当项目引入一个依赖时，Maven 先查询**本地仓库**：若有，直接使用；若没有，进入下一步。
2. 若配置了**私服**，查询私服：若有，下载到本地仓库后使用；若没有，由私服去查询中央仓库。
3. 私服从**中央仓库**下载依赖，先缓存到私服，再下载到本地仓库，最终供项目使用。





### 第二节 maven核心



### 第三节 maven进阶



## 第二章 SpringBootWeb

### 第一节 概述

一、SpringBoot

1. SpringBoot 的核心优势

- **简化配置**：自动完成大部分默认配置，无需手动编写大量 XML 或注解配置。
- **提高效率**：快速创建项目、自动引入依赖，降低开发成本。
- **官方推荐**：Spring 官网 “Quick Start” 明确推荐从 Spring Boot 开始构建 Spring 应用。
- **企业主流**：当前企业开发中最为主流的方式（几乎所有 Java Web 项目均基于 Spring Boot）。

2. SpringBoot 项目的核心结构

创建后的 SpringBoot 项目遵循 Maven 标准结构，关键文件 / 目录作用如下：

|                   文件 / 目录                    |        作用        |                           核心说明                           |
| :----------------------------------------------: | :----------------: | :----------------------------------------------------------: |
|                    `pom.xml`                     | 依赖管理与项目配置 | 必须继承`spring-boot-starter-parent`（统一管理依赖版本），需引入`spring-boot-starter-web`（Web 开发依赖） |
| 启动类（如`SpringBootWebQuickStartApplication`） |      项目入口      | 类上需加`@SpringBootApplication`注解，通过`main`方法启动项目 |
|               `src/main/resources`               |      资源目录      | - `static`：存放 HTML/CSS/JS 等静态文件- `templates`：存放模板文件（如 Thymeleaf）- `application.properties`：核心配置文件（默认空，可配置端口、数据库等） |
|                    `src/test`                    |    测试代码目录    |                 自动生成测试类，用于单元测试                 |

 二、SpringBoot 入门程序启动原理（核心：起步依赖）

1. 起步依赖（Starter）概念

起步依赖是 SpringBoot 的核心特性，本质是**Maven 依赖的 “集合包”**：SpringBoot 将某类功能（如 Web 开发、单元测试）所需的所有依赖（含第三方框架、工具类等）封装为一个独立依赖，开发者只需引入这 1 个依赖，即可自动获取该功能所需的全部依赖（基于 Maven 的 “依赖传递” 特性）。

2. 核心起步依赖解析

创建 SpringBootWeb 项目时，默认引入 2 个关键起步依赖，分别对应核心功能：

| 起步依赖名称               | 功能作用                                              | 关键传递依赖                                                 |
| -------------------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| `spring-boot-starter-web`  | 支持 Web 应用开发（如 HTTP 请求处理、Servlet 容器等） | `spring-boot-starter-tomcat`（内嵌 Tomcat）、SpringMVC 核心依赖等 |
| `spring-boot-starter-test` | 支持单元测试（如 Junit、AssertJ 等测试框架）          | Junit、Mockito、Spring Test 等                               |

3. 启动原理：内嵌 Tomcat 的自动启动

Web 应用能通过`main`方法启动的核心原因的是 **`spring-boot-starter-web`传递引入了内嵌 Tomcat**：

1. 引入`spring-boot-starter-web`后，Maven 自动下载`spring-boot-starter-tomcat`（Tomcat 服务器依赖）；
2. 运行启动类的`main`方法时，SpringBoot 会自动初始化并启动内嵌的 Tomcat 服务器；
3. Tomcat 启动后，会自动将项目代码（如 Controller、Service 等）部署到服务器中；
4. 服务器默认监听 8080 端口，开发者可通过`localhost:8080`访问 Web 应用。



## 第三章 HTTP协议

###  第一节 概述

一、HTTP 协议的基本定义

HTTP 是 **Hyper Text Transfer Protocol** 的缩写，中文译为 “超文本传输协议”。

- “超文本” 与 HTML（超文本标记语言）中的概念一致，可理解为包含链接、多媒体等扩展内容的文本；本质是**浏览器（客户端）与服务器之间数据传输的规则约定**。

**核心作用**

规定客户端与服务器交互时的**数据格式**，确保双方能正确解析对方发送的信息：

- 客户端发送请求时，需按 HTTP 格式携带 “需要什么数据、数据格式要求” 等信息；
- 服务器处理请求后，需按 HTTP 格式返回 “处理结果、响应数据” 等信息；
- 若不遵循该格式，服务器无法解析请求、客户端无法解析响应

二、HTTP 协议的三大核心特点

1. **基于 TCP 协议**
   - TCP 是 “面向连接、安全可靠” 的传输层协议，HTTP 依赖 TCP 实现数据传输；
   - 通信前需先通过 “三次握手” 建立连接，确保客户端与服务器都具备收发数据的能力，避免数据丢失，提升传输安全性。
2. **基于请求 - 响应模型**
   - 严格遵循 “一次请求对应一次响应” 的规则：**没有请求，就没有响应**；
   - 流程固定：客户端发起请求 → 服务器接收并处理 → 服务器返回响应 → 客户端接收响应（一次交互结束）。
3. **无状态协议**
   - 定义：HTTP 协议对 “事务处理没有记忆能力”，即**多次请求之间相互独立，后一次请求不会记录前一次请求的信息**；
   - 优缺点：
     - 优点：无需保存历史请求状态，减少服务器内存占用，提升请求处理速度；
     - 缺点：多次请求之间无法共享数据（例如登录后，后续请求无法直接识别 “已登录状态”）；
     - 解决方案：后续会学习 “会话技术”（如 Cookie、Session、JWT 令牌）来弥补无状态的缺陷，实现跨请求数据共享。



### 第二节 HTTP请求协议

一、HTTP 请求数据的格式（3 个组成部分）

HTTP 请求数据由**请求行、请求头、请求体**三部分构成，三者结构严格且存在明确区分逻辑：

1. 第一部分：请求行（请求数据的第一行）

- **作用**：描述请求的核心元信息，包含 3 个关键要素，格式为 `请求方式 请求路径 协议版本`。
- 三要素详解
  - **请求方式**：指定请求的类型，最常见为`GET`和`POST`；
  - **请求路径**：指定要访问的服务器资源地址，若为`GET`请求，可在路径后通过`?`携带请求参数（如`/request?name=itheima&age=18`）；
  - **协议版本**：当前使用的 HTTP 协议版本，主流为`HTTP/1.1`。

![image-20251109124042798](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251109124042798.png)



2. 第二部分：请求头（请求行之后，以`Key: Value`格式存在）

- **作用**：传递请求的附加信息（如浏览器信息、请求主机、数据类型等），格式统一为`Key: Value`（冒号后需加空格）。

- 常见请求头及含义

  |     请求头 Key     |                           作用说明                           |
  | :----------------: | :----------------------------------------------------------: |
  |       `Host`       | 指定请求的目标主机名和端口（如`localhost:8080`），告诉服务器 “要访问哪台主机”； |
  | `User-Agent`（UA） | 传递发起请求的浏览器版本信息（如 Chrome、Firefox），服务器可据此适配不同浏览器； |
  |      `Accept`      | 声明浏览器能接收的资源类型（如`application/json`表示可接收 JSON 格式数据，`*/*`表示可接收所有类型）； |
  | `Accept-Encoding`  | 声明浏览器支持的压缩格式（如`gzip`），服务器可据此对响应数据压缩，节省带宽； |
  | `Accept-Language`  | 声明浏览器的语言偏好（如`zh-CN`表示简体中文），服务器可据此返回对应语言的内容； |
  |   `Content-Type`   | 仅`POST`请求常用，指定请求体中数据的格式（如`application/json`表示请求体是 JSON 数据）； |
  |  `Content-Length`  | 仅`POST`请求常用，指定请求体数据的字节大小，帮助服务器判断数据是否接收完整； |

3. 第三部分：请求体（仅部分请求有）

- **作用**：存放请求的核心数据（如表单提交的参数、JSON 数据等），**仅`POST`请求常用**，`GET`请求通常无请求体。
- 关键特性
  - 与请求头之间必须有一个 “空行”，作为两者的分隔标志（HTTP 协议的强制格式要求）；
  - `POST`请求通过请求体传递数据，数据大小无限制（区别于`GET`请求的参数大小限制）；
  - 数据格式由`Content-Type`请求头指定（如 JSON、表单格式等）。

4. GET 与 POST 请求的核心差异（基于请求格式）

|   对比维度   |                GET 请求                |                  POST 请求                   |
| :----------: | :------------------------------------: | :------------------------------------------: |
| 请求参数位置 |       拼接在请求路径后（`?`后）        |                 放在请求体中                 |
| 是否有请求体 |                 通常无                 | 有（需配合`Content-Type`和`Content-Length`） |
| 参数大小限制 | 有（受浏览器 / 服务器限制，通常几 KB） |      无（理论上无限，取决于服务器配置）      |

![image-20251109124421512](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251109124421512.png)

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251109124556045.png" alt="image-20251109124556045" style="zoom:67%;" />



二、服务器端获取请求数据（基于`HttpServletRequest`对象）

Web 服务器（如 Tomcat）会自动解析 HTTP 原始请求数据，并将解析结果封装到`HttpServletRequest`对象中，开发者无需手动解析原始协议数据，**直接通过该对象获取请求信息**即可。

1. 核心前提

- 在 SpringBoot（或 JavaWeb）的 Controller 方法中，只需在方法形参中声明`HttpServletRequest`类型的参数，框架会自动将封装好的对象传入（依赖注入）。

- 示例代码结构：

  ```java
  @RestController
  public class RequestController {
      @RequestMapping("/request") // 匹配请求路径
      public String getRequestData(HttpServletRequest request) {
          // 通过request对象获取请求数据
          return "OK";
      }
  }
  ```

  ![image-20251109124821780](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251109124821780.png)

2. 常用获取方法（对应请求数据的三部分）

|     要获取的信息     |      `HttpServletRequest`的方法      |                           方法说明                           |
| :------------------: | :----------------------------------: | :----------------------------------------------------------: |
|       请求方式       |        `request.getMethod()`         |        返回请求方式（如`GET`、`POST`，大写字符串）；         |
| 请求路径（完整 URL） | `request.getRequestURL().toString()` | 返回完整请求地址（如`http://localhost:8080/request?name=it黑马`）； |
| 请求路径（资源路径） |      `request.getRequestURI()`       |      返回资源访问路径（不含协议和主机，如`/request`）；      |
|     请求协议版本     |       `request.getProtocol()`        |                返回协议版本（如`HTTP/1.1`）；                |
|   请求参数（单个）   |   `request.getParameter("参数名")`   | 根据参数名获取值（适用于`GET`的路径参数、`POST`的请求体参数）； |
|      指定请求头      |   `request.getHeader("请求头Key")`   | 根据请求头 Key 获取值（如`request.getHeader("Accept")`获取浏览器支持的资源类型）； |



第三节 HTTP响应协议

一、HTTP 响应数据的基本格式

HTTP 响应数据由**三部分组成**，结构与请求协议类似，且**响应头与响应体之间需用空行分隔**，具体如下：

| 组成部分 |         位置         |                           核心内容                           |                             示例                             |
| :------: | :------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  响应行  |   响应数据第 1 行    | 包含 3 部分：1. HTTP 协议版本（如 HTTP/1.1）2. 响应状态码（如 200）3. 状态码描述（如 OK） |                      `HTTP/1.1 200 OK`                       |
|  响应头  | 响应行之后、空行之前 |     以`Key: Value`键值对形式存在，用于描述响应的附加信息     | `Content-Type: application/json`、`Location: https://www.baidu.com` |
|  响应体  |       空行之后       | 服务器响应给前端的**具体数据**（如 HTML 内容、JSON 数据、图片二进制流等） |      `<h1>Hello World</h1>`、`{"name":"Tom","age":20}`       |

![image-20251109132952176](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251109132952176.png)



二、HTTP 响应状态码

状态码共分**5 大类**（以数字第一位区分），每类对应不同的响应场景，需重点掌握典型状态码的含义：

| 状态码类别 | 第一位数字 |                     核心含义                     |                       典型状态码及场景                       |
| :--------: | :--------: | :----------------------------------------------: | :----------------------------------------------------------: |
|  临时响应  |     1      |    服务器已接收请求，正在处理（开发中较少见）    |        101：切换协议（如 WebSocket 建立长连接时使用）        |
|  成功响应  |     2      |           请求已被服务器成功接收并处理           |         200：请求成功（最常用，"程序员的幸运数字"）          |
|   重定向   |     3      | 请求需跳转到其他地址，浏览器会自动发起第二次请求 | 307：临时重定向（如访问`http://www.baidu.com`时，自动跳转至`https://www.baidu.com`） |
| 客户端错误 |     4      |  请求错误，责任在客户端（如路径错误、权限不足）  |         404：请求资源不存在（路径错误或资源已删除）          |
| 服务器错误 |     5      |      服务器处理请求时发生异常，责任在服务器      |  500：服务器端不可预期错误（如代码抛异常、数据库连接失败）   |

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251109133221454.png" alt="image-20251109133221454" style="zoom:50%;" /><img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251109133547412.png" alt="image-20251109133547412" style="zoom: 50%;" />

**关键提示**：开发中需优先关注 3 个状态码 ——200（成功）、404（客户端路径错）、500（服务器代码错)。

三、常见响应头及其作用

响应头以`Key: Value`形式传递附加信息，常用响应头及含义如下：

| 响应头 Key       | 核心作用                                              | 示例                                                         |
| ---------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| Content-Type     | 描述响应体的数据格式（告诉浏览器如何解析响应体）      | `Content-Type: text/html`（HTML 页面）、`Content-Type: application/json`（JSON 数据） |
| Content-Length   | 响应体的字节长度（帮助浏览器判断数据是否接收完整）    | `Content-Length: 1024`（响应体共 1024 字节）                 |
| Content-Encoding | 响应内容的压缩算法（减少数据传输大小，提高效率）      | `Content-Encoding: gzip`（使用 gzip 压缩）                   |
| Cache-Control    | 指导浏览器如何缓存响应数据（控制缓存有效期）          | `Cache-Control: max-age=300`（浏览器缓存 300 秒）            |
| Location         | 配合 3xx 重定向状态码使用，指定重定向的目标地址       | `Location: https://www.baidu.com`（重定向到百度 HTTPS 地址） |
| Set-Cookie       | 服务器向浏览器设置 Cookie（用于会话跟踪，如登录状态） | `Set-Cookie: token=abc123; Path=/`（设置 tokenCookie）       |

**关键提示**：大部分响应头由服务器自动生成，开发中无需手动设置（特殊场景如重定向需设置`Location`）。



四、服务器端设置响应数据的两种方式（基于 JavaWeb/SpringBoot）

在 SpringBoot 开发中，可通过两种方式操作响应数据（无需手动解析 HTTP 协议原始数据，框架已封装）：

方式 1：基于`HttpServletResponse`对象（原生 API）

- **原理**：在 Controller 方法的形参中声明`HttpServletResponse`对象，框架会自动注入该对象，通过其方法设置响应行、响应头、响应体。

- 示例代码

  ```java
  @RequestMapping("/response")
  public void setResponse(HttpServletResponse response) throws IOException {
      // 1. 设置响应状态码
      response.setStatus(401);
      // 2. 设置响应头
      response.setHeader("Name", "AT黑码");
      // 3. 设置响应体（通过流写入）
      response.getWriter().write("<h1>Hello Response</h1>");
  }
  ```



方式 2：基于`ResponseEntity`对象（Spring 封装 API）

- **原理**：Spring 提供的`ResponseEntity`类可封装响应状态码、响应头、响应体，直接作为 Controller 方法的返回值，框架会自动解析并生成 HTTP 响应。

- **核心优势**：通过链式编程简化操作，无需手动处理流，更符合 Spring 开发风格。

  ```java
  @RequestMapping("/response2")
  public ResponseEntity<String> setResponseEntity() {
      // 链式编程：设置状态码→响应头→响应体，返回ResponseEntity对象
      return ResponseEntity
              .status(401) // 设置状态码401
              .header("Name", "JavaWeb-AI") // 设置响应头
              .body("<h1>Hello Response</h1>"); // 设置响应体
  }
  ```

**注意：** **状态码 / 响应头无需手动设置**，无特殊需求时，服务器会根据业务逻辑自动生成（如成功返回 200，无需手动调用`setStatus(200)`）；



## 第四章 分层解耦

### 第一节 三层架构

一、核心思想：为什么需要 “分层解耦”

在传统 Web 开发中，若将 “接收请求、处理业务、操作数据” 等逻辑混写在同一代码块（如早期 Servlet 直接写全量逻辑），会导致**代码耦合度高、维护困难、可扩展性差**（例如修改数据库操作需改动业务逻辑代码，修改业务逻辑需调整请求处理逻辑）。

“分层解耦” 的核心目标是：**按 “职责划分代码模块”，降低模块间依赖，实现 “高内聚、低耦合”**，让代码更易维护、复用和测试。

二、三层架构：Web 后端的标准分层模型

三层架构是 “分层解耦” 思想的经典落地方案，将 Web 后端代码按 “职责” 划分为三个独立层次，各层次仅与上下相邻层次交互，不跨层调用

![image-20251109141736766](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251109141736766.png)

|           层次名称           |                           核心职责                           |                       典型技术 / 组件                        |                         输入 / 输出                          |
| :--------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|   **表现层（Controller）**   | 1. 接收前端请求（如 HTTP 请求参数、JSON 数据）2. 调用业务层处理逻辑3. 封装响应结果（如 JSON、页面跳转）4. 不处理业务逻辑，仅做 “请求转发” | SpringMVC 的`@Controller`、`@RequestMapping`、`@RequestBody`、`@ResponseBody` |   输入：前端请求（参数、数据）输出：后端响应（结果、页面）   |
|    **业务层（Service）**     | 1. 核心业务逻辑处理（如用户登录校验、订单计算、数据校验）2. 调用数据层操作数据3. 处理业务规则（如 “用户余额不足不可下单”） | Spring 的`@Service`、业务接口与实现类（如`UserService`接口 +`UserServiceImpl`） | 输入：表现层传递的请求参数输出：处理后的业务结果（如 “登录成功 / 失败”） |
| **数据层（DAO/Repository）** | 1. 仅负责与数据库交互（如 CRUD 操作：查询数据、插入数据、更新数据）2. 不包含业务逻辑，仅做 “数据存取”3. 屏蔽数据库差异（如 MySQL/Oracle 切换不影响上层） |  MyBatis 的`@Mapper`、XML 映射文件、Spring 的`@Repository`   | 输入：业务层传递的查询 / 操作条件输出：数据库返回的原始数据（如实体类、列表） |

 三、调用流程（以 “用户查询” 为例）

1. **前端发起请求**：用户在页面点击 “查询个人信息”，发送 HTTP 请求到后端（如`/user/info?id=1`）；
2. **表现层（Controller）接收请求**：`UserController`通过`@RequestMapping`接收请求，获取参数`id=1`，调用业务层的`userService.getById(1)`；
3. **业务层（Service）处理逻辑**：`UserServiceImpl`调用`userDao.selectById(1)`获取数据，若需要可添加业务校验（如 “判断用户是否存在”），将处理后的`User`对象返回给表现层；
4. **数据层（DAO）操作数据库**：`UserDao`通过 MyBatis 执行 SQL（`select * from user where id=1`），从数据库查询数据并封装为`User`实体，返回给业务层；
5. **表现层响应结果**：`UserController`将`User`对象转为 JSON 格式，返回给前端页面，页面渲染展示。

四、分层架构的优势

1. **降低耦合度**：各层职责单一，修改某一层（如数据层从 MySQL 改为 Redis）无需改动其他层，仅需调整该层代码；
2. **提高可维护性**：代码按职责划分，问题定位更清晰（如 “查询数据错误” 直接排查数据层，“业务逻辑错误” 排查业务层）；
3. **提高复用性**：同一业务逻辑可被多个表现层接口调用（如 “用户校验” 逻辑可被 “登录”“修改密码” 两个接口复用）；
4. **便于测试**：可分层测试（如单独测试业务层：模拟数据层返回结果，无需依赖真实数据库；单独测试数据层：仅验证 SQL 正确性）。



### 第二节 分层解耦

- 耦合：衡量软件中各个层/各个模块的依赖关联程度。
- 内聚：软件中各个功能模块内部的功能联系。
- 软件设计原则：**高内聚低耦合**。

 一、前分层解耦的 “痛点” 

**传统分层开发的耦合问题**：

- 各层组件（如 Service 类、Dao 类）需手动`new`创建依赖对象（例：`UserService service = new UserService();`），导致代码耦合度高；
- 若依赖对象的构造方式变化（如 Dao 类新增构造参数），所有使用该对象的地方都需修改，维护成本高；
- 组件难以复用，且无法灵活切换实现类（如从`MySQLDao`切换为`OracleDao`需修改大量代码）。

核心目标：通过 IOC 与 DI 解决 “对象创建权” 与 “依赖管理” 的耦合问题，实现代码解耦、可维护性提升。

 二、核心概念 1：IOC（控制反转）

1. 定义

- **控制**：指 “对象的创建权” 和 “依赖关系的管理权”；
- **反转**：将传统开发中 “开发者手动创建对象、管理依赖” 的权力，转移给**框架（容器）** ，由框架统一创建和管理对象。

2. 本质：从 “主动创建” 到 “被动接收”

| 开发模式 | 对象创建方式                | 依赖管理方式     | 耦合度 |
| -------- | --------------------------- | ---------------- | ------ |
| 传统开发 | 开发者手动`new`（主动创建） | 手动维护依赖关系 | 高     |
| IOC 模式 | 框架创建（被动接收）        | 框架自动管理依赖 | 低     |

3. 核心价值

- 解耦：组件不再依赖具体实现类，仅依赖接口或抽象类；
- 简化开发：无需关注对象创建细节（如构造参数、初始化逻辑）；
- 便于维护：修改依赖实现时，只需配置框架，无需修改业务代码。



三、核心概念 2：DI（依赖注入）

1. 定义：DI（Dependency Injection，依赖注入）是 IOC 的**具体实现方式**：框架在创建对象时，自动将该对象所需的 “依赖对象”（如 Service 依赖的 Dao）注入到对象中，开发者无需手动赋值。
   - Bean对象：IOC容器中创建、管理的对象，称之为Bean。

2. 与 IOC 的关系

- IOC 是 “思想”：强调 “反转对象创建权”；
- DI 是 “手段”：通过 “注入依赖” 实现 IOC 思想，二者本质是同一概念的不同角度描述。

3. 实现

步骤 1：将Dao及Service层的实现类，交给IOC容器管理。使用@Component注解（注意：是加在实现类上，而非接口上）

步骤 2：为Controller 及 Service注入运行时所依赖的对象。使用@Autowired注解

步骤 3：获取 Spring 管理的对象



四、关键认知：Spring 容器

- **容器（ApplicationContext）** 是 Spring 实现 IOC/DI 的核心：负责创建、管理、销毁对象（称为 “Bean”），并处理 Bean 之间的依赖注入；
- 开发者只需通过配置（xml / 注解）定义 Bean 和依赖关系，容器会自动完成后续工作；
- 容器中的 Bean 默认是 “单例”（全局只创建一个对象），可通过配置修改作用域。



### 第三节 IOC详解

一、注解

- 要把某个对象交给IOC容器管理，需要在对应的类上加上如下注解之一：

|     注解     |         说明          |                       位置                        |
| :----------: | :-------------------: | :-----------------------------------------------: |
| @ Component  |  声明bean的基础注解   |            不属于以下三类时，用此注解             |
| @ Controller | @ Component的衍生注解 |                 标注在控制层类上                  |
|  @ Service   | @ Component的衍生注解 |                 标注在业务层类上                  |
| @ Repository | @ Component的衍生注解 | 标注在数据访问层类上（由于与mybatis整合，用的少） |

- 声明bean的时候，可以通过注解的value属性指定bean的名字，如果没有指定，默认为类名首字母小写。



二、组件扫描

1. 前面声明bean的四大注解，要想生效，还需要被组件扫描注解@ComponentScan扫描。
2. 该注解虽然没有显式配置，但是实际上已经包含在了启动类声明注解@SpringBotApplication 中，默认扫描的范围是启动类所在包及其子包。

![image-20251111163122599](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251111163122599.png)



### 第四节 DI详解

![image-20251111165159701](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251111165159701.png)

- **注意事项：**

1. @Autowired注解，默认是按照类型进行注入的。
2. 如果存在多个相同类型的bean，将会报出如下错误：

![image-20251111165402485](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251111165402485.png)



## 第五章 Mybatis

### 第一节 入门

 一、MyBatis 基础认知

1. 定位与核心作用

- **框架类型**：优秀的**持久层框架**（对应 Web 开发三层架构中的 “DAO 数据访问层”），核心功能是与数据库交互。
- **核心价值**：**简化 JDBC 开发**——JDBC 是 Sun 公司定义的 Java 操作数据库规范，但直接使用需编写大量繁琐代码（如手动加载驱动、处理连接、设置参数、解析结果集等），MyBatis 可大幅减少这些重复工作。

2. 与 JDBC 的关系

- **JDBC 本质**：Java EE 13 项规范之一，是 “底层操作数据库的标准”，但仅提供基础接口，实际开发效率低。
- **MyBatis 定位**：基于 JDBC 封装的框架，不替代 JDBC，而是通过封装简化其使用，解决 JDBC 的 “繁琐性” 问题，是当前企业开发中 Java 操作数据库的**主流技术**。

二、快速入门程序：3 步核心开发流程

以 “查询 user 表所有数据” 为例，完整开发流程分为**准备工作、依赖与配置、编写 SQL 与测试**三部分

1. 准备工作：搭建基础环境

需完成 3 个核心准备，为后续开发铺垫：

- **创建 Spring Boot 工程**
- **准备数据库表**：使用`mybatis`数据库中的`user`表（字段含`id`、`name`、`age`、`gender`、`phone`）
- 创建实体类（Entity）
  - 在`com.atguigu`包下新建`entity`子包，创建`User`类；
  - 实体类属性与表字段**保持一致**（便于 MyBatis 自动封装结果），属性类型推荐使用**包装类**（如`Integer id`而非`int id`）；
  - 生成`getter/setter`、`toString`方法，以及无参 / 有参构造器（可通过 IDEA 快捷键快速生成）。

2. 依赖引入与 MyBatis 配置

（1）引入核心依赖

Spring Boot 工程创建时，直接勾选 2 个必备依赖，无需手动编写`pom.xml`：

- **MyBatis 起步依赖**：`mybatis-spring-boot-starter`（整合 MyBatis 与 Spring Boot 的核心依赖）；
- **MySQL 驱动依赖**：`mysql-connector-java`。

> 补充：工程创建后可查看`pom.xml`，确认依赖已正确引入，Spring Boot 父工程会自动管理依赖版本。

（2）配置数据库连接四要素

MyBatis 操作数据库需明确 “连接目标”，需在 Spring Boot 默认配置文件`application.properties`中，配置**数据库连接四要素**（与图形化工具连接数据库的配置一致）：

```properties
# 1. 数据库驱动类全类名（MySQL固定写法）
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# 2. 数据库连接URL（指定主机、端口、数据库名，localhost=本机，3306=默认端口，mybatis=目标数据库名）
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis
# 3. 数据库访问用户名（如root）
spring.datasource.username=root
# 4. 数据库访问密码（根据自身数据库密码修改）
spring.datasource.password=1234
```

> 注意：需将`url`中的数据库名、`username`和`password`改为自身环境配置，避免连接失败。

3. 编写 SQL 与单元测试

MyBatis 定义 SQL 有 “注解” 和 “XML” 两种方式，入门阶段优先使用**注解方式**，流程如下：

（1）定义 Mapper 接口（持久层接口）：Mapper 接口对应传统三层架构的 DAO 层，是 MyBatis 的核心接口，**无需编写实现类（框架自动生成动态代理对象）**：

1. 在`com.atguigu`包下新建`mapper`子包，创建`UserMapper`接口；
2. 接口上添加`@Mapper`注解：标识该接口为 MyBatis 的 Mapper 接口，框架会自动生成代理对象，并交给 Spring IOC 容器管理；
3. 定义查询方法：方法返回值为`List<User>`（存储多条用户数据），方法上添加`@Select`注解并指定 SQL 语句：

```java
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import java.util.List;

@Mapper
public interface UserMapper {
    // 注解中编写SQL：查询user表所有数据
    @Select("select * from user")
    List<User> list(); // 方法名自定义，需见名知意
}
```

（2）单元测试验证

通过 Spring Boot 整合的单元测试，验证 SQL 执行结果，确保功能正常：

1. 打开`test/java`下的默认测试类（如`SpringBootMybatisQuickStartApplicationTests`）；
2. 类上已存在`@SpringBootTest`注解：该注解会自动加载 Spring Boot 环境，创建 IOC 容器；
3. 依赖注入`UserMapper`对象：使用`@Autowired`注解从 IOC 容器中获取 Mapper 代理对象；
4. 编写测试方法：调用`UserMapper.list()`方法查询数据，遍历输出结果：

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import java.util.List;

@SpringBootTest
class SpringBootMybatisQuickStartApplicationTests {

    // 依赖注入Mapper接口（框架自动生成代理对象）
    @Autowired
    private UserMapper userMapper;

    @Test
    void testListUser() {
        // 调用方法查询所有用户
        List<User> userList = userMapper.list();
        // 遍历输出结果（可使用Stream流或增强for循环）
        userList.forEach(user -> System.out.println(user));
    }
}
```



### 第二节 JDBC

一、JDBC 核心概念

1. JDBC 定义与本质

- **全称**：Java Database Connectivity（Java 数据库连接）。
- 本质：Sun 公司（后被 Oracle 收购）提供的一套操作关系型数据库的规范（接口），而非具体实现。
- 作用：统一 Java 程序操作不同关系型数据库（如 MySQL、Oracle、SQL Server）的方式，避免因数据库底层实现差异导致的 “一套代码对应一种数据库” 的问题。

![image-20251113201259598](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251113201259598.png)

2. JDBC 的实现：数据库驱动

- 实现者：由各数据库厂商提供（厂商最了解自身数据库的底层逻辑）。

  例：MySQL 厂商提供 “MySQL 驱动”，Oracle 厂商提供 “Oracle 驱动”，SQL Server 厂商提供 “SQL Server 驱动”。

- **开发者视角**：编写 Java 代码时，只需面向 JDBC 接口编程（调用接口方法），底层实际执行的是 “数据库驱动” 中的实现类代码（即引入的驱动 Jar 包）。

二、原始 JDBC 操作数据库的步骤

JDBC 操作数据库需固定 5 步，视频中以 “查询所有用户” 为例演示，步骤如下：

1. **注册驱动**：指定使用的数据库驱动类（例：MySQL 的驱动类`com.mysql.cj.jdbc.Driver`），告知 JDBC 要操作的数据库类型。
2. **获取连接（Connection）**：通过数据库连接 URL（例：`jdbc:mysql://localhost:3306/db_name`）、用户名（root）、密码，建立 Java 程序与数据库的连接。
3. **创建 Statement 对象**：通过 Connection 对象创建 Statement（或 PreparedStatement），用于执行 SQL 语句（例：`select * from user`）。
4. 解析结果集（ResultSet）
   - 执行查询 SQL 后，返回 ResultSet 对象，需**逐个字段解析**（例：`rs.getInt("id")`、`rs.getString("name")`），并手动封装为 Java 实体类（如 User），再存入集合（如 List<User>）。
   - 弊端：若表有 30 个字段，需重复写 30 行解析代码，冗余且繁琐。
5. **释放资源**：手动关闭 ResultSet、Statement、Connection，避免资源泄漏。

三、原始 JDBC 的 3 大核心问题

这是 MyBatis 框架诞生的直接原因，具体问题如下：

|   问题类型   |                           具体表现                           |
| :----------: | :----------------------------------------------------------: |
|  硬编码问题  | 数据库连接信息（URL、用户名、密码）、驱动类名写死在 Java 代码中。若切换环境（开发→测试→生产），需修改代码、重新编译打包，维护成本高。 |
| 结果解析繁琐 | 需手动解析 ResultSet 的每个字段，手动封装为实体类，代码冗余、易出错（字段名与方法不匹配会导致 bug）。 |
|   资源浪费   | 每次执行 SQL 都需 “创建连接→执行 SQL→关闭连接”，频繁创建 / 销毁 Connection，占用系统资源，降低性能。 |

四、MyBatis 如何解决 JDBC 的问题

MyBatis 通过 “配置化”“自动化”“连接池” 三大手段，彻底简化 JDBC 开发，对比如下：

| JDBC 的问题  |                      MyBatis 的解决方案                      |
| :----------: | :----------------------------------------------------------: |
|  硬编码问题  | 将数据库连接信息（URL、用户名、密码）配置在`application.properties`文件中，修改时无需改代码，直接改配置文件即可。 |
| 结果解析繁琐 | 自动完成结果封装：只需在 Mapper 接口（或 XML）中指定 “SQL 语句” 和 “返回类型（如 User）”，MyBatis 会自动将 ResultSet 解析为实体类对象，并封装为 List 等集合，无需手动编写解析代码。 |
|   资源浪费   | 集成**数据库连接池**：1. 在`application.properties`中配置`spring.datasource`前缀的连接信息，Spring Boot 底层会自动启用连接池（如 HikariCP）。<br>2. 连接池提前创建一定数量的 Connection，执行 SQL 时从池子里 “借”，执行完后 “还” 回池子里，实现连接复用，避免频繁创建 / 销毁，提升性能。 |

### 第三节 数据库连接池

一、数据库连接池核心概念

数据库连接池是**管理数据库连接对象（Connection）的容器**，类比线程池的设计思想，主要负责连接的分配、复用与回收，避免频繁创建和关闭连接造成的资源浪费。

二、有无连接池的对比

通过对比无连接池和有连接池的客户端执行 SQL 流程，可直观体现连接池的价值：

| 场景           | 执行流程                                                     | 问题 / 优势                                                  |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 无数据库连接池 | 1. 客户端执行 SQL 时，创建新 Connection2. 执行 SQL3. 关闭 Connection 释放资源 | 每次创建 / 关闭连接耗时耗资源，频繁操作会严重降低系统性能    |
| 有数据库连接池 | 1. 程序启动时，连接池初始化一定数量 Connection2. 客户端从池内获取 Connection3. 执行 SQL4. 连接归还池（而非关闭） | 1. 连接可复用，减少资源消耗2. 避免连接闲置未释放导致池内连接耗尽的问题 |

![image-20251114114808985](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251114114808985.png)

*补充机制*：连接池会监测闲置连接，若闲置时间超过预设最大值，会自动回收连接，确保池内连接可用。

 三、数据库连接池的核心优势

1. **资源重用**：连接对象可反复使用，避免频繁创建 / 销毁 Connection 带来的资源开销（Connection 创建是 IO 密集型操作，耗时较长）。
2. **提升系统响应速度**：客户端无需等待创建新连接，直接从池内获取可用连接，减少请求处理的延迟。
3. **避免连接耗尽**：通过闲置连接回收机制，防止个别连接长期占用不释放，导致后续客户端无法获取连接的问题。

四、数据库连接池的标准接口

Sun 公司定义了统一的数据库连接池标准接口 ——**javax.sql.DataSource**，所有主流连接池产品都必须实现该接口，且需实现核心方法`getConnection()`（用于从连接池获取连接）。

该接口的作用是：统一连接池的使用规范，让开发者无需关注不同连接池的实现细节，只需通过`DataSource`接口调用`getConnection()`即可获取连接，降低技术选型的切换成本。

五、主流数据库连接池产品

| 类别     | 产品名称         | 特点与现状                                                   |
| -------- | ---------------- | ------------------------------------------------------------ |
| 传统产品 | C3P0、DBCP       | 早期常用，但性能和功能较落后，**当前项目中极少使用**         |
| 当前主流 | Hikari（追光者） | 1. Spring Boot 默认集成的连接池2. 性能极高（轻量级、启动快、资源占用少） |
| 当前主流 | Druid（德鲁伊）  | 1. 阿里巴巴开源产品2. 功能强大（支持监控、防 SQL 注入等）3. 企业项目中广泛使用 |

 六、Spring Boot 中切换数据库连接池（以 Hikari→Druid 为例）

1. 引入 Druid 起步依赖

在项目的`pom.xml`文件中，添加 Druid 的 Spring Boot 起步依赖（无需额外配置连接池类型，Spring Boot 会自动识别并切换）：

```xml
<!-- Druid数据库连接池起步依赖 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.2.16</version> <!-- 版本号可根据实际需求调整 -->
</dependency>
```

2. 验证切换结果

通过运行单元测试，查看控制台日志：

- 切换前：日志中会出现`HikariDataSource`（Hikari 连接池的实现类）；
- 切换后：日志中会出现`com.alibaba.druid.pool.DruidDataSource`（Druid 连接池的实现类），证明切换成功。



### 第四节 lombok

一、Lombok 的核心作用

解决 Java 实体类（如 MyBatis 中的`User`类）的**代码臃肿问题**：

传统实体类需手动编写`get/set`方法、`toString`、`equals/hashCode`、构造器等 “重复体力活代码”（5 个属性的类约需 60-70 行），Lombok 通过**注解自动生成这些代码**，简化开发、提高效率，同时支持自动生成日志变量（如 Logback 的日志对象）。

二、Lombok 核心注解（高频使用）

| 注解名称              | 功能说明                                                     | 备注                                                         |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `@Getter`             | 为类中所有属性生成`getXXX()`方法                             | 单独使用时需配合`@Setter`实现属性读写                        |
| `@Setter`             | 为类中所有属性生成`setXXX()`方法                             | —                                                            |
| `@ToString`           | 生成 “可读性强” 的`toString()`方法（包含类名、所有属性名及值） | 避免默认`toString()`仅显示内存地址的问题                     |
| `@EqualsAndHashCode`  | 重写`equals()`和`hashCode()`方法（基于类中所有属性判断对象相等性） | 用于集合去重、对象比较等场景                                 |
| `@Data`               | **综合注解**：包含`@Getter`、`@Setter`、`@ToString`、`@EqualsAndHashCode` | 最常用注解，直接替代上述 4 个注解，大幅减少代码量            |
| `@NoArgsConstructor`  | 生成**无参构造器**                                           | MyBatis、Spring 等框架实例化对象时需无参构造，建议配合`@Data`使用 |
| `@AllArgsConstructor` | 生成**全参构造器**（包含类中所有属性的参数）                 | 需注意：`@Data`不包含此注解，若需全参构造需单独添加          |

三、Lombok 使用步骤（Spring Boot 环境）

1. 引入依赖（无需指定版本）

在`pom.xml`中添加 Lombok 依赖，Spring Boot 父工程已统一管理 Lombok 版本：

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <!-- 无需version标签，父工程已管理 -->
</dependency>
```

添加后刷新 Maven，依赖即生效。

2. 给实体类添加注解

以`User`实体类为例，传统代码可简化为：

```java
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;

// 1个@Data替代4个注解，配合构造器注解
@Data
@NoArgsConstructor  // 无参构造
@AllArgsConstructor // 全参构造
public class User {
    private Integer id;
    private String username;
    private String password;
    private String email;
    private Integer age;
    // 无需手动写get/set、toString、构造器等
}
```

3. 验证功能（单元测试）

- 调用`user.getUsername()`、`user.setAge(20)`等方法，IDE 可正常识别（Lombok 自动生成）；
- 运行 MyBatis 查询测试，实体类可正常封装数据库数据（无参构造生效）。

四、Lombok 原理

Lombok 并非 “运行时动态生成代码”，而是在**编译期**完成工作：

1. 编译器编译 Java 文件时，扫描类上的 Lombok 注解；
2. 根据注解类型（如`@Data`、`@NoArgsConstructor`）自动生成对应方法（`get/set`、构造器等）；
3. 最终生成的`.class`字节码文件中，包含所有自动生成的方法（可在`target/classes`目录下查看编译后的文件）。



### 第五节 mybatis基本操作

#### 5.1 预编译SQL

一、MyBatis 日志配置（查看 SQL 执行详情）

为直观查看 MyBatis 底层执行的 SQL 语句及结果，需在`application.properties`配置文件中开启日志并指定输出到控制台，步骤如下：

1. **配置项规则**：无需记忆完整配置名，通过关键字联想（如`mybatis`+`log`），IDEA 会自动提示相关配置；

2. 核心配置代码

   ```properties
   # MyBatis日志配置：输出到控制台
   mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
   ```

3. **日志作用**：开启后可查看执行的 SQL 语句、参数替换过程及影响行数（如`update 0`表示无数据被删除）。

二、预编译 SQL（`#{}占位符`核心特性）

日志中显示的`delete from emp where id = ?`即为**预编译 SQL**，`?`是参数占位符，最终执行时会用实际参数（如`16`）替换占位符。其核心优势体现在**性能**和**安全性**两方面：

1. 性能优势：减少 SQL 编译次数

需先理解 MySQL 执行 SQL 的完整流程：

```
SQL语句发送 → 语法检查/解析 → 优化器优化 → 编译为可执行函数 → 执行 → 缓存编译结果
```

- **非预编译 SQL**（如`delete from emp where id=1`、`id=2`）：每条 SQL 因参数不同，需重复执行 “检查 - 优化 - 编译” 流程，即使逻辑一致也需编译多次；
- **预编译 SQL**（`delete from emp where id=?`）：仅首次执行时编译，后续相同逻辑的 SQL 直接复用缓存中的编译结果，仅替换参数即可，大幅减少重复操作（如删除 100 条数据仅编译 1 次）。

2. 安全优势：防止 SQL 注入

（1）什么是 SQL 注入：通过恶意构造输入数据，修改服务器端预定义的 SQL 语句逻辑，实现未授权访问（如无需正确账号密码即可登录系统）。

**示例场景**：登录功能中，若用字符串拼接 SQL（如`select count(*) from emp where username='${name}' and password='${pwd}'`），恶意输入密码：

```sql
' or '1'='1
```

最终拼接的 SQL 会变为：

```sql
select count(*) from emp where username='任意值' and password='' or '1'='1'
```

因`'1'='1`永远为`true`，条件整体成立，导致登录成功。

（2）预编译 SQL 如何防注入？

预编译 SQL 中，`?`占位符会将输入的**整个字符串作为参数传递**（而非直接拼接进 SQL），即使输入恶意语句（如`' or '1'='1`），也会被当作密码的一部分处理，无法修改 SQL 逻辑，从而彻底避免注入风险。

三、MyBatis 两种占位符区别（`#{} vs ${}`）

MyBatis 支持`#{}和${}`两种占位符，二者功能差异极大，开发中需根据场景选择：

|     对比     |               `#{}（推荐，预编译）`                |                 `${}（直接拼接，非预编译）`                  |
| :----------: | :------------------------------------------------: | :----------------------------------------------------------: |
| 底层处理方式 |             替换为`?`，生成预编译 SQL              |                 直接将参数值拼接进 SQL 语句                  |
|     性能     |          首次编译后缓存，后续复用，性能高          |                 每条 SQL 需重新编译，性能低                  |
|    安全性    |             参数整体传递，防 SQL 注入              |               支持恶意拼接，存在 SQL 注入风险                |
|   使用场景   | **参数传递**（如删除 / 查询的 ID、登录的账号密码） | **动态表名 / 字段名**（如`select * from ${tableName}`，场景极少） |
|   示例结果   |            `delete from emp where id=?`            |                `delete from emp where id=16`                 |

**开发原则**：除 “动态表名 / 字段名” 场景外，所有参数传递均使用`#{}`，避免`${}`带来的安全风险。

#### 5.2 主键返回

一、主键返回的定义与业务必要性

1. 定义

主键返回是指在使用 MyBatis 完成**数据新增操作**后，通过特定配置，将数据库自动生成的主键值（如自增 ID）回显到对应的实体类对象中，供后续业务使用的功能。

**核心业务场景（为什么需要主键返回）**

主要用于**多表关联插入**场景，典型案例为 “苍穹外卖” 的 “套餐 - 菜品” 多对多关系：

- 业务逻辑：添加套餐时，需先插入 “套餐基本信息” 到`setmeal`表，再插入 “套餐与菜品的关联关系” 到中间表`setmeal_dish`；
- 依赖关系：中间表`setmeal_dish`需存储 “当前新增套餐的主键（setmeal_id）” 和 “菜品主键（dish_id）”，因此必须先获取第一步插入的套餐主键，才能完成第二步关联数据插入；
- 结论：无主键返回功能时，多表关联插入无法完成，需通过配置主动获取新增数据的主键。

二、实现主键返回的配置方法（注解方式）

通过在 Mapper 接口的新增方法上添加`@Options`注解，声明 2 个核心属性，即可开启主键返回功能，具体步骤如下：

1. 核心注解与属性说明

| 注解       | 属性名             | 取值 / 配置                  | 作用说明                                                     |
| ---------- | ------------------ | ---------------------------- | ------------------------------------------------------------ |
| `@Options` | `useGeneratedKeys` | `true`                       | 告诉 MyBatis：需要获取数据库自动生成的主键（开启主键返回功能） |
| `@Options` | `keyProperty`      | 实体类主键属性名（如`"id"`） | 指定将获取到的主键值，封装到哪个实体类属性中（如`emp.id`）   |

2. 完整代码示例

```java
// Mapper接口中的新增方法（含主键返回配置）
@Insert("insert into emp(name, age, dept_id) values(#{name}, #{age}, #{deptId})")
// 关键配置：开启主键返回，并指定封装到emp的id属性
@Options(useGeneratedKeys = true, keyProperty = "id") 
void insert(Emp emp);
```



#### 5.3 根据主键ID查询

一、需求背景与核心目标

- **业务场景**：员工列表页点击 “编辑” 按钮时，需根据员工 ID 查询该员工的完整信息，回显到表单中供修改（后续修改功能已前置实现，本步骤聚焦 “查询回显”）。
- **技术目标**：通过 MyBatis 实现 “根据 ID 单条查询”，并解决 “数据库字段名与实体类属性名不一致导致的数据封装失败” 问题。

二、基础实现：根据 ID 查询的核心步骤

1 编写 SQL 语句（核心逻辑）

根据 ID 查询单条数据，因 ID 是主键，结果唯一。

```sql
select * from emp where id = 20; -- 20 为测试用固定 ID，实际需动态传参
```

2. 定义 Mapper 接口方法（关键配置）

在 `EmpMapper` 接口中定义查询方法，核心关注**返回值类型**、**参数传递**与**注解配置**：

- **返回值类型**：因查询结果是单条员工数据，无需用集合封装，直接返回 `Emp` 实体类对象。
- **方法名**：遵循语义化命名（如 `getById`，表示 “根据 ID 获取”）。
- **注解**：用 `@Select` 注解标识该方法为查询操作，并指定 SQL 语句。
- **动态传参**：方法形参接收 `Integer id`（需查询的员工 ID），SQL 中用 `${id}` 动态获取参数值。

```java
public interface EmpMapper {
    // 根据 ID 查询员工
    @Select("select * from emp where id = ${id}")
    Emp getById(Integer id);
}
```

3. 编写测试代码（验证结果）

在测试类中调用 `EmpMapper` 的 `getById` 方法，传递测试 ID（如 20），打印返回的 `Emp` 对象，验证数据是否正常封装。

测试代码示例：

```java
@SpringBootTest
public class EmpMapperTest {
    @Autowired
    private EmpMapper empMapper;

    @Test
    public void testgetById() {
        // 调用方法查询 ID 为 20 的员工
        Emp emp = empMapper.getById(20);
        // 打印结果
        System.out.println(emp);
    }
}
```

三、核心问题：字段名与属性名不一致的解决方案

测试中发现：`id`、`username`、`password` 等字段能正常封装，但 `dept_id`（数据库字段）、`create_time`（数据库字段）、`update_time`（数据库字段）对应实体类的 `deptId`、`createTime`、`updateTime`（驼峰命名属性）却为 `null`。

**根本原因**：MyBatis 默认仅在 “数据库字段名与实体类属性名完全一致” 时，才会自动封装数据；若不一致（如下划线 vs 驼峰），则无法匹配。

方案 1：给数据库字段起别名

在 SQL 中为不一致的字段起 “与实体类属性名一致的别名”，让 MyBatis 能自动匹配封装。

- 适用场景：少量字段不一致，临时快速解决。
- 注意：需显式列出所有字段（不能用 `select *`），避免遗漏或别名错误。

```java
@Select("select id, username, password, name, gender, image, job, " +
        "dept_id as deptId, create_time as createTime, update_time as updateTime " +
        "from emp where id = ${id}")
Emp getById(Integer id);
```

方案 2：手动结果映射（@Results + @Result）

通过 MyBatis 提供的 `@Results` 和 `@Result` 注解，手动指定 “数据库字段名（column）” 与 “实体类属性名（property）” 的映射关系。

- 适用场景：字段名与属性名规则不统一（非下划线 - 驼峰对应），需精准匹配。
- 缺点：代码臃肿，若多个方法需映射，重复工作量大，一般不推荐。

```java
// @Results：批量配置结果映射；@Result：单个字段-属性映射
@Results({
    @Result(column = "dept_id", property = "deptId"),   // 字段 dept_id → 属性 deptId
    @Result(column = "create_time", property = "createTime"), // 字段 create_time → 属性 createTime
    @Result(column = "update_time", property = "updateTime")  // 字段 update_time → 属性 updateTime
})
@Select("select * from emp where id = ${id}")
Emp getById(Integer id);
```

方案 3：开启驼峰命名自动映射（全局最优解）

MyBatis 提供 “驼峰命名自动映射开关”，开启后可自动将数据库 “下划线分隔字段”（如 `dept_id`）映射到实体类 “驼峰命名属性”（如 `deptId`），无需手动配置。

- **前提条件**：严格遵循命名规范 —— 数据库字段用 `下划线分隔`（如 `create_time`），实体类属性用 `驼峰命名`（如 `createTime`）。
- **优点**：全局生效，一劳永逸，无需在每个方法中重复处理，是企业开发首选方案。

```properties
# 开启 MyBatis 驼峰命名自动映射（默认关闭）
mybatis.configuration.map-underscore-to-camel-case=true
```

- 配置后效果：无需修改 Mapper 接口方法，直接使用最初的 `select * from emp where id = ${id}` 即可，MyBatis 会自动完成 `dept_id→deptId`、`create_time→createTime` 等映射。

![image-20251117121420463](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251117121420463.png)



#### 5.4 模糊匹配

一、业务需求与 SQL 编写逻辑

基于员工列表查询的页面原型和需求描述，明确查询规则后编写 SQL，是实现条件查询的基础，核心规则与 SQL 结构如下：

| 查询条件       | 匹配规则                   | SQL 实现方式                             |
| -------------- | -------------------------- | ---------------------------------------- |
| 姓名（name）   | 模糊匹配（含关键字即可）   | `name LIKE CONCAT('%', #{name}, '%')`    |
| 性别（gender） | 精准匹配（男 = 1，女 = 2） | `gender = #{gender}`                     |
| 入职时间       | 范围查询（开始 - 结束）    | `entry_date BETWEEN #{begin} AND #{end}` |
| 排序要求       | 按修改时间倒序             | `ORDER BY update_time DESC`              |

**关键说明：**

1. **模糊匹配的实现**：需求明确 “姓名支持模糊匹配”，需在关键字前后加 `%`（匹配任意字符），但直接写 `'%#{name}%'` 会导致预编译失效，需用 MySQL 字符串拼接函数 `CONCAT()` 解决；
2. **多条件关系**：所有查询条件为 “且” 关系，需用 `AND` 连接；
3. **分页暂不考虑**：视频中明确分页在后续步骤实现，当前仅关注条件组装与排序。

二、MyBatis 接口定义与参数传递

在 Mapper 接口中定义查询方法，需重点关注**返回值类型**和**参数传递方式**，确保与业务需求匹配：

1. 返回值类型

条件查询可能返回多条员工记录，因此返回值需用 **`List<Emp>`**（`Emp` 为员工实体类），而非单个 `Emp` 对象（单个对象仅适用于 “根据 ID 查单个员工”）。

2. 参数传递方式

本次查询需传递 4 个参数（姓名 `name`、性别 `gender`、入职开始时间 `begin`、入职结束时间 `end`），因 `Emp` 实体类中仅含单个 `entry_date`（无法存储 “开始 / 结束” 两个时间），故直接传递单个参数（而非封装实体），参数类型如下：

- 姓名：`String name`（文本类型）；
- 性别：`Short gender`（数据库中性别用 1/2 存储，Short 更节省空间）；
- 入职时间：`LocalDate begin` / `LocalDate end`（仅需 “年月日”，`LocalDate` 比 `Date` 更贴合业务）。

三、模糊查询的两种实现方式

模糊查询需在关键字前后加 `%`，但 MyBatis 中 `#{}`（预编译占位符）不能直接放在引号内，因此有两种实现方案，需关注**安全性**和**性能差异**：

| 实现方式                  | 语法示例                              | 原理                                                     | 优缺点对比                                                   |
| ------------------------- | ------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 1. `$` 符号（字符串拼接） | `name LIKE '%${name}%'`               | `$` 直接将参数值拼接到 SQL 中，不生成预编译语句          | 优点：写法简单；缺点：**存在 SQL 注入风险**，且 SQL 无法复用（性能差），不推荐 |
| 2. `CONCAT()` 函数 + `#`  | `name LIKE CONCAT('%', #{name}, '%')` | 用 MySQL 函数拼接 `%` 和参数，`#{}` 生成预编译占位符 `?` | 优点：**无 SQL 注入风险**，SQL 可预编译复用（性能优），推荐使用 |

核心原理：

- `#{}`：MyBatis 会将其替换为 `?`，参数值通过 JDBC 的 `setParameter()` 传入，避免 SQL 注入，是安全的预编译方式；
- `${}`：仅做字符串拼接，参数值直接嵌入 SQL，若参数为恶意值（如 `' OR '1'='1`），会导致 SQL 逻辑被篡改（注入）。



#### 5.5 XML映射文件

一、XML 映射文件的定义规范

| 规范序号 |          规范内容          |                           关键说明                           |
| :------: | :------------------------: | :----------------------------------------------------------: |
|    1     |        **同包同名**        | 文件名需与 Mapper 接口名完全一致（如接口为`EmpMapper`，文件名为`EmpMapper.xml`)<br/>文件路径需与 Mapper 接口所在包路径一致（Maven 项目中，接口在`java/com/itheima/mapper`，XML 需放在`resources/com/itheima/mapper` |
|    2     |   **namespace 属性匹配**   | XML 根标签`<mapper>`的`namespace`属性值，需与 Mapper 接口的**全类名**完全一致（如`com.itheima.mapper.EmpMapper`） |
|    3     | **SQL 的 id 与方法名匹配** | XML 中配置的 SQL 标签（如`<select>`、`<insert>`）的`id`属性，需与 Mapper 接口中的**方法名**完全一致（如接口方法`list()`，SQL 的`id="list"`） |

![image-20251114161009724](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251114161009724.png)

```xml
<select id="list" resultType="com.itheima.pojo.Emp">
    select * from emp where name like concat('%',#{name},'%') and gender = #{gender}
</select>
```



二、规范设计的核心原理

MyBatis 框架通过 XML 映射文件的 3 点规范，实现 “Mapper 接口方法→SQL 语句” 的自动关联，原理如下：

当调用 Mapper 接口的方法（如`empMapper.list()`）时，MyBatis 会：

1. 根据接口的全类名，匹配`namespace`属性相同的 XML 映射文件；
2. 在匹配的 XML 文件中，根据接口方法名，找到`id`属性相同的 SQL 语句；
3. 执行找到的 SQL 语句，并根据`resultType`封装返回结果（查询场景）。若不遵守规范，MyBatis 无法建立接口与 SQL 的关联，将导致方法调用失败。

四、开发工具优化：MyBatis X 插件

为提升 XML 映射文件与 Mapper 接口的开发效率，推荐在 IDEA 中安装**MyBatis X 插件**，核心功能如下：

- **快速跳转**：接口方法前、XML SQL 标签前会显示 “小鸟图标”，点击可直接跳转到对应的 XML SQL 或接口方法，避免手动查找路径；
- **语法校验**：自动检测`resultType`与接口方法返回值的匹配性，提前发现类型不匹配问题（注：多模块项目中可能出现误报，不影响代码运行，可移除无关模块解决）。

五、MyBatis SQL 配置方式选型

MyBatis 提供 “注解配置” 和 “XML 配置” 两种 SQL 配置方式，官方推荐选型原则如下：

| 配置方式                | 适用场景                                            | 优势                                                         | 劣势                                               |
| ----------------------- | --------------------------------------------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| 注解配置（如`@Select`） | 简单的增删改查（CRUD）操作                          | 代码简洁，SQL 与接口方法直接绑定，无需额外 XML 文件          | 复杂 SQL（如多表关联、动态条件）可读性差，维护困难 |
| XML 配置                | 复杂 SQL 操作（如多表连接、动态 SQL、SQL 片段复用） | SQL 与 Java 代码分离，格式清晰，支持动态 SQL、SQL 片段等高级功能 | 需额外创建 XML 文件，配置需遵守规范                |



### 第六节 Mybatis动态SQL

- 动态 SQL 是指**随用户输入或外部条件变化而动态拼接的 SQL 语句**，并非固定不变。例如员工条件查询中，用户可能只输入 “姓名”、只输入 “性别”、输入多个条件或不输入条件，需 SQL 语句根据实际输入动态调整。

- **解决的核心问题**：传统固定 SQL 的缺陷 —— 若查询条件未指定（参数为`null`），固定 SQL 仍会拼接该条件（如`and gender = null`），导致查询不到数据或 SQL 语法错误。动态 SQL 可通过标签判断条件是否成立，只拼接有效条件，避免无效查询。

#### 6.1 if 标签

一\ if标签

1. 标签作用

用于**条件判断**：当`test`属性中的条件成立时，才拼接标签内部的 SQL 片段；条件不成立则不拼接。

2. 语法格式

```xml
<if test="条件表达式">
    -- 满足条件时拼接的SQL片段（如 and 字段名 = #{参数名}）
</if>
```

3. 关键细节

- `test`属性
  - 用于指定判断条件，表达式中直接使用参数名（无需加`#{}或${}`），例如判断 “姓名参数不为 null”：`test="name != null"`。
  - 支持多条件组合，如判断 “入职开始日期和结束日期都不为 null”：`test="beginDate != null and endDate != null"`。
- SQL 片段拼接规则若多个`if`标签条件都成立，会按标签顺序依次拼接 SQL 片段，需注意片段开头的逻辑运算符（如`and`）—— 需确保拼接后整体 SQL 语法正确

二、辅助标签：where 标签（解决 if 标签的语法问题）

1. 单独使用`if`标签可能出现**SQL 语法错误**，例如：

- 场景：只查询 “性别”（姓名参数为`null`），`if`标签仅拼接`and gender = #{gender}`，最终生成的 SQL 为：`select * from emp where and gender = 1`（多了一个`and`，语法错误）。
- 场景：所有条件都不成立，`if`标签未拼接任何片段，最终生成的 SQL 为：`select * from emp where`（多了一个`where`，语法错误）。

2. 标签作用

- 动态生成`where`关键字

  - 若`where`标签内部有至少一个`if`标签条件成立（即有有效 SQL 片段），则自动生成`where`关键字；

  - 若所有`if`标签条件都不成立（无有效片段），则不生成`where`关键字（避免`select * from emp where`的错误）。

- 自动去除多余逻辑运算符:若内部 SQL 片段开头有多余的`and`或`or`（如上述 “多了一个`and`” 的场景），自动剔除，确保最终 SQL 语法正确。

3. 语法格式（与 if 标签配合使用）

```xml
<select id="selectEmpByCondition" resultType="com.example.pojo.Emp">
    select * from emp
    <where>
        <if test="name != null">
            and name like concat('%', #{name}, '%')  -- 模糊查询姓名
        </if>
        <if test="gender != null">
            and gender = #{gender}  -- 精确查询性别
        </if>
        <if test="beginDate != null and endDate != null">
            and entry_date between #{beginDate} and #{endDate}  -- 范围查询入职日期
        </if>
    </where>
</select>
```



#### 6.2 foreach标签

一、foreach 标签核心作用

`foreach`是 MyBatis 动态 SQL 的核心标签之一，主要用于**循环遍历集合或数组**，将遍历的元素动态拼接成 SQL 片段，解决 “批量操作” 场景的 SQL 动态生成问题（如批量删除、批量新增、批量查询等）。

视频中以 “批量删除员工” 为实例 —— 前端勾选多个员工 ID 传递到后端，后端通过`foreach`遍历 ID 集合，动态生成`DELETE FROM emp WHERE id IN (13,14,15)`这类 SQL 语句。

二、foreach 标签 5 个关键属性

|    属性名    |                           作用描述                           |                         实例中的配置                         |
| :----------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| `collection` | 指定要遍历的**集合 / 数组名称**，需与 Mapper 接口方法的形参名保持一致 | 遍历员工 ID 集合，配置为`collection="ids"`（形参名为`List<Integer> ids`） |
|    `item`    | 定义遍历过程中**单个元素的变量名**（类似循环中的`for (int id : ids)`的`id`） |              单个 ID 变量名，配置为`item="id"`               |
| `separator`  | 指定遍历元素之间的**分隔符**（如逗号、`AND`等，避免元素拼接时语法错误） |        批量删除需用逗号分隔 ID，配置为`separator=","`        |
|    `open`    |       指定循环**开始前拼接的 SQL 片段**（如左括号`(`）       |               生成`IN (`前缀，配置为`open="("`               |
|   `close`    |       指定循环**结束后拼接的 SQL 片段**（如右括号`)`）       |                生成`)`后缀，配置为`close=")"`                |

三、批量删除实战步骤

1. 需求与基础 SQL 分析

- 需求：删除 ID 为 13、14、15 的员工，静态 SQL 为`DELETE FROM emp WHERE id IN (13,14,15)`；
- 问题：ID 列表是动态传递的（前端勾选不同员工），需通过`foreach`遍历集合生成`IN (id1,id2,...)`片段。

2. 编写 Mapper 接口方法

在`EmpMapper`接口中定义批量删除方法，用`List<Integer>`封装动态 ID 集合：

```java
// 批量删除员工：形参名ids需与foreach的collection属性一致
void deleteBatch(@Param("ids") List<Integer> ids); 
// 注：若形参未用@Param指定名称，集合类型默认用"list"，数组类型默认用"array"
```

3. 配置 XML 映射文件（核心）

在`EmpMapper.xml`中通过`foreach`动态生成 SQL，拼接`IN (id1,id2,...)`片段：

```xml
<delete id="deleteBatch">
    DELETE FROM emp 
    WHERE id IN 
    <!-- foreach循环遍历ids集合，生成(13,14,15) -->
    <foreach collection="ids" item="id" separator="," open="(" close=")">
        #{id} //代表每次遍历出来的元素
        <!-- 引用item定义的变量，MyBatis会自动预编译，防止SQL注入 -->
    </foreach>
</delete>
```

四、关键注意事项

1. **collection 属性的取值规则**：

   - 若方法形参用`@Param("name")`指定名称，`collection`需与`name`一致（如`@Param("ids")`对应`collection="ids"`）；
   - 若未用`@Param`，集合类型（`List`）默认用`collection="list"`，数组类型（`int[]`）默认用`collection="array"`。

2. **预编译安全**：

   视频中强调用`#{id}`（而非`${id}`）引用`item`变量 ——MyBatis 会将`#{id}`解析为预编译占位符`?`，避免 SQL 注入风险（若用`${id}`会直接拼接字符串，存在注入漏洞）。

3. **适用场景扩展**：

   `foreach`不仅用于批量删除，还可用于批量新增（如`INSERT INTO emp (id,name) VALUES (?,?),(?,?)`）、批量查询（如`WHERE dept_id IN (?,?)`）等场景，核心逻辑均为 “遍历集合拼接 SQL 片段”。



#### 6.3 sql和include标签

一、`<sql>`与`<include>`标签

1. 标签引入背景

在 MyBatis 的 XML 映射文件中，若存在大量重复的 SQL 片段（如多语句共用的`SELECT`字段列表、`FROM`表名等），会导致**代码复用性差**：后期修改表名 / 字段时，需在所有引用处逐一修改，效率低且易出错。

2. 标签功能与使用规范

|    标签     |                  功能描述                  |                           核心属性                           |
| :---------: | :----------------------------------------: | :----------------------------------------------------------: |
|   `<sql>`   | 抽取公共 SQL 片段，生成可复用的 “SQL 模板” |   `id`：给片段分配唯一标识（必填），如`id="common_select"`   |
| `<include>` |  在目标位置引用`<sql>`标签定义的公共片段   | `refid`：指定要引用的`<sql>`标签的`id`（必填），如`refid="common_select"` |

- `refid`的值必须与目标`<sql>`标签的`id`完全一致；标签可自闭合（`<include refid="xxx"/>`）

3. 实操示例

步骤 1：用`<sql>`抽取公共片段

```xml
<!-- 抽取“查询emp表所有字段”的公共SQL片段，id为common_select -->
<sql id="common_select">
    SELECT id, name, age, dept_id, hire_date FROM emp
</sql>
```

步骤 2：用`<include>`引用片段

```xml
<!-- 动态条件查询：引用公共片段 -->
<select id="selectEmpByCondition" resultType="Emp">
    <include refid="common_select"/> <!-- 引用公共SQL片段 -->
    <where>
        <if test="name != null and name != ''">
            AND name LIKE CONCAT('%', #{name}, '%')
        </if>
        <if test="age != null">
            AND age = #{age}
        </if>
    </where>
</select>

<!-- 根据ID查询：复用同一公共片段 -->
<select id="selectEmpById" resultType="Emp">
    <include refid="common_select"/> <!-- 直接引用，无需重复写字段列表 -->
    WHERE id = #{id}
</select>
```



### 第七节 开发规范

1. 前后端分离开发模式

- 架构流程
  1. 前端工程部署在 Nginx 服务器，后端工程部署在 Tomcat 服务器（Spring Boot 内嵌 Tomcat，无需单独配置）。
  2. 交互逻辑：前端发起请求 → 后端接口处理 → 操作数据库 → 后端返回响应 → 前端渲染页面。
- **核心约定**：前后端通过**接口文档**对接，文档由后端人员编写（当前阶段仅需 “阅读文档”），格式可多样（在线 YApi、离线 Markdown/PDF）。

2. 接口文档核心内容

接口文档需明确每个接口的 “输入输出”，以 “部门列表查询” 为例，包含：

- 请求路径（如`/depts`）、请求方式（如 GET）、接口描述。
- 请求参数（如无参数则说明）。
- 响应数据：格式（JSON）、字段含义（如`code`= 响应码、`message`= 提示信息、`data`= 返回数据）、响应案例。

3. RESTful 风格接口（核心规范）

RESTful 是**前后端交互的主流接口风格**，核心是 **“用 URL 定位资源，用 HTTP 动词描述操作”**：

（1）两大核心要点

1. URL 定位资源：URL 仅描述 “操作的资源”，不包含操作类型（如查询、删除），资源名用复数形式（如`/users`,`/depts`）。

   - 示例：`/depts/1`表示 “ID 为 1 的部门” 这一资源，不体现 “查询” 或 “删除”。

2. HTTP 动词描述操作：通过 HTTP 请求方式对应 CRUD 操作，彻底替代传统 URL 中的 “getById”“delete” 等关键词：

   | HTTP 请求方式 | 对应操作 | 示例（操作 “部门” 资源）                                     |
   | ------------- | -------- | ------------------------------------------------------------ |
   | GET           | 查询     | `GET /depts`（查询所有部门）、`GET /depts/1`（查询 ID=1 的部门） |
   | POST          | 新增     | `POST /depts`（新增部门，请求体带部门信息）                  |
   | PUT           | 修改     | `PUT /depts/1`（修改 ID=1 的部门，请求体带更新信息）         |
   | DELETE        | 删除     | `DELETE /depts/1`（删除 ID=1 的部门）                        |

（2）注意事项

- REST 是 “约定而非规定”：可根据实际需求调整（如部分场景用 POST 代替 PUT），但需团队统一。
- 资源名用复数：如`/depts`（而非`/dept`），表示 “操作部门这类资源”，而非单个资源。

4. 统一响应结果封装

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251117123815726.png" alt="image-20251117123815726" style="zoom:50%;" />

所有接口返回统一格式的 JSON 数据，便于前端统一解析，封装为`Result`类（放在`pojo`包下）：

- 核心属性
  - `code`：响应码（约定 1 = 成功，0 = 失败，与前端协商一致）。
  - `message`：提示信息（如 “删除成功”“参数错误”）。
  - `data`：返回的数据（如查询到的部门列表、单个员工信息，无数据时为`null`）。
- **便捷方法**：提供静态方法（如`success()`、`success(T data)`、`error(String message)`），简化响应代码（如成功时直接`return Result.success(deptList)`）。

四、功能开发流程

后续开发每个接口均遵循以下流程，确保逻辑清晰：

1. **明确需求**：查看产品经理提供的页面原型，确认功能细节（如 “员工分页查询” 需支持页码、每页条数）。
2. **阅读接口文档**：明确接口的请求路径、方式、参数、响应格式，梳理开发思路。
3. **开发接口**：按 “Mapper 层→Service 层→Controller 层” 的顺序开发（先写 SQL 操作，再处理业务逻辑，最后接收请求）。
4. **测试接口**：用 Postman 工具测试接口（验证参数合法性、响应正确性）。
5. **前后端联调**：与前端配合，确认接口能正常对接，页面功能正常。

![image-20251117131530585](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251117131530585.png)

- Controller 层是 “前端请求入口”，负责接收请求、调用 Service、封装响应结果
- **核心注解使用**
  - `@RestController`：组合注解（包含`@Controller`+`@ResponseBody`），自动将返回的 Java 对象转为 JSON 格式响应给前端；
  - `@GetMapping("/depts")`：限定请求方式为 GET，指定请求路径为`/depts`（替代`@RequestMapping(value="/depts", method=RequestMethod.GET)`，更简洁）；
  - `@Autowired`：依赖注入 Service 接口对象（面向接口编程，而非具体实现类，降低耦合）。
- Service 层是 “业务逻辑中转站”，负责调用 Mapper 层获取数据，无复杂业务逻辑时仅做 “透传”
  - 注入`DeptMapper`时，需保证`DeptMapper`接口已被 MyBatis 扫描（通过`@Mapper`注解或 SpringBoot 启动类的`@MapperScan`注解）。
- Mapper 层（MyBatis 数据访问层）直接操作数据库，负责执行 SQL 语句

![image-20251117173647909](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251117173647909.png)



### 第八节 文件上传

#### 8.1 文件上传

1. 定义

将本地的图片、视频、音频、文档等文件，通过前端操作上传到服务器，供其他用户浏览或下载的过程，称为文件上传。

2. 典型应用场景

- 社交场景：发微博、微信朋友圈时上传图片 / 视频
- 业务场景：新增员工时上传员工头像、表单提交时附加附件等

二、前端实现：文件上传三要素

前端需通过 `form` 表单实现文件上传，必须满足以下三个核心条件

|      要素      |                 具体要求                 |                         原因 / 作用                          |
| :------------: | :--------------------------------------: | :----------------------------------------------------------: |
| 文件选择表单项 |  表单中必须包含 `type="file"` 的表单项   | 用于在页面生成 “选择文件 / 浏览” 按钮，点击后可弹窗选择本地文件 |
|  表单提交方式  |         必须使用 `method="post"`         | 文件通常体积较大，`post` 方式支持大体积数据传输（`get` 方式有 URL 长度限制） |
|  表单编码格式  | 必须设置 `enctype="multipart/form-data"` | 普通默认编码（`application/x-www-form-urlencoded`）无法传输二进制大文件，该格式会将表单数据按 “多部分” 拆分传输，确保文件内容完整提交 |

![image-20251117212438374](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251117212438374.png)



**关键对比：两种编码格式的差异**

1. **默认编码（`application/x-www-form-urlencoded`）**
   - 仅提交文件的 “文件名”，不包含文件内容，无法实现真正的文件上传。
   - 提交数据格式：`username=tom&age=123&image=test.txt`（仅文件名）。
2. **`multipart/form-data` 编码**
   - 表单数据按 “表单项拆分” 为多个部分，每个部分用浏览器自动生成的 `boundary`（分隔符）区分。
   - 提交内容：普通表单项（如姓名、年龄）的数值 + 文件的 “文件名 + 完整二进制内容”（文本文件可看到内容，图片 / 视频等二进制文件显示为乱码，但内容完整）。



三、服务端接收：SpringBoot 实现

服务端需通过 Controller 接收前端提交的表单数据（含文件），核心步骤如下：

1. 核心 API：`MultipartFile`

Spring 提供 `MultipartFile` 接口，专门用于接收前端上传的文件，需注意：

- **参数名匹配**：`MultipartFile` 形参名必须与前端 `type="file"` 表单项的 `name` 属性一致（如前端 `name="image"`，后端形参需为 `MultipartFile image`）。
- **参数名不匹配处理**：若名称不一致，可通过 `@RequestParam("前端name值")` 注解绑定（如 `@RequestParam("image") MultipartFile file`）。

2. 普通表单项接收

姓名、年龄等普通表单项的接收方式不变，直接声明对应类型的形参即可（如 `String username`、`Integer age`），Spring 会自动映射表单提交的数值。

3. 临时文件特性

- 前端上传的文件被服务端接收后，会先存储为**临时文件**（默认存放在系统临时目录，如 Windows 的 `C:\Users\XXX\AppData\Local\Temp`）。
- 临时文件的生命周期：仅在当前请求响应周期内存在，请求处理完毕后会被自动删除。因此，服务端需在请求处理过程中，将临时文件 “永久保存” 到目标位置（本地目录或云存储），否则文件会丢失。



#### 8.2 本地存储

一、文件上传本地存储的核心概念

本地存储是指服务端接收前端上传的文件后，将文件**永久保存到服务器本地磁盘目录**（如示例中的`E盘/images`文件夹），而非临时文件（默认请求响应后临时文件会自动删除）。

- 核心场景：需将用户上传的图片、文档等数据持久化，避免请求结束后文件丢失。
- 依赖 API：SpringBoot 中通过`MultipartFile`对象处理上传文件。

二、本地存储基础实现

1. 核心方法：`transferTo()`

`MultipartFile`对象提供`transferTo(File dest)`方法，可直接将接收的文件转存到指定磁盘路径，无需手动处理 IO 流，步骤如下：

1. 确定目标存储目录（如`E:/images/`），复制路径备用；
2. 在 Controller 方法中，通过`MultipartFile`参数接收前端上传的文件；
3. 构造目标文件对象`File`，指定存储路径 + 文件名；
4. 调用`multipartFile.transferTo(file)`完成转存；
5. 处理异常：`transferTo()`方法会抛出`IOException`，需在方法上声明抛出`Exception`或捕获处理。

2. 文件名处理：避免固定命名的问题

- 初始问题：若直接将文件名写死（如01.txt），会导致两个问题：
  1. 文件类型不匹配（如上传图片却存为`.txt`）；
  2. 文件名重复，后上传的文件会覆盖前一个文件（同一目录下文件名唯一）。
- **解决方案**：使用**原始文件名**存储，通过`MultipartFile`的`getOriginalFilename()`方法获取前端上传文件的原始名称（如`1.jpg`），拼接在目标路径后。

三、关键优化：保证文件名唯一性

1. 原始文件名的缺陷

若多个用户上传同名文件（如均为`1.jpg`），仍会出现文件覆盖问题，需生成**唯一文件名**。

2. 唯一文件名生成方案：UUID + 文件后缀

- **UUID 介绍**：通用唯一识别码（Universally Unique Identifier），是 **36 位固定长度的字符串**（含 4 个横杠，如`550e8400-e29b-41d4-a716-446655440000`），全球唯一，可通过`java.util.UUID.randomUUID().toString()`生成。

- 文件后缀获取：从原始文件名中截取最后一个`.`后的部分（如`1.2.3.jpg`的后缀是`.jpg`），通过`lastIndexOf('.')`定位`.`的位置，再用`substring(index)`截取：

  ```java
  // 1. 获取原始文件名
  String originalFilename = multipartFile.getOriginalFilename();
  // 2. 截取文件后缀（如.jpg）
  int index = originalFilename.lastIndexOf('.');
  String suffix = originalFilename.substring(index);
  // 3. 生成唯一文件名（UUID+后缀）
  String uniqueFileName = UUID.randomUUID().toString() + suffix;
  // 4. 构造目标文件
  File destFile = new File("E:/images/" + uniqueFileName);
  ```

四、文件大小限制配置

1. 默认限制问题:SpringBoot 默认限制**单个上传文件最大 1MB**、**单个请求总文件大小最大 10MB**，若上传超过 1MB 的文件（如视频、大图片），会抛出`FileSizeLimitExceededException`。

2. 自定义配置（application.properties）: 通过配置修改大小限制，单位支持`MB`/`KB`：

```properties
# 单个文件最大上传大小（示例：10MB）
spring.servlet.multipart.max-file-size=10MB
# 单个请求总文件大小（示例：100MB，适用于一次上传多个文件的场景）
spring.servlet.multipart.max-request-size=100MB
```

五、本地存储的局限性与替代方案

在企业级项目中，本地存储几乎不被使用，原因如下：

1. **前端无法直接访问**：存储在服务器磁盘的文件（如`E:/images/1.jpg`），浏览器无法通过 URL 直接访问，需额外开发文件访问接口；
2. **磁盘容量有限且扩容困难**：服务器本地磁盘空间固定，大量文件（如海量图片）会占满磁盘，且单服务器扩容成本高；
3. **数据安全性低**：若服务器磁盘损坏，所有存储的文件会永久丢失，无备份机制。

企业中常用以下两种方案解决文件存储问题：

1. **自建分布式文件系统**：如 FastDFS、MinIO，通过集群实现高可用、大容量存储；
2. **云存储服务**：如阿里云 OSS、腾讯云 COS，无需自建服务器，支持高可用、自动扩容，且提供 CDN 加速（下一小节将讲解阿里云 OSS 集成）。

六、`MultipartFile`核心方法汇总

| 方法名                  | 作用                                    | 返回值类型  |
| ----------------------- | --------------------------------------- | ----------- |
| `getOriginalFilename()` | 获取上传文件的原始文件名                | String      |
| `transferTo(File dest)` | 将文件转存到指定磁盘路径                | void        |
| `getSize()`             | 获取文件大小（单位：字节）              | long        |
| `getBytes()`            | 获取文件内容的字节数组                  | byte[]      |
| `getInputStream()`      | 获取文件内容的输入流（用于手动处理 IO） | InputStream |



#### 8.3 阿里云OSS

一、云服务与阿里云基础概念

1. **阿里云定位**：阿里巴巴集团旗下全球领先的云计算公司，国内最大的云服务提供商，提供海量成熟的互联网服务。
2. **“云” 与 “云服务” 定义**
   - **云**：可简单理解为 “互联网”，是服务的承载载体；
   - 云服务：通过互联网对外提供的标准化服务，无需开发者自行开发底层功能，直接调用即可，例如：
     - 基础服务：短信服务、邮件服务、语音服务；
     - 业务服务：视频直播服务、文字识别服务、对象存储服务（OSS）；
   - **核心价值**：降低项目开发难度（无需对接底层资源，如短信服务无需对接三大运营商）、提升开发效率，但需按使用量支付费用。

二、阿里云 OSS 核心认知

1. **OSS 定义与简称**
   - 全称为 “对象存储服务”，简称 OSS（Object Storage Service），其中 “对象” 即指待存储的**文件**（文本、图片、音频、视频等）。
2. **OSS 核心特性**
   - 海量存储：支持大规模文件存储，无需担心本地磁盘容量；
   - 安全可靠：阿里云提供数据保障，避免本地存储的丢失风险；
   - 低成本：按实际使用量计费，适合中小项目及学习场景；
   - 高可用：具备稳定的访问与管理能力，支持快速调取文件。
3. **OSS 在项目中的作用（对比本地存储）**
   - 传统本地存储：前端上传文件→服务端接收→存储到服务器本地磁盘，存在 “磁盘容量有限、数据易丢失、迁移困难” 问题；
   - OSS 存储：前端上传文件→服务端接收→直接转发至 OSS 存储，服务端本地无需保存文件，解决本地存储的痛点，且后续可通过 OSS 直接访问文件。

4. **关键术语：SDK**
   - 全称 “Software Development Kit（软件开发工具包）”，包含使用该云服务所需的**依赖包**（如 Java 的 JAR 包）和**示例代码**，是开发者快速上手的核心工具。



### 第九节 配置文件

#### 9.1 参数配置化

一、核心问题：硬编码参数的弊端

在之前的阿里云 OSS 文件上传功能中，将 4 个关键参数（`endPoint`、`accessKeyId`、`accessKeySecret`、`bucketName`）直接写在 Java 代码中（硬编码），存在两大问题：

1. **修改繁琐**：参数变更时需修改代码→重新编译→重新运行，无法快速生效；
2. **维护困难**：企业级项目中参数分散在多个 Java 类，修改时需定位多个文件，效率低。

二、解决方案：参数配置化思想

核心思路：**将分散的参数集中到 Spring Boot 默认配置文件中管理，通过框架提供的注解注入到代码中**，实现 “配置与代码解耦”。

1. 核心配置文件：application.properties

Spring Boot 默认的配置文件，采用`key=value`格式存储参数，key 需定义为 “有业务含义的命名”（如`aliyun.oss.endPoint`），便于识别和维护。

配置示例（阿里云 OSS 参数）：

```properties
# 阿里云OSS配置
aliyun.oss.endPoint=oss-cn-beijing.aliyuncs.com
aliyun.oss.accessKeyId=LTAI5txxxxxxxxxxxxxxx
aliyun.oss.accessKeySecret=xxxxxxxxxxxxxxxxxxxxxxxxxx
aliyun.oss.bucketName=web-tlias
```

2. 属性注入：@Value 注解

Spring Boot 提供`@Value`注解（需导入 Spring 的`org.springframework.beans.factory.annotation.Value`包），用于将`application.properties`中的参数值注入到 Java 类的成员变量中，无需手动读取配置文件。

注入语法：

```java
// 格式：@Value("${配置文件中的key}")
@Value("${aliyun.oss.endPoint}")
private String endPoint;

@Value("${aliyun.oss.accessKeyId}")
private String accessKeyId;

@Value("${aliyun.oss.accessKeySecret}")
private String accessKeySecret;

@Value("${aliyun.oss.bucketName}")
private String bucketName;
```



三、配置化的优势

1. **集中管理**：所有参数统一在`application.properties`中，修改时只需操作一个文件；
2. **无需编译**：修改配置文件后无需重新编译代码，重启服务即可生效（开发环境可开启热部署进一步简化）；
3. **通用性强**：不仅适用于阿里云 OSS，其他第三方服务（如 MySQL、Redis、短信接口等）的参数均可按此方式配置。



#### 9.2 yml配置文件

一、Spring Boot 支持的配置文件类型

Spring Boot 主要支持两种配置文件，核心信息对比如下：

| 配置文件类型 |              文件名格式              |        语法格式        |                  特点                  |
| :----------: | :----------------------------------: | :--------------------: | :------------------------------------: |
|  Properties  |       `application.properties`       |      `key=value`       | 语法简单，但层级不清晰，复杂配置易冗余 |
|     YAML     | `application.yml`/`application.yaml` | 层级缩进 +`key: value` |      层级清晰、简洁，以数据为中心      |

二、YAML 配置文件的核心语法规则

YAML 语法严格依赖格式，需注意以下 5 点关键规则：

1. **大小写敏感**：`server.port`与`Server.Port`是不同配置项，需严格区分大小写。
2. **值前必须加空格**：`key`与`value`之间用 “冒号 + 空格” 分隔，例如`port: 9000`（**冒号后无空格会报错**）。
3. 缩进表示层级
   - 同层级配置需左对齐，例如`server`下的`port`和`address`需保持相同缩进。
4. **缩进空格数无固定要求**：只要同层级缩进一致即可（通常建议 2 个或 4 个空格，保持统一风格）。
5. **注释规则**：用`#`表示注释

三、YAML 配置文件的常见数据格式

YAML 支持多种数据结构，视频中重点讲解了企业开发中最常用的两种：

1. 定义对象 / Map 集合

用于配置有多个属性的 “实体类” 或键值对集合，语法为 “父 key + 缩进子属性”：

```yaml
# 示例：配置用户对象（属性包含name、age、address）
user:
  name: ZhangSan  # 字符串值直接写，无需加引号
  age: 25         # 数值型值直接写
  address: Beijing
```

- 对应 Map 集合时，`user`为 Map 名，`name`/`age`/`address`为 Map 的 key，后续值为 Map 的 value。

2. 定义数组 / List/Set 集合

用于配置多个同类型元素的集合，语法为 “父 key+`- 元素值`”（`-`后需加空格）：

```yaml
# 示例：配置兴趣爱好数组（包含4个元素）
hobby:
  - java    # 第1个元素
  - c       # 第2个元素
  - game    # 第3个元素
  - sports  # 第4个元素
```

- IDEA 会自动提示当前元素在集合中的位置（如 “hobby [0]” 表示第 1 个元素），便于排查配置。

五、YAML vs Properties/XML 的优势

1. **对比 Properties**：YAML 通过层级缩进解决了 Properties “长 key 冗余” 问题，例如数据库配置中`spring.datasource`无需重复书写，层级更直观。
2. **对比 XML**：XML 需通过`<标签>`嵌套实现层级（如`<server><port>8080</port></server>`），语法臃肿；YAML 仅需缩进，配置更简洁。



#### 9.3 @ConfigurationProperties

 一、注解的核心作用：解决「属性注入繁琐」问题

1. @Value 注解的局限性

在需要注入多个配置项（如阿里云 OSS 的 `endpoint`、`accessKeyId`、`accessKeySecret`、`bucketName`）时，传统方式需在**每个成员变量上单独添加 @Value 注解**，并通过 `${配置项key}` 指定值，存在以下问题：

- 编码冗余：若需注入 7-8 个甚至 20 个属性，需重复编写 @Value 注解；
- 维护成本高：配置项与代码强绑定，修改配置项需同步调整多个注解。

2. @ConfigurationProperties 的优势

通过「批量绑定配置项」的方式，自动将配置文件中符合规则的配置项注入到 Bean 的属性中，无需逐个标注注解，大幅简化代码。

二、@ConfigurationProperties 使用的 3 个核心前提

需满足「配置项与属性名匹配」「Bean 交给容器管理」「指定配置前缀」三个条件，才能实现自动注入：

| 前提条件            | 具体要求                                                     | 实现方式                                                     |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 配置项与属性名一致  | 配置文件中 `key` 的**后缀**需与实体类的属性名完全匹配（支持驼峰命名与横杠分隔的自动转换，如配置文件 `access-key-id` 可匹配属性 `accessKeyId`） | 例：配置文件 `aliyun.oss.endpoint` → 实体类属性 `private String endpoint` |
| 提供 Get/Set 方法   | 实体类需为属性提供 Get/Set 方法（用于 Spring 注入值）        | Lombok 的 `@Data` 注解                                       |
| 类交给 IOC 容器管理 | 实体类需成为 Spring 容器中的 Bean，才能被 Spring 处理属性注入 | 在类上添加 `@Component` 注解（或其他能注册 Bean 的注解，如 `@Configuration`） |

三、注解的关键配置：指定「配置前缀」

1. 为什么需要前缀？

配置文件中多个配置项可能存在**共同前缀**（如阿里云 OSS 所有配置项都以 `aliyun.oss` 开头），通过前缀可精准定位「需要绑定的配置组」，避免不同模块配置项冲突。![image-20251119154156773](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251119154156773.png)

2. 配置方式

在实体类上添加 `@ConfigurationProperties` 注解，并通过 `prefix` 属性指定共同前缀：

```java
import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Data // 自动生成 Get/Set 方法
@Component // 注册为 Spring Bean
// 指定配置项前缀：匹配配置文件中 "aliyun.oss" 开头的配置
@ConfigurationProperties(prefix = "aliyun.oss")
public class AliOssProperties {
    // 配置项：aliyun.oss.endpoint → 属性名 endpoint（完全匹配）
    private String endpoint;
    // 配置项：aliyun.oss.access-key-id → 属性名 accessKeyId（横杠自动转驼峰）
    private String accessKeyId;
    // 配置项：aliyun.oss.access-key-secret → 属性名 accessKeySecret
    private String accessKeySecret;
    // 配置项：aliyun.oss.bucket-name → 属性名 bucketName
    private String bucketName;
}
```

四、优化：引入依赖实现「配置文件提示」

1. 问题：注解使用时的警告

初次使用 `@ConfigurationProperties` 时，IDEA 会提示警告，原因是缺少「配置文件自动提示」的依赖，导致配置 `aliyun.oss` 相关项时无语法提示，易写错 key。

2. 解决方案：引入 `spring-boot-configuration-processor` 依赖

在 `pom.xml` 中添加依赖，Spring 会自动识别被 `@ConfigurationProperties` 标注的 Bean，在配置文件（properties/yml）中输入前缀时，自动提示对应的配置项：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <!-- 仅编译时需要，不打包到运行环境 -->
    <optional>true</optional>
</dependency>
```

- 作用：提供配置项的语法提示，降低配置错误率；
- 性质：**可选依赖**，不引入不影响程序运行，仅影响开发体验。

五、@ConfigurationProperties 与 @Value 注解的对比

两者均用于注入外部配置，但适用场景不同，核心差异如下：

| 对比维度 | @ConfigurationProperties                                     | @Value                                                    |
| -------- | ------------------------------------------------------------ | --------------------------------------------------------- |
| 注入方式 | 批量注入（一次绑定多个配置项）                               | 单个注入（一次只能绑定一个配置项）                        |
| 配置支持 | 支持松散绑定（如 `access-key-id` 匹配 `accessKeyId`）        | 不支持松散绑定，需严格匹配 `key`                          |
| 类型转换 | 自动完成类型转换（如配置文件字符串 → 实体类 Integer）        | 需手动指定类型转换（如 `${xxx:0}` 指定默认值为数字）      |
| 适用场景 | 需注入多个配置项（如第三方服务配置：OSS、Redis 等），且需复用配置类 | 仅需注入 1-2 个简单配置项（如 `server.port`），且无需复用 |
| 配置提示 | 配合 `spring-boot-configuration-processor` 支持配置提示      | 不支持配置提示                                            |





## 第六章 案例

### 第一节 登录功能

 一、登录功能的必要性：解决系统安全问题

1. 未登录系统的安全隐患：前序开发的部门 / 员工管理系统可直接通过浏览器访问（输入路径即可操作数据），若项目上线，任何人都能随意修改数据，无安全性可言。
2. 行业通用解决方案：所有后台管理系统均需「登录校验」—— 只有输入正确的用户名、密码（部分含验证码），登录成功后才能访问系统，这是保障数据安全的基础门槛。

二、核心概念：认证（Authentication）

- **定义**：根据用户名和密码，校验用户身份的过程。
- 结果
  - 认证成功：允许访问系统数据（如部门 / 员工的增删改查）；
  - 认证失败：拒绝访问，停留在登录页面并提示错误信息。

三、功能需求与技术拆解

1. 核心需求

用户在登录页面输入「员工用户名（`username`）」和「密码（`password`）」，系统校验合法性：

- 合法：跳转至系统主页；
- 不合法：停留在登录页，提示 “用户名或密码错误”。

2. 数据层：SQL 语句设计（操作员工表`emp`）

（1）校验逻辑

通过用户名和密码查询员工记录：

- 若查询到 1 条记录 → 用户名 / 密码正确（因`username`字段有**唯一约束（`unique`）**，不会出现多条结果）；
- 若查询到 0 条记录 → 用户名或密码错误。

（2）SQL 语句

```sql
SELECT * FROM emp 
WHERE username = #{username} AND password = #{password};
```

（注：`#{}`为 MyBatis 的参数占位符，避免 SQL 注入，后续课程会深入）

3. 接口设计（前后端交互规范）

| 接口要素         | 具体内容                                                     |
| ---------------- | ------------------------------------------------------------ |
| 请求路径         | `/login`                                                     |
| 请求方式         | `POST`（因需传递敏感信息，`POST`比`GET`更安全，数据在请求体中而非 URL 暴露） |
| 请求参数（JSON） | `{"username": "用户名", "password": "密码"}`                 |
| 响应结果（JSON） | 标准`Result`格式：- 成功：`{"code": 1, "msg": "success", "data": ...}`- 失败：`{"code": 0, "msg": "用户名或密码错误", "data": null}` |



### 第二节 登录校验

一、核心概念：登录校验是服务端对客户端请求的 “前置安全检查”，核心逻辑如下：

| 校验结果   | 处理逻辑                                               |
| ---------- | ------------------------------------------------------ |
| 用户已登录 | 放行请求，执行后续业务操作（如查询数据、修改信息）     |
| 用户未登录 | 拒绝执行业务操作，返回错误响应，前端接收后跳转至登录页 |

本质是通过 “状态校验” 保障系统资源仅对已授权用户开放，避免未授权访问。

二、实现登录校验的核心难点：HTTP 协议的无状态性

1. HTTP 无状态性定义：HTTP 协议是 “无状态” 的 —— 每次请求都是独立的，下一次请求不会携带上一次请求的状态信息。
2. 难点本质：服务端需要一种机制，“记住” 用户的登录状态，并在后续请求中识别该状态 —— 这需要结合会话技术和统一拦截技术实现。

三、登录校验的整体实现思路

1. 核心逻辑框架

解决 “HTTP 无状态”+“统一校验” 的问题，需分两步：

1. **存储登录标记**：用户登录成功时，服务端生成并存储 “登录状态标记”（如会话 ID、令牌），前端后续请求需携带该标记；
2. 统一拦截校验：拦截所有客户端请求，提取请求中的 “登录标记”，校验其有效性：
   - 标记有效（用户已登录）：放行请求，进入业务接口；
   - 标记无效 / 不存在（用户未登录）：返回错误，触发前端跳转登录页。

2. 关键技术方向

|   技术类别   |                           具体技术                           |                             作用                             |
| :----------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|   会话技术   |       传统 Web 会话（如 Session）、令牌技术（如 JWT）        |   解决 “如何存储 / 传递登录标记”，让服务端识别用户登录状态   |
| 统一拦截技术 | Filter（过滤器，Servlet 规范）、Interceptor（拦截器，Spring 框架） | 解决 “如何统一拦截所有请求”，避免在每个接口中重复写 “登录判断” 代码，简化开发 |



#### 2.1 会话技术

一、会话技术的核心概念

1. 会话的定义

- **本质**：用户与 Web 服务器之间的一次 “交互周期”，从用户打开浏览器访问服务器开始，到用户关闭浏览器（或超时）结束。

![image-20251120131407222](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251120131407222.png)

二、会话跟踪的核心方案

- “会话跟踪”：一种维护浏览器状态的方法，服务器需要识别多次请求是否来自于同一浏览器，以便**在同一次会话的多次请求间共享数据**。

**方式一：Cookie会话跟踪技术**

一、Cookie 的核心定位与本质

- **技术属性**：客户端会话跟踪技术，数据存储在**客户端浏览器**中，是 HTTP 协议原生支持的会话跟踪方案。
- **核心价值**：解决 HTTP 协议 “无状态” 问题，实现 “同一次会话中不同请求之间的数据共享”。

二、Cookie 的工作流程（含 HTTP 协议支撑）

Cookie 的交互全流程依赖 HTTP 协议的两个**核心头字段**，且浏览器与服务器的操作均为**自动化执行**，无需手动干预，具体步骤如下：

|            阶段            |             核心操作             |    依赖的 HTTP 头     |                           具体行为                           |
| :------------------------: | :------------------------------: | :-------------------: | :----------------------------------------------------------: |
| 1. 首次请求（设置 Cookie） | 服务器生成 Cookie 并响应给浏览器 | **响应头 Set-Cookie** | 如用户登录成功后，服务器通过`Set-Cookie: login_username=it黑马`，将用户标识数据封装到响应头，返回给浏览器。 |
|        2. 本地存储         |      自动解析并存储 Cookie       |           -           | 浏览器识别到响应头中的`Set-Cookie`后，自动将 Cookie 数据（键值对）存储在本地（可通过浏览器 “Application> Storage > Cookie” 查看）。 |
| 3. 后续请求（携带 Cookie） |     自动携带 Cookie 到服务器     |   **请求头 Cookie**   | 浏览器后续发起同一域名 / IP 的请求时，会自动将本地存储的 Cookie 通过`Cookie: login_username=itheima`封装到请求头，传递给服务器。 |
|       4. 服务器校验        |      读取 Cookie 并判断身份      |           -           | 服务器通过 Web 框架（如 SpringBoot）提供的 API（如`request.getCookies()`）获取 Cookie，校验数据是否存在 / 有效，从而确认用户身份。 |

![image-20251120133646344](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251120133646344.png)

三、Cookie 的优缺点分析

1. 优点

- **协议原生支持**：基于 HTTP 协议的`Set-Cookie`和`Cookie`头，所有主流浏览器（Chrome、Firefox 等）均自动实现解析 / 存储 / 携带逻辑，无需额外开发。
- **使用成本低**：Web 框架（如 SpringBoot、Tomcat）提供成熟的 Cookie 操作 API（`response.addCookie()`、`request.getCookies()`），开发效率高。

2. 缺点

- **移动端不支持**：仅浏览器环境可用，安卓 /iOS 等原生 APP 无法直接使用 Cookie，无法满足跨端（Web+APP）项目需求。
- **安全性差**：数据存储在客户端，用户可通过浏览器设置（如 “隐私设置和安全性 → 阻止所有 Cookie”）禁用 / 修改 Cookie，且易被劫持，仅适合存储**不敏感数据**（如用户名展示，不可存密码）。
- 跨域限制：Cookie 默认不支持跨域请求（前后端分离项目常见问题）。
  - 跨域判定：协议（HTTP/HTTPS）、IP / 域名、端口号，三者任意一个不同即视为跨域（如前端部署在`192.168.150.200:80`，后端在`192.168.150.100:8080`，属于跨域）。
  - 跨域场景下，服务器设置的 Cookie 无法被浏览器存储，后续请求也无法携带，导致会话跟踪失效。



**方式二：Session会话跟踪**

1. 核心原理

Session 是**服务器端存储的会话对象**，底层依赖 Cookie 实现 “会话 ID 传递”，本质是 “服务器存储数据 + Cookie 传递标识” 的组合。

2. 完整流程（含代码演示逻辑）

| 步骤                           | 浏览器端行为                                              | 服务器端行为                                                 |
| ------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| 1. 首次请求（如访问`/s1`接口） | 发起无 Cookie 的请求                                      | 检测到无 Session，自动创建 Session 对象，生成唯一`Session ID`；调用`session.setAttribute("loginUser", "tom")`存储用户数据；通过响应头`Set-Cookie: JSESSIONID=xxx`，将`Session ID`传给浏览器 |
| 2. 存储标识                    | 接收`Set-Cookie`，自动将`JSESSIONID=xxx`存储到本地 Cookie | -                                                            |
| 3. 后续请求（如访问`/s2`接口） | 携带本地 Cookie（含`JSESSIONID`）发起请求                 | 从请求 Cookie 中提取`Session ID`，匹配到对应的 Session 对象；调用`session.getAttribute("loginUser")`获取数据，实现跨请求共享 |

3. 代码关键操作

- 获取 Session 对象：两种方式
  1. 方法形参直接声明`HttpSession`类型（自动关联当前会话）；
  2. 通过`HttpServletRequest`对象获取：`request.getSession()`。
- 数据操作：
  - 存数据：`session.setAttribute("key", value)`（如存储登录用户）；
  - 取数据：`session.getAttribute("key")`（如后续请求获取用户信息）；
  - 验证标识：通过`Session的哈希值`判断是否为同一对象（视频演示中两次请求哈希值相同，证明是同一会话）。

4. 优缺点分析![image-20251120142135269](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251120142135269.png)

| 优点                                                         | 缺点                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 数据存储在服务器端，安全性高（用户无法篡改数据）；API 简单，无需手动实现标识传递（框架自动处理 Session 创建、Cookie 携带） | 1. **集群环境不兼容**：Session 存储在单个服务器，负载均衡下请求分发到其他服务器时，无法找到对应 Session（单点故障风险）；<br>2. 依赖 Cookie，继承 Cookie 的局限性（如浏览器禁用 Cookie 则失效、跨域 Cookie 限制） |



**方式三：基于令牌的客户端方案**

1. 核心原理：令牌（Token）是**客户端存储的字符串标识**，本质是 “服务器生成令牌 + 客户端存储 + 请求携带令牌 + 服务器校验令牌” 的全流程，无需服务器存储会话数据（无状态设计）。

2. 核心优势（解决传统方案痛点）

- **跨端兼容**：支持 PC 端、移动端、小程序 —— 无需依赖 Cookie，令牌可存储在任意客户端存储介质；
- **集群友好**：服务器无需存储会话数据，任意集群节点均可通过令牌校验身份（无 “Session 同步” 问题）；
- **减轻服务器压力**：避免服务器存储大量 Session 对象，降低内存消耗；
- **安全性可控**：令牌可加密（如 JWT 的签名机制），伪造令牌会被校验拦截。

4. 局限性

- 需手动实现全流程：令牌生成、客户端存储、请求携带、服务器校验（需前后端配合）；
- 令牌存储在客户端：需注意 “令牌泄露风险”（如 XSS 攻击窃取 LocalStorage 中的令牌，需配合 HTTPS、令牌过期策略缓解）。



#### 2.2 JWT令牌

一、JWT 基础概念

1. **全称与本质**
   - JWT（标准名称为 **JSON Web Token**），本质是一种**用于安全传输信息的字符串令牌**，核心是将 JSON 格式数据封装为可在通信双方传递的安全标识。
   - 核心作用：作为用户身份标识，解决 “登录后如何验证用户身份” 的问题，属于令牌技术的一种。
2. **核心特性**
   - **简洁性**：令牌本身是简单字符串，可直接在请求参数（如 URL）或请求头中传递，无需复杂格式处理。
   - **自包含性**：令牌中可自定义存储业务信息（如用户名、用户 ID 等），无需每次校验时都查询数据库，减少服务端开销。
   - **安全性**：依赖数字签名确保令牌不被篡改，接收方可通过签名验证令牌有效性。

二、JWT 令牌的组成（三部分，用英文句号 “.” 分隔）

JWT 令牌结构为 `Header.Payload.Signature`，三部分均基于 JSON 格式处理（前两部分编码，第三部分签名），具体如下：

|        名称         |                             作用                             |                           处理方式                           |
| :-----------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|   Header（头部）    |              记录令牌类型和签名算法（如 HS256）              |              Base64 编码（**非加密**，可解码）               |
| Payload（有效载荷） | 存储核心信息：<br>自定义数据（如用户名、用户 ID)<br> 默认数据（如令牌签发时间、有效期） |     Base64 编码（**非加密**，可解码，不建议存敏感信息）      |
|  Signature（签名）  |                  防止令牌被篡改，确保安全性                  | 基于 Header 指定的算法（如 HS256），结合**Header 编码后内容 + Payload 编码后内容 + 服务端密钥**计算生成（**不可逆向破解**） |

![image-20251120143025547](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251120143025547.png)

三、关键技术：Base64 编码（前两部分的处理方式）

1. **定义**：一种 “二进制数据→可打印字符” 的编码方式，非加密技术（可通过工具解码）。
2. **核心字符集**：共 64 个可打印字符，包括：A-Z（26）、a-z（26）、0-9（10）、“+” 和 “/”（2 个）；“=” 为补位符号（用于补齐编码后长度）。
3. **在 JWT 中的作用**：将 JSON 格式的 Header 和 Payload 转为字符串，便于在网络中传递（避免 JSON 格式的特殊字符干扰）。

四、JWT 的典型应用场景：登录校验流程

1. **登录阶段：生成 JWT 令牌**

   - 前端发起登录请求（携带用户名、密码），服务端验证 credentials 有效性；
   - 验证通过后，服务端基于用户信息（如用户 ID）生成 JWT 令牌（按 “Header→Payload→Signature” 流程生成）；
   - 服务端将 JWT 令牌返回给前端，前端存储令牌（如 LocalStorage、Cookie）。

2. **后续请求阶段：携带 JWT 令牌**

   - 前端每次发起需登录的请求（如查询个人信息）时，在请求头（或参数）中携带 JWT 令牌（例：`Authorization: Bearer <JWT令牌>`）。

3. **服务端校验阶段：验证 JWT 有效性**

   - 服务端统一拦截请求，提取 JWT 令牌；

   - 校验逻辑：

     ① 检查令牌是否存在 → 不存在则拒绝访问；

     ② 验证 Signature：用相同密钥和算法重新计算签名，与令牌中的 Signature 对比 → 不一致则令牌被篡改，拒绝访问；

     ③ 检查 Payload 中的有效期（如 exp 字段）→ 过期则拒绝访问；

     ④ 校验通过后，从 Payload 中提取用户信息（如用户 ID），放行请求并处理业务。

五、JWT 的核心优势

- **无状态**：服务端无需存储令牌（仅需保存密钥），减少服务端存储压力，适合分布式系统（多台服务端可共用密钥校验令牌）；
- **跨域友好**：令牌通过请求头传递，可解决 Cookie 的跨域限制问题；
- **自包含**：Payload 中可携带业务数据，减少服务端查询数据库的次数，提升性能。



六、JWT 令牌生成：3 步核心流程

生成 JWT 令牌需借助`Jwts`工具类，通过链式编程设置关键参数，最终生成字符串格式的令牌。

1. 核心工具类与方法

- 工具类：`io.jsonwebtoken.Jwts`（JWT 操作的核心入口）
- 核心方法：`Jwts.builder()` → 初始化 JWT 构建器，后续通过链式调用设置参数。

2. 必设 3 个关键参数

生成令牌时需明确 3 类参数，对应 JWT 的 “载荷（Payload）” 和 “签名（Signature）” 部分：

|    参数类别    |                        配置方法                         |                             说明                             |
| :------------: | :-----------------------------------------------------: | :----------------------------------------------------------: |
| 签名算法与密钥 | `signWith(SignatureAlgorithm algorithm, String secret)` | 用于生成数字签名，确保令牌不被篡改；**算法需与校验时一致，密钥需保密** |
| 自定义业务数据 |         `setClaims(Map<String, Object> claims)`         | 存储用户相关的非敏感数据（如用户 ID、用户名），会被 Base64 编码到令牌的 “载荷” 部分，可解码查看 |
|   令牌有效期   |            `setExpiration(Date expiration)`             | 设置令牌失效时间，避免令牌永久有效；需传入`Date`对象，代表失效时刻 |

3. 生成令牌并输出

最后调用`compact()`方法，将构建的参数组装为 JWT 令牌（字符串格式），可打印到控制台或返回给前端。



#### 2.3 过滤器Filter

一、Filter 基础认知

1. 定位与地位

- **Java Web 三大组件之一**：另外两个组件是 Servlet、Listener，目前仅 Filter 在企业开发中使用频率较高，Servlet 和 Listener 已极少使用。
- **非 Spring Boot 原生组件**：Filter 是 Java Web 标准规范定义的组件，需额外配置才能在 Spring Boot 项目中生效。

2. 核心作用：拦截与通用处理

Filter 的核心能力是**统一拦截对 Web 资源的请求**，在请求到达资源前、资源响应后执行通用逻辑，避免重复编码。具体应用场景包括：

- 登录校验（核心场景）：统一校验用户是否登录，替代 “每个接口重复编写校验逻辑” 的繁琐操作；
- 统一编码处理：如设置请求 / 响应的字符集（如 UTF-8）；
- 敏感字符过滤：拦截请求中的违规字符并处理；
- 日志记录：统一记录请求的 URL、参数、耗时等信息。

![image-20251120153948106](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251120153948106.png)

3. 执行流程：Filter 是请求访问资源的 “必经之路”，完整流程如下：

- **请求拦截**：浏览器发送请求后，先被 Filter 拦截，不直接进入业务接口；
- **逻辑处理**：Filter 执行自定义逻辑（如登录校验）；
- **请求放行**：若逻辑校验通过，通过`FilterChain`将请求传递给目标资源（如 Controller 接口）；
- **资源处理**：目标资源执行业务逻辑并生成响应；
- **响应返回**：响应结果先回到 Filter，Filter 可进一步处理响应（如添加统一响应头），最终返回给浏览器。

> 关键注意点：若 Filter 未执行 “放行” 操作，请求会被阻断，无法访问后续资源。

二、Filter 开发步骤

开发 Filter 需完成 “定义 Filter 类” 和 “配置生效” 两步，缺一不可。

1. 第一步：定义 Filter 类，需实现`javax.servlet.Filter`接口，并重写核心方法。

- Filter 接口包含 3 个方法，核心差异在于执行时机和调用次数：

|    方法名    |                作用                |               调用时机                |    调用次数    |
| :----------: | :--------------------------------: | :-----------------------------------: | :------------: |
|   `init()`   | 初始化操作（如加载配置、创建资源） | Web 服务器启动时（Filter 对象创建后） |    仅 1 次     |
| `destroy()`  |  销毁操作（如释放资源、关闭连接）  | Web 服务器关闭时（Filter 对象销毁前） |    仅 1 次     |
| `doFilter()` |  核心拦截逻辑（如登录校验、放行）  |           每次拦截到请求时            | 多次（随请求） |

> 注意：`init()`和`destroy()`在 Filter 接口中已有默认实现，若无需特殊处理可不用重写；`doFilter()`必须重写，否则无法实现拦截逻辑。

2. 第二步：配置生效，由于 Filter 是 Java Web 组件，需额外配置才能在 Spring Boot 中生效，核心是两个注解：

（1）`@WebFilter`注解（Filter 类上），作用：标识当前类是 Filter 组件，并指定拦截的请求路径。

- 核心属性：`urlPatterns`，用于定义拦截规则，常见配置如下：
  - `/*`：拦截所有请求（如课程入门案例）；
  - `/api/*`：拦截以`/api`开头的所有请求（如接口请求）；
  - `/dept/*`：拦截部门管理相关的所有请求（如部门增删改查）；
  - `*.do`：拦截所有以`.do`结尾的请求（传统项目常用）。

（2）`@ServletComponentScan`注解（启动类上）

作用：开启 Spring Boot 对 Java Web 三大组件（Servlet、Filter、Listener）的扫描支持，否则`@WebFilter`注解无法生效。

三、Filter 拦截路径配置

拦截路径用于指定 Filter 要拦截哪些请求（配置位置通常在 `@WebFilter(urlPatterns = "拦截路径")` 注解中）。

1. 三种拦截路径对比

|   配置类型   |          语法示例          |                           作用说明                           |                           适用场景                           |
| :----------: | :------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| 拦截具体路径 |  `urlPatterns = "/login"`  |    仅拦截指定的单个请求路径（精确匹配），其他路径不拦截。    |                 针对特定接口（如登录、注销）                 |
|   目录拦截   | `urlPatterns = "/depts/*"` |  拦截以指定目录开头的所有请求（`*` 表示目录下任意子路径）。  | 针对某类资源（如部门管理所有接口 `/depts/list`、`/depts/delete/1`） |
| 拦截所有路径 |    `urlPatterns = "/*"`    | 拦截当前项目下所有请求（包括静态资源如 CSS、JS，需注意排除不必要资源）。 |             全局拦截（如统一登录校验、全局日志）             |

#### 2.4 过滤器链

一、过滤器链（Filter Chain）基础概念

1. 定义

在一个 Web 应用中，可配置**多个 Filter 过滤器**，这些过滤器按特定顺序组成的 “处理序列” 称为**过滤器链**。

- 作用：对请求进行 “多阶段拦截处理”（如先校验登录状态、再校验权限、最后过滤非法字符），也可对响应进行多阶段处理。
- 核心对象：`FilterChain`（过滤器链对象），用于控制过滤器间的调用顺序和请求放行逻辑。

2. 核心原则

- **“放行即传递”**：当前过滤器调用`filterChain.doFilter(request, response)`时，并非直接访问 Web 资源，而是将请求**传递给下一个过滤器**；若当前是最后一个过滤器，才会放行到目标 Web 资源（如 Servlet、Controller 接口）。
- **“响应时回溯”**：Web 资源处理完请求后，响应会按 “过滤器执行顺序的反向” 回溯，依次执行每个过滤器中 “放行后的逻辑”。

二、过滤器执行优先级规则

**以注解`@WebFilter`配置的过滤器，优先级由 “过滤器类名的自然排序” 决定**（即按字母 / 数字的 ASCII 码顺序排序）。

- 规则：类名首字母越靠前，优先级越高（先执行）；若首字母相同，比较后续字符。
- 示例：
  - 类名`ABCFilter`（A 开头）优先级高于`DemoFilter`（D 开头）→ 先执行 ABCFilter；
  - 若将`ABCFilter`改名为`XBCFilter`（X 开头），则`DemoFilter`（D 开头）优先级更高→ 先执行 DemoFilter。

> 注意：若通过`web.xml`配置过滤器，优先级由`<filter-mapping>`标签的配置顺序决定（先配置的先执行），与类名无关。



#### 2.5 拦截器Interceptor

一、拦截器基本概念

1. 定义与定位

拦截器（Interceptor）是 **Spring 框架提供的动态拦截机制**，专门用于拦截**控制器（Controller）方法的执行**，属于 Spring MVC 体系的核心组件，而非 Java EE 标准（区别于 Filter）。

2. 核心作用

- **请求拦截**：在前端请求到达 Controller 方法前、后或视图渲染完成后，执行预定义逻辑；
- 通用操作：集中处理通用性需求，例如：
  - 登录校验（校验请求是否携带合法 JWT 令牌，未登录则返回错误）；
  - 日志记录（记录请求路径、参数、执行时间等）；
  - 权限控制（判断用户是否有操作某接口的权限）。

3. 与过滤器（Filter）的关联

二者均用于请求拦截，但拦截器更 “聚焦于 Spring 控制器层”，后续会通过细节对比进一步区分，入门阶段可先理解为 “Spring 体系内的 Filter”。

二、拦截器开发核心流程

开发拦截器需严格遵循 “定义拦截器 → 配置拦截器” 的流程，缺一不可。

第一步：定义拦截器（实现接口 + 重写方法）

1. 核心接口与注解

- 实现 `HandlerInterceptor` 接口（Spring 提供的拦截器标准接口）；
- 类上添加 `@Component` 注解，将拦截器交给 Spring IOC 容器管理（后续配置时需注入）。

2. 重写 3 个关键方法

`HandlerInterceptor` 接口包含 3 个默认实现方法，需根据业务需求重写，**核心逻辑集中在 `preHandle`**：

|      方法名       |             执行时机              |                          返回值含义                          |                    核心用途                    |
| :---------------: | :-------------------------------: | :----------------------------------------------------------: | :--------------------------------------------: |
|    `preHandle`    | 目标 Controller 方法执行 **之前** | `true`：放行（允许执行 Controller 方法）；`false`：拦截（不执行 Controller） |           登录校验、权限判断（核心）           |
|   `postHandle`    | 目标 Controller 方法执行 **之后** |                       无返回值（void）                       | 对 Controller 结果做二次处理（如修改响应数据） |
| `afterCompletion` |   整个请求的视图渲染 **完成后**   |                       无返回值（void）                       |         资源清理（如关闭流、释放连接）         |



第二步：配置拦截器（指定拦截路径）

需定义 **配置类**，实现 `WebMvcConfigurer` 接口，重写 `addInterceptors` 方法，注册拦截器并指定拦截范围，核心是通过 `addPathPatterns()`（指定拦截路径）和 `excludePathPatterns()`（指定排除路径）方法实现。

1. 配置类核心要求

- 类上添加 `@Configuration` 注解，标识为 Spring 配置类；
- 注入第一步定义的拦截器（通过 `@Autowired`，因拦截器已加 `@Component`）；
- 调用 `InterceptorRegistry` 的 `addInterceptor` 注册拦截器，`addPathPatterns` 指定拦截路径。

2. 关键路径匹配规则

- `"/**"`：拦截 **所有请求**（包括多级路径，如 `/user/login`、`/order/list`）；
- `"/user/**"`：仅拦截 `/user` 开头的请求（如 `/user/login`，不拦截 `/order`）；
- `"/user/*"`：仅拦截 `/user` 下一级路径（如 `/user/login`，不拦截 `/user/info/detail`）。

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration // 标识为Spring配置类
public class WebConfig implements WebMvcConfigurer {

    // 注入第一步定义的拦截器（IOC容器中已存在）
    @Autowired
    private LoginCheckInterceptor loginCheckInterceptor;

    // 注册拦截器并配置拦截路径
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loginCheckInterceptor) // 注册拦截器
                .addPathPatterns("/**"); // 拦截所有请求（实际场景可排除登录接口，如excludePathPatterns("/login")）
    }
}
```



二、拦截器的执行流程

拦截器是 Spring 框架提供的组件，仅作用于 “进入 Spring 环境的资源”（如 Controller 接口），其执行流程需结合**过滤器（Filter）** 和**前端控制器（DispatcherServlet）** 理解，完整执行链路（Filter + Interceptor + Controller）

![image-20251120173917347](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251120173917347.png)

1. 浏览器发起请求→ 先被过滤器（Filter）拦截：
   - 执行 Filter 的 “放行前逻辑”（如日志打印、编码设置）；
   - 执行 `chain.doFilter()` 放行，请求进入 Spring 环境。
2. 请求进入 Spring 环境→ 先经过前端控制器（DispatcherServlet）：
   - DispatcherServlet 是 Spring MVC 的核心，负责接收请求并分发到对应 Controller；
   - 分发前先被**拦截器（Interceptor）拦截**。
3. 拦截器执行核心方法
   - **`preHandle()`**：Controller 方法执行**前**执行（如登录校验：判断 Token 是否有效，返回`true`放行，`false`拦截）；
   - **`postHandle()`**：Controller 方法执行**后**、视图渲染前执行（如统一响应处理）；
   - **`afterCompletion()`**：视图渲染完成后执行（如资源清理、异常处理）。
4. Controller 执行完毕→ 响应沿原链路返回：
   - 先回到 DispatcherServlet，再回到 Filter；
   - 执行 Filter 的 “放行后逻辑”（如后续日志打印），最终响应给浏览器。

## 四、入门阶段关键注意事项

1. **IOC 容器管理**：拦截器必须加 `@Component`，配置类需通过 `@Autowired` 注入，否则拦截器无法生效；
2. **路径配置**：`addPathPatterns("/**")` 会拦截所有请求，实际开发中需通过 `excludePathPatterns` 排除无需拦截的接口（如登录 `/login`、注册 `/register`）；
3. **与 Filter 的区别（初步）**：拦截器仅拦截 Controller 方法，不拦截静态资源（如 HTML、CSS），而 Filter 可拦截所有请求（包括静态资源），后续课程会深入对比。



### 第一节 异常处理

 一、异常问题现象与根源

1. 未处理异常的表现

- **前端无反馈**：重复添加 “就业部”（部门名称含唯一约束）时，页面无提示、数据未新增，用户体验差；
- **响应不符合规范**：打开 F12 查看网络请求，发现响应状态码为**500（服务器端异常）**，且返回的 JSON 数据仅包含错误时间、状态码、描述等原生信息，并非项目约定的 “统一响应结果（Result）”，导致前端无法解析；
- **异常逐层上抛**：三层架构（Controller→Service→Mapper）中，若各层均未处理异常，异常会从 Mapper（操作数据库出错）抛给 Service，再抛给 Controller，最终抛给框架，由框架返回原生错误信息。

2. 异常根源

- **业务约束冲突**：部门表（dept）的`name`字段添加了`unique`（唯一约束），重复插入相同名称触发数据库异常；
- **未统一异常处理**：项目未定义异常处理机制，导致异常直接暴露给框架，无法按照开发规范封装响应结果。

二、异常处理的两种方案

|          方案          |                           实现方式                           |                      优点                      |                             缺点                             |   推荐度   |
| :--------------------: | :----------------------------------------------------------: | :--------------------------------------------: | :----------------------------------------------------------: | :--------: |
| 方案 1：手动 try-catch | 在 Controller 的每个方法中，通过`try-catch`捕获 Service/Mapper 抛出的异常，手动封装 Result 响应 |              实现简单，入门易理解              | 代码臃肿（每个方法都需重复 try-catch）、维护成本高（新增方法需额外加处理） |  ❌ 不推荐  |
| 方案 2：全局异常处理器 | 定义一个全局统一的异常处理类，捕获整个项目中所有未处理的异常，统一封装响应 | 代码简洁、优雅，一次定义全局生效，降低维护成本 |                     需理解特定注解的作用                     | ✅ 项目推荐 |

三、全局异常处理器核心实现

全局异常处理器是本次课程的重点，核心通过**2 个关键注解 + 1 个处理类**实现，步骤如下：

1. 核心注解解析

|          注解           | 作用位置 |                           核心功能                           | 补充说明                                                     |
| :---------------------: | :------: | :----------------------------------------------------------: | ------------------------------------------------------------ |
| `@RestControllerAdvice` |   类上   | 标识该类为 “全局异常处理器”，同时具备`@ControllerAdvice`（全局控制器增强）和`@ResponseBody`（将返回值转为 JSON）的功能 | 无需额外加`@ResponseBody`，底层已集成，确保返回的 Result 能自动转为前端可解析的 JSON |
|   `@ExceptionHandler`   |  方法上  |    指定当前方法负责捕获的 “异常类型”，通过`value`属性配置    | 若`value = Exception.class`，表示捕获**所有类型的异常**（通用处理）；也可指定具体异常（如`NullPointerException.class`）做针对性处理 |

2. 实现步骤（代码示例）

步骤 1：创建异常处理类

在项目中新建包（如`com.itheima.exception`），创建全局异常处理类（如`GlobalExceptionHandler`）：

```java
// 1. 类上添加@RestControllerAdvice，标识为全局异常处理器
@RestControllerAdvice
public class GlobalExceptionHandler {

    // 2. 方法上添加@ExceptionHandler，指定捕获所有异常
    @ExceptionHandler(value = Exception.class)
    public Result handleException(Exception e) {
        // 3. 异常处理逻辑：输出堆栈信息（便于排查问题）
        e.printStackTrace();
        // 4. 封装统一响应结果Result，返回给前端
        return Result.error("对不起，操作失败，请联系管理员");
    }
}
```

步骤 2：异常处理流程

1. 业务层（Mapper/Service）抛出异常，且未手动处理；
2. 异常逐层上抛至 Controller，Controller 也未处理；
3. 全局异常处理器（`GlobalExceptionHandler`）通过`@ExceptionHandler`捕获异常；
4. 执行处理逻辑（如打印日志），封装`Result`对象；
5. `@RestControllerAdvice`自动将`Result`转为 JSON，响应给前端；
6. 前端解析标准的 Result 格式，展示错误提示（如 “对不起，操作失败，请联系管理员”）。

步骤3 :  效果验证

- **前端反馈正常**：重复添加 “就业部” 时，页面会弹出错误提示，而非无响应；
- **响应符合规范**：网络请求返回的 JSON 是项目约定的 Result 格式（含`code`、`message`、`data`等字段），前端可正常解析；
- **问题排查便捷**：服务器端控制台会打印异常堆栈信息，便于开发人员定位 “唯一约束冲突” 的根源。
