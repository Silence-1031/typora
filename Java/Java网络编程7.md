# Java网络编程

## 第一章 概述

 一、网络编程基础概念

1. **定义**：实现不同设备中程序（如电脑、手机的软件 / 程序）之间数据交互的技术，本质是解决 “设备间通信” 的技术方案。
2. 生活中的应用场景
   - 即时通讯
   - 网页浏览
3. **技术必要性**：现代生活已高度依赖网络通信，从社交、娱乐到办公，网络编程是支撑这些场景的底层技术，不可或缺。

 二、两大核心通信架构（CS 与 BS）

1. CS 架构（Client-Server：客户端 - 服务端架构）

![image-20251031160927941](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251031160927941.png)

|      维度      |                           具体说明                           |
| :------------: | :----------------------------------------------------------: |
|  **核心组成**  |     需开发**客户端**和**服务端**两部分，二者配合实现通信     |
| **客户端特点** | 需程序员开发，用户需下载、安装客户端软件（如微信、IDEA、音乐软件） |
| **服务端特点** | 需程序员开发，负责接收客户端请求、处理数据，并向客户端响应结果 |
|  **典型案例**  | 微信（用户安装客户端，与微信服务端交互）、IDEA（Java 开发的客户端，可下载插件） |

2. BS 架构（Browser-Server：浏览器 - 服务端架构）

|       维度       |                           具体说明                           |
| :--------------: | :----------------------------------------------------------: |
|   **核心组成**   | 仅需开发**服务端**，客户端直接使用现成的浏览器（无需程序员开发客户端） |
|  **客户端特点**  | 客户端为通用浏览器（如 Chrome、Edge），用户直接下载使用，无需额外开发 |
|  **服务端特点**  | 需程序员开发，负责处理浏览器的请求（如访问网页、提交数据），返回网页数据 |
|   **典型案例**   | B 站网页版、企业内部办公系统（用户通过浏览器访问，无需安装专用软件） |
|   **核心优势**   | 服务端升级后，浏览器访问即可获取最新内容，无需更新客户端<br>随时随地有浏览器 + 网络即可访问，分布式特性更优 |
| **行业应用占比** | 商用场景中占绝对主导（保守估计 99% 的企业项目为 BS 架构），是 Java Web 开发的核心方向 |

三、Java 网络编程技术基础

- **核心包**：Java 将网络编程相关的类和接口统一封装在 `java.net` 包中，是实现网络通信的 “工具库”。



## 第二章 网络通信三要素

一、网络编程三要素

| 要素    | 作用                                                         | 类比理解                                              |
| ------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| IP 地址 | 设备在网络中的**唯一标识**，用于定位设备                     | 设备的 “身份证号”                                     |
| 端口    | 应用程序在设备中的**唯一标识**，用于区分设备上的不同程序     | 设备的 “门牌号”（如微信对应特定端口，避免与 QQ 冲突） |
| 协议    | 网络中数据传输的**规则**（如连接方式、数据格式、是否需确认） | 通信双方的 “对话准则”                                 |

 二、IP 地址

1. IP 地址（Internet Protocol 互联网协议地址）是分配给网络设备的唯一标识，目前主流分为 IPv4 和 IPv6 两类：

| 类别 |   地址长度   |                          表示方式                           |       地址数量        |                             特点                             |
| :--: | :----------: | :---------------------------------------------------------: | :-------------------: | :----------------------------------------------------------: |
| IPv4 | 32 位二进制  |               点分十进制（如`192.168.1.66`）                |     2³² ≈ 42 亿个     |             地址数量有限，已无法满足全球设备需求             |
| IPv6 | 128 位二进制 | 冒分十六进制（如`2001:0DB8:85A3:0000:0000:8A2E:0370:7334`） | 2¹²⁸ ≈ 全球沙子级数量 | 地址极多，解决 IPv4 枯竭问题；目前设备多为 “IPv4+IPv6 双支持”（兼容老设备） |

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251031161705110.png" alt="image-20251031161705110" style="zoom:67%;" />

2. 域名(Domain Name)与 DNS(Domain Name System) 解析

- **域名的作用**：人类难以记忆复杂的 IP 地址（如`180.101.49.11`），因此用 “域名”（如`www.baidu.com`）替代，提升易用性。
- DNS 解析原理：域名需通过DNS（域名解析器）转换为真实 IP 才能访问：
  1. 本地 DNS 服务器查询：若本地已缓存域名 - IP 映射，直接返回 IP；![image-20251031162058085](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251031162058085.png)
  2. 运营商 DNS 服务器查询：若本地无缓存，自动请求运营商 DNS（记录全球域名 - IP 映射），并将结果缓存到本地，方便后续使用。

3. 公网 IP 与内网 IP

|         类别         |                      作用                      |          范围示例           |                             特点                             |
| :------------------: | :--------------------------------------------: | :-------------------------: | :----------------------------------------------------------: |
|       公网 IP        |         用于**连接互联网**，全球可访问         |   如阿里云服务器分配的 IP   |              全球唯一，需申请（如购买云服务器）              |
| 内网 IP（局域网 IP） | 仅在**组织机构内部使用**（如公司、教室局域网） | 如`192.168.x.x`、`10.x.x.x` | 不同局域网可重复使用，节省公网 IP；内网设备无法直接被外网访问 |

4. 本机 IP（特殊 IP）

- 固定标识：`127.0.0.1`（IP 地址）和`localhost`（对应域名）；
- 作用：永远指向**当前程序所在的主机**，常用于同一设备内多个系统的通信（如本地开发时，服务端与客户端的连接）。

5. 物理地址（MAC 地址）

- 定义：设备出厂时自带的**全球唯一标识**（16 进制表示，如`00-1C-42-21-37-4D`），与 IP 地址不同（IP 可动态变化，MAC 固定不变）；
- 应用：企业常通过绑定 MAC 地址限制上网设备（仅注册 MAC 的设备可接入公司内网）。

 三、IP 相关实用命令（Windows 系统）

1. `ipconfig`：查看本机 IP 信息

- 基础用法：打开命令行，输入`ipconfig`，可查看本机内网 IP、公网 IP 等；
- 扩展用法：输入`ipconfig /all`，可查看**物理地址（MAC 地址）** 等更详细信息。

2. `ping`：测试设备间连通性

- 作用：判断本机与目标主机（IP 或域名）是否能正常通信；
- 用法：命令行输入`ping 目标IP/域名`（如`ping www.baidu.com`、`ping 192.168.1.100`）；

 四、Java 中操作 IP 的核心类：`InetAddress`

Java 通过`java.net.InetAddress`类封装 IP 地址，提供获取 IP 信息、判断连通性等方法，常用操作如下：

1. 获取本机 IP 对象

```java
import java.net.InetAddress;
import java.net.UnknownHostException;

public class IpDemo {
    public static void main(String[] args) throws UnknownHostException {
        // 获取本机IP对象
        InetAddress localIp = InetAddress.getLocalHost();
        System.out.println("本机IP对象：" + localIp); // 输出格式：主机名/IP地址
        System.out.println("本机IP字符串：" + localIp.getHostAddress()); // 如192.168.1.66
        System.out.println("本机主机名：" + localIp.getHostName()); // 如DESKTOP-ABC123
    }
}
```

2. 获取目标设备 IP 对象（通过 IP 或域名）

```java
// 通过域名获取（如百度）
InetAddress baiduIp = InetAddress.getByName("www.baidu.com");
System.out.println("百度IP：" + baiduIp.getHostAddress()); // 输出百度服务器IP（可能多个，因百度多节点部署）

// 通过IP获取（如本地设备）
InetAddress targetIp = InetAddress.getByName("192.168.1.100");
System.out.println("目标主机名：" + targetIp.getHostName());
```

3. 判断本机与目标设备的连通性

```java
// isReachable(timeout)：判断timeout毫秒内是否连通，返回true/false
boolean isConnected = baiduIp.isReachable(5000); // 5秒内判断与百度的连通性
System.out.println("是否连通百度：" + isConnected); // 联网时返回true，断网时返回false
```



五、端口

1. 端口的定义与作用

- **核心功能**：IP 地址仅能定位 “主机设备”，而端口是**主机内运行的应用程序的唯一标识**，用于区分同一设备上的不同应用（如微信、QQ），确保数据**准确送达目标程序。**
- **通俗类比**：若 IP 是 “小区地址”，端口就是 “户号”，数据需通过 “户号” 找到具体住户（应用程序）。

2. 端口号的技术特性

- **取值范围**：端口号是 16 位二进制数，对应十进制范围为 **0~65535**（共 65536 个端口），足以满足单设备上的应用标识需求（日常设备常用程序通常不超过 100 个）。
- **端口号唯一性**：**同一设备内，多个应用程序的端口号不能重复**，否则会出现 “端口冲突”，导致程序启动失败（报错如 `BindSocketException`，即 “绑定端口异常”）。
- **跨设备端口重复问题**：不同设备上的同一应用（如你电脑的微信和他人电脑的微信）可使用相同端口号，因 IP 地址已区分设备，不会冲突。

3. 端口的分类（按用途划分）

| 端口类型             | 取值范围    | 用途说明                                                     | 典型示例                                |
| -------------------- | ----------- | ------------------------------------------------------------ | --------------------------------------- |
| 周知端口（知名端口） | 0~1023      | 预留给系统或知名应用，**不允许用户程序使用**                 | HTTP 协议用 80 端口、FTP 协议用 21 端口 |
| 注册端口             | 1024~49151  | 分配给用户开发的应用程序，**推荐开发者使用此范围端口**       | 自定义程序用使用某个端口端              |
| 动态端口             | 49152~65535 | 临时分配给启动的程序，程序关闭后端口释放，供其他程序复用，**无需手动指定** | 临时通信的随机端口                      |

4. 端口冲突的解决方案

当启动程序报 “绑定端口异常” 时，有两种处理方式：

1. 关闭占用目标端口的其他程序，释放端口；
2. 修改当前程序的端口号（需确保在 “注册端口” 范围内，且不与其他程序重复）。



六、协议

1. 协议的定义与核心作用

- **定义**：网络通信中，设备间预先约定的**连接规则**（如何建立连接）和**数据传输规则**（数据格式、交互逻辑）的总称。
- **核心目的**：解决 “不同设备（手机、电脑、手表等）如何互通” 的问题，需遵循统一标准才能实现全球设备互联。

2. 常见网络协议模型

 （1）OSI 网络参考模型（理论模型）

- 是国际互联网组织制定的 “开放网络互联标准”，将通信流程分为 7 层，设计过于理论化，**实际开发中几乎不使用**。

（2）TCP/IP 网络模型（实际标准）

- 是全球设备互联的**实际通用协议**，将通信流程简化为 4 层，硬件（如网卡）和软件均按此模型设计，确保兼容性。

- TCP/IP 四层模型及程序员关注点

  |      模型层级       |                           核心功能                           |  面向人群  |
  | :-----------------: | :----------------------------------------------------------: | :--------: |
  |       应用层        |      定义应用级数据格式（如网页、文件、邮件的传输规则）      |   程序员   |
  |       传输层        |         负责端到端的数据传输（确保数据送达目标端口）         |   程序员   |
  |       网络层        |               负责 IP 地址定位（找到目标设备）               | 网络工程师 |
  | 数据链路层 + 物理层 | 负责将数据转为二进制信号（如电信号、光信号），通过硬件（网线、光纤）传输 | 硬件工程师 |

3. 传输层核心协议：UDP 与 TCP

传输层有两种核心协议，开发者需根据业务场景选择，二者特性完全相反：

（1）UDP 协议（用户数据报协议-User Datagram Protocol）

- 核心特点：**无连接、不可靠、高效率**
  - 无连接：通信前无需建立连接，直接发送数据包 (包含：自己的IP、端口、目的地IP、端口和数据等)；
  - 不可靠：发送方不确认接收方是否在线，数据丢失不重发，接收方收到数据也不返回确认；
  - 高效率：无需连接和确认流程，传输速度快；
  - 数据限制：单个数据包最大为 64KB。
- **适用场景**：对可靠性要求低、对实时性要求高的场景，如**视频直播、语音通话**（丢失少量数据仅导致画面 / 声音短暂模糊，不影响整体体验）。

 （2）TCP 协议（传输控制协议-Tansmission Control Protocol）

- 核心特点：**面向连接、可靠、低效率**
  - 面向连接：通信前必须通过 “三次握手” 建立可靠连接；
  - 可靠：通过 “数据确认”“重传机制”“消息 ID 去重” 确保数据不丢失、不重复；
  - 低效率：连接和确认流程会增加耗时；
  - 数据无限制：可传输大量数据（如文件、大文本）。
- **适用场景**：对可靠性要求高、不允许数据丢失的场景，如**网页下载、文件传输、支付功能**（丢失数据会导致页面错乱、文件损坏或支付异常）。

4. TCP 协议的关键机制

 （1）三次握手（建立连接）

- **目的**：确保通信双方 “既能发消息，也能收消息”（全双工模式），避免连接建立后一方无法收发的问题。
- 流程逻辑：
  - 客户端 → 服务端：“我要连接你”（服务端收到后，确认 “客户端能发”）；
  - 服务端 → 客户端：“我收到你的请求了”（客户端收到后，确认 “服务端能收也能发”）；
  - 客户端 → 服务端：“好的，开始连接”（服务端收到后，确认 “客户端能收”）。

 （2）四次挥手（断开连接）

- **目的**：确保双方已完成所有数据收发，避免断开后残留未处理数据。
- 流程逻辑：
  - 客户端 → 服务端：“我要断开连接”（服务端收到后，确认 “断开请求已送达”）；
  - 服务端 → 客户端：“收到请求，稍等我处理剩余数据”（客户端收到后，等待服务端收尾）；
  - 服务端 → 客户端：“我处理完了，可以断开了”（客户端收到后，确认 “服务端无残留数据”）；
  - 客户端 → 服务端：“好的，断开”（服务端收到后，正式断开连接）。

（3）数据可靠性保障补充

- **确认机制**：服务端收到数据后，会向客户端返回 “ACK 确认信号”；若客户端未收到确认，会重发数据，直到服务端确认。
- **去重机制**：每个数据包携带唯一 “消息 ID”，服务端收到重复 ID 的数据包时，会直接拒绝并返回确认，避免数据重复处理。



## 第三章 UDP通信

 一、UDP 协议基础特性

UDP（用户数据报协议）是无连接、不可靠的传输层协议，核心特点如下：

1. **无连接**：通信双方无需事先建立连接，发送端直接发送数据，接收端被动接收。
2. **不可靠**：发送端发送数据后不关心接收端是否收到，数据可能丢失、重复或乱序（因无确认机制）。
3. **数据限制**：单次发送数据封装在**64KB 以内**的数据包中，超过需分多次发送。
4. **效率高**：无需建立连接和确认，适合对实时性要求高（如视频通话、广播）、数据丢失可接受的场景。

 二、Java UDP 通信核心 API

Java 通过`java.net`包提供 UDP 通信所需类，核心是**两个类**：`DatagramSocket`（数据报套接字）和`DatagramPacket`（数据报数据包），二者分工明确：

|       类名       |                             作用                             |
| :--------------: | :----------------------------------------------------------: |
| `DatagramSocket` | 代表 “通信的一端”（发送端 / 接收端），负责创建通信通道、发送 / 接收数据包 |
| `DatagramPacket` |    代表 “封装数据的容器”，包含数据、目的地 IP、目的地端口    |

1. DatagramSocket（通信端）

- 创建发送端（客户端）:无需指定端口，系统自动分配动态端口（因发送端无需被其他端主动查找）`DatagramSocket socket = new DatagramSocket();`
- 创建接收端（服务端）：必须指定固定端口(因接收端需被发送端通过端口定位，否则发送端无法找到）`DatagramSocket socket = new DatagramSocket(8080);`（端口需与发送端目标端口一致）
- 核心方法：
  - `send(DatagramPacket p)`：发送数据包（发送端调用）。
  - `receive(DatagramPacket p)`：接收数据包（接收端调用，**阻塞方法**—— 若无数据则一直等待）。
  - `close()`：关闭套接字，释放网卡等资源（通信结束后必须调用，避免资源泄漏）。

2. DatagramPacket（数据包）

数据包分 “发送用” 和 “接收用” 两种构造器，需根据场景选择：

（1）发送端数据包（封装待发送数据 + 目的地信息）

- 构造器：`DatagramPacket(byte[] buf, int length, InetAddress address, int port)`

  参数说明：

  - `byte[] buf`：待发送的数据（需转为字节数组，对应视频中的 “韭菜”）。
  - `int length`：实际发送的数据长度（通常为`buf.length`，即发送全部数据）。
  - `InetAddress address`：接收端的 IP 地址（如本机 IP 用`InetAddress.getLocalHost()`获取）。
  - `int port`：接收端的固定端口（如 8080，需与接收端`DatagramSocket`端口一致）。

 （2）接收端数据包（仅需准备 “接收数据的容器”）

- 构造器：`DatagramPacket(byte[] buf, int length)`

  参数说明：

  - `byte[] buf`：接收数据的字节数组（建议设为 64KB，即`new byte[64*1024]`，适配 UDP 最大数据量）。
  - `int length`：接收数组的长度（通常为`buf.length`，即最大化接收数据）。

- 接收后获取数据：数据会存入`buf`数组，可通过`new String(buf, 0, packet.getLength())`转为字符串（`getLength()`获取实际接收的数据长度，避免读取数组中未使用的空值）。

三、UDP 通信实现流程

视频分 “一发一收”（基础版）和 “多发多收”（进阶版）两种场景，核心流程可总结为 “客户端（发送端）” 和 “服务端（接收端）” 两部分：

场景 1：一发一收（单次通信）

```java
//Server_demo
public static void main(String[] args) throws IOException {
        //1.创建接收端对象
        DatagramSocket socket=new DatagramSocket(8080);
        //2.创建数据包对象来接收数据
        byte[] buf=new byte[1024*64];
        DatagramPacket packet=new DatagramPacket(buf,buf.length);

        //3.接收数据
        socket.receive(packet);
        //4.解析数据
        System.out.println("服务端收到了"+new String(packet.getData(),0,packet.getLength()));
        //指定长度，否则会将64KB都打印出来
    
    	socket.close();
    }

//Client_demo
public class Client_Demo {
    //完成UDP通信一发一收--客户端
    public static void main(String[] args) throws IOException {
        //1.创建发送端对象
        DatagramSocket socket=new DatagramSocket();
        //2.创建数据，并把数据打包
        byte[] bytes="我是客户端".getBytes();
        DatagramPacket packet=new DatagramPacket(bytes,bytes.length, InetAddress.getLocalHost(),8080);

        //3.发送数据
        socket.send(packet);
        
        socket.close();
    }

}
```

**关键注意点**

- **启动顺序**：必须**先启动服务端**，再启动客户端。若先启动客户端，数据会因找不到接收端而丢失。
- **端口一致性**：客户端数据包的 “目的地端口” 必须与服务端`DatagramSocket`的端口一致（如均为 8080），否则服务端无法接收。



场景 2：多发多收（循环通信）

在 “一发一收” 基础上，通过**死循环**实现 “发送端反复发、接收端反复收”，并支持 “客户端主动退出”，核心改造点如下：

1. 客户端（发送端）改造

- 新增**Scanner**：让用户手动输入数据，而非固定数据。
- 新增**while (true) 死循环**：循环接收用户输入，直到输入 “exit” 退出。
- 退出逻辑：输入 “exit” 时，break 循环并关闭套接字。

2. 服务端（接收端）改造

- 新增**while (true) 死循环**：循环调用`receive()`，反复接收客户端数据（服务端通常不主动退出，需手动停止）。
- 避免重复创建资源：`DatagramSocket`和接收用`DatagramPacket`放循环外，仅在循环内接收数据并解析。

```java
//Server_demo
public class Server_Demo {
    public static void main(String[] args) throws IOException {
        //1.创建接收端对象
        DatagramSocket socket=new DatagramSocket(8080);
        //2.创建数据包对象来接收数据
        byte[] buf=new byte[1024*64];
        DatagramPacket packet=new DatagramPacket(buf,buf.length);

        while (true) {
            //3.接收数据
            socket.receive(packet);
            //4.解析数据
            System.out.println("服务端收到了"+new String(packet.getData(),0,packet.getLength()));
            //指定长度，否则会将64KB都打印出来
            String ip = packet.getAddress().getHostAddress();
            int port = packet.getPort();
            System.out.println("对方ip:"+ip+"对方端口:"+port);
            System.out.println("--------------------");
        }
    }
}

//Client_demo

public class Client_Demo {
    //完成UDP通信多发多收--客户端
    public static void main(String[] args) throws IOException {
        //1.创建发送端对象
        DatagramSocket socket=new DatagramSocket();
        Scanner sc=new Scanner(System.in);

        while (true) {
            System.out.println("请输入要发送的数据：");
            String msg=sc.nextLine();

            //如果输入exit则退出

            //2.创建数据，并把数据打包
            byte[] bytes=msg.getBytes();
            DatagramPacket packet=new DatagramPacket(bytes,bytes.length, InetAddress.getLocalHost(),8080);

            //3.发送数据
            socket.send(packet);
        }
    }

}
```



## 第四章 TCP通信

一、TCP 协议基础特性

TCP（传输控制协议）是面向连接的可靠通信协议，是 Java 网络编程的核心基础，关键特性包括：

1. **面向连接**：通信前需通过 “三次握手” 建立客户端与服务端的全双工可靠连接，确保双方通信通道稳定。
2. **可靠传输**：数据发送后需服务端确认接收，若未确认则自动重传，底层保障数据不丢失、不重复、有序到达。
3. **端到端通信**：基于 “客户端 - 服务端” 架构，需明确双方身份（IP + 端口），客户端主动发起连接，服务端被动监听。

 二、Java TCP 通信核心 API

Java 通过 JDK 提供的两个核心类实现 TCP 通信，客户端与服务端分工明确，不可混用：

| 类别   | 核心类                  | 作用与关键方法                                               |
| ------ | ----------------------- | ------------------------------------------------------------ |
| 客户端 | `java.net.Socket`       | 代表客户端通信管道，用于发起与服务端的连接关键方法：<br>构造器：`Socket(String 服务端IP, int 服务端端口)`（建立连接）<br/>`getOutputStream()`：获取字节输出流（发数据）<br/> `getInputStream()`：获取字节输入流（收数据）<br/>`close()`：关闭通信管道 |
| 服务端 | `java.net.ServerSocket` | 代表服务端端口监听对象，用于接收客户端连接请求关键方法：<br/>构造器：`ServerSocket(int 服务端端口)`（注册端口，绑定监听）<br/>`accept()`：阻塞等待客户端连接，返回`Socket`对象（建立与客户端的通信管道）<br/>`close()`：关闭服务端监听 三、TCP 通信核心模型：基于 IO 流的管道传输 |

![image-20251101125841861](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251101125841861.png)TCP 通信本质是 “通过 Socket 管道 + IO 流传输数据”，核心逻辑如下：

1. **建立管道**：客户端通过`Socket`连接服务端`ServerSocket`，双方各自持有`Socket`对象，形成双向通信管道。
2. **数据发送**：发送方从`Socket`获取**字节输出流**（或包装后的高级流），将数据写入流，数据通过管道传输到接收方。
3. **数据接收**：接收方从`Socket`获取**字节输入流**（需与发送方流类型匹配），从流中读取数据，完成接收。
4. **流的匹配原则**：通信严格要求 “发送方用什么流发，接收方就用什么流收”，否则会出现数据读取异常（如半包、乱码）。

四、TCP “一发一收” 实现（基础版）

“一发一收” 是 TCP 通信的入门案例，客户端发送一次数据，服务端接收一次数据，核心步骤分 “客户端开发” 和 “服务端开发” 两部分。

```java
//Server_demo
public class Server_demo {
    public static void main(String[] args) throws Exception {
        System.out.println("服务端启动了");
        //1. 创建一个ServerSocket对象，构造方法中指定端口号
        ServerSocket ss=new ServerSocket(9999);

        //2. 调用accept方法，阻塞等待客户端连接，一旦有客户端连接就会返回一个Socket对象
        Socket socket = ss.accept();

        //3. 通过Socket对象获取输入流，读取数据
        InputStream is=socket.getInputStream();

        //4. 封装成特殊数据流
        DataInputStream dis=new DataInputStream(is);

        //5. 读取数据
        String msg=dis.readUTF();
        int id=dis.readInt();
        System.out.println("id:"+id+"  msg:"+msg);

        //6.客户端的ip和端口
        System.out.println("客户端的ip:"+socket.getInetAddress().getHostAddress());

        socket.close();
    }
}

//Client_demo
public class Client_demo {
    public static void main(String[] args) throws IOException {
        //1. 创建一个Socket对象，构造方法中指定服务器的IP和端口号
        Socket socket=new Socket("127.0.0.1",9999);

        //2. 通过Socket对象获取一个输出流，并使用输出流发送数据
        OutputStream os=socket.getOutputStream();

        //3.包装成特殊数据流
        DataOutputStream dos=new DataOutputStream(os);
        dos.writeUTF("hello,server");
        dos.writeInt(1);

        //4.释放资源
         socket.close();
    }
}

```



- 关键注意事项

  - **启动顺序**：必须先启动服务端（`ServerSocket`监听端口），再启动客户端，否则客户端会抛出 “连接拒绝” 异常。

  - **阻塞特性**：服务端`accept()`会阻塞（等待客户端连接），客户端 / 服务端的流读取方法（如`readInt()`）也会阻塞（等待数据到达），无需担心 “谁跑更快”—— 数据会缓存到 Socket 管道中，直到接收方读取。

  - **端口冲突**：服务端端口若被其他程序占用，会抛出 “端口已使用” 异常，需更换未占用端口（1024~65535 之间，避免知名端口）。

 五、TCP “多发多收” 实现

“一发一收” 仅支持单次通信，“多发多收” 通过**死循环**实现客户端反复发、服务端反复收，核心改造点是 “循环控制” 和 “资源管理”

- **资源复用**：Socket 管道、IO 流仅创建 1 次（放在循环外），避免反复建立连接 / 创建流，减少资源消耗。
- **刷新缓冲区**：未关闭流时，数据会暂存于缓冲区，需调用`flush()`强制写入管道（如客户端`dos.flush()`），否则服务端无法及时接收数据。
- **退出与异常处理**：客户端通过 “输入 exit” 主动退出；服务端通过捕获 “客户端断开异常（EOFException）” 被动退出，避免死循环无法终止。



六、多客户端通信

- **核心问题：单线程服务端的局限性**：原始 TCP 服务端仅依赖 “主线程”，需同时完成 “接收客户端连接” 和 “处理客户端消息” 两个任务：

  - 主线程通过`accept()`方法等待客户端连接，连接成功后会进入 “死循环读取该客户端消息”；

  - 此时主线程被 “绑定” 到当前客户端，无法再接收其他客户端的连接请求，导致**仅支持 1 个客户端通信**。

- 解决方案：多线程架构设计

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251101133104812.png" alt="image-20251101133104812" style="zoom:50%;" />

通过 “主线程 + 子线程” 的分工，实现 “同时接收多客户端连接 + 并行处理消息”，核心是 **“主线程接连接，子线程处理消息”**。

1. 架构模型

| 线程角色 |                           核心职责                           |
| :------: | :----------------------------------------------------------: |
|  主线程  |    仅负责调用`accept()`接收客户端`Socket`连接，不处理消息    |
|  子线程  | 每接收 1 个客户端`Socket`，就创建 1 个独立子线程，专门读取该客户端的消息 |

2. 关键细节：客户端上下线追踪

通过代码增强，可让服务端实时感知客户端的 “上线” 和 “下线” 状态，为后续项目（如即时通讯）奠定基础。

1. 客户端上线追踪

- **触发时机**：主线程调用`accept()`成功获取客户端`Socket`时，代表该客户端上线；
- **实现方式**：在主线程中，获取客户端`Socket`的 IP 地址（`socket.getInetAddress().getHostAddress()`），打印 “XX 客户端上线” 日志。

2. 客户端下线追踪

- **触发时机**：客户端主动关闭`Socket`（或关闭程序）时，服务端子线程读取消息会抛出异常（如`EOFException`、`IOException`），代表客户端下线；
- **实现方式**：在子线程的`try-catch`块中，捕获 “读取消息异常”，打印 “XX 客户端下线” 日志（同样通过`Socket`获取客户端 IP）。

3. 场景与局限性说明

- **适用场景**

  该方案适用于**局域网小型应用**：

  - 客户端数量较少（如几百个），每个客户端对应 1 个线程，计算机内存和 CPU 可承受（JVM 中单个线程栈默认内存较小，几百个线程无压力）。

- **局限性与优化方向**

  - 若客户端数量极多（如几万、几十万），“1 个客户端 1 个线程” 会导致**线程泛滥**，引发内存溢出、上下文切换频繁等问题；
  - 后续优化：使用**线程池**（如`ThreadPoolExecutor`）管理子线程，复用线程资源，避免线程频繁创建和销毁（视频后续课程会讲解）。

七、BS架构

- BS 架构是 Web 开发的基础架构，区别于需手动开发客户端的 CS 架构（客户端 - 服务器架构），其核心特点是**客户端为浏览器（无需开发），仅需开发服务端**，底层依赖 TCP 通信与 HTTP 协议实现数据交互。

1. BS 架构的通信流程

- **请求发起**：浏览器通过`HTTP协议`发起请求，请求地址格式为`http://IP地址:端口号`（如`http://127.0.0.1:8080`）。
  - HTTP 协议：浏览器与服务端的 “沟通规则”，规定请求 / 响应的数据格式。
- **连接建立**：浏览器与服务端通过`TCP Socket管道`建立通信连接（类似 CS 架构的 Socket 通信），主线程负责接收客户端连接，子线程（或线程池）处理具体的数据响应。
- **数据响应**：服务端向浏览器返回符合 HTTP 协议格式的数据，浏览器解析后展示为网页。

2. 服务端响应的 HTTP 协议格式

![image-20251101135136149](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251101135136149.png)

浏览器仅识别符合 HTTP 协议格式的数据，响应格式需严格遵循以下 3 点：

|     格式要求     |                             说明                             |                             示例                             |
| :--------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  第一行：状态行  | 声明 HTTP 协议版本、响应状态码（200 表示 “正常响应”，404 表示 “资源未找到”） |                      `HTTP/1.1 200 OK`                       |
|  第二行：响应头  | 声明响应内容的类型、编码等元信息，核心头字段为`Content-Type`（指定内容类型） | `Content-Type: text/html; charset=UTF-8`（表示内容为 HTML 文本，编码为 UTF-8，避免乱码） |
|     空行分隔     | 响应头与网页内容之间必须加**单独空行**（作为格式分隔符，缺一不可） |                              -                               |
| 后续内容：响应体 |  实际的网页内容（HTML 代码），可包含标签、样式、图片链接等   | `<html><body><h1 style="color:red">听我讲Java</h1></body></html>` |

**关键点**

- 服务端需从 Socket 管道中获取`字节输出流`，并包装为`PrintStream`（打印流），方便自动换行（HTTP 协议要求每行数据需换行，打印流的`println()`方法可直接实现）；
- 响应完成后需关闭 Socket 管道（BS 架构为 “短连接”，请求响应后立即断开，再次请求需重新建立连接）。

 二、BS 架构服务端的代码实现（核心步骤）

服务端开发仅需关注 “接收浏览器连接” 与 “返回 HTTP 格式数据”，无需开发客户端，核心代码流程如下：

1. 基础服务端实现（单线程处理单连接）

```java
// 1. 创建ServerSocket对象，绑定8080端口
ServerSocket serverSocket = new ServerSocket(8080);
while (true) {
    // 2. 主线程循环接收浏览器连接（阻塞式）
    Socket socket = serverSocket.accept();
    // 3. 启动子线程处理当前连接（避免主线程阻塞，支持多浏览器同时请求）
    new Thread(() -> {
        try {
            // 4. 获取打印流（包装字节输出流，方便换行）
            PrintStream ps = new PrintStream(socket.getOutputStream());
            // 5. 按HTTP格式写入响应数据
            ps.println("HTTP/1.1 200 OK"); // 状态行
            ps.println("Content-Type: text/html; charset=UTF-8"); // 响应头
            ps.println(); // 空行分隔
            ps.println("<html><head><title>BS架构演示</title></head>"); // 响应体（HTML）
            ps.println("<body><h1 style='font-size:20px;color:red'>听我讲Java</h1></body></html>");
            // 6. 关闭流与连接（短连接）
            ps.close();
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }).start();
}
```

2. 线程池优化（解决 “线程泛滥” 问题）

上述基础实现存在**性能隐患**：若同时有 1000 个浏览器请求，会创建 1000 个线程，线程创建 / 销毁的开销大，且大量线程会占用内存资源。而 BS 架构的请求为 “短任务”（响应网页后立即结束），**线程池**是最优解决方案。

 **线程池优化的核心优势**

- 控制线程数量：避免线程过多导致的资源耗尽；
- 线程复用：线程处理完一个任务后，无需销毁，可继续处理下一个任务，减少创建 / 销毁开销；
- 任务队列：当请求数超过线程池处理能力时，请求会进入队列等待，避免直接拒绝。

**线程池优化的代码实现**

1. 创建线程池：使用`ThreadPoolExecutor`手动创建
2. **包装任务**：将 Socket 连接包装为`Runnable`任务，提交给线程池处理（替代 “一个连接一个线程” 的模式）。

```java
// 1. 手动创建线程池（核心参数按需调整）
ExecutorService threadPool = new ThreadPoolExecutor(
    3, // 核心线程数
    10, // 最大线程数
    10, // 空闲线程存活时间（秒）
    TimeUnit.SECONDS,
    new ArrayBlockingQueue<>(100), // 任务队列（容量100）
    Executors.defaultThreadFactory(), // 线程工厂
    new ThreadPoolExecutor.AbortPolicy() // 任务拒绝策略（队列满时抛异常）
);

// 2. 主线程接收连接，提交任务给线程池
ServerSocket serverSocket = new ServerSocket(8080);
while (true) {
    Socket socket = serverSocket.accept();
    // 3. 将Socket包装为Runnable任务，提交给线程池
    threadPool.submit(() -> {
        try {
            PrintStream ps = new PrintStream(socket.getOutputStream());
            // 按HTTP格式响应数据（同基础实现）
            ps.println("HTTP/1.1 200 OK");
            ps.println("Content-Type: text/html; charset=UTF-8");
            ps.println();
            ps.println("<html><body><h1>线程池优化的BS服务端</h1></body></html>");
            ps.close();
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    });
}
```