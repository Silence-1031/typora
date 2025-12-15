# Java高级

## 第一章 文件File

- 临时存储 vs 持久化存储

1. 现有临时存储方案的不足:之前学习的变量、数组、集合、对象等存储容器有很多不足

- **存储位置**：数据仅存于**内存（内存条）** 中，属于 “临时存储容器”；
- **数据安全性**：程序结束、电脑断电后，内存中的数据会**完全丢失**；

2. 持久化存储:为实现数据长久保存，需将数据存储到**磁盘（硬盘）** 中，核心技术包括：

- **File 类**：用于操作 “磁盘中的文件 / 文件夹” 本身（如创建、删除、获取文件信息）；
- **IO 流**：用于读写 “文件中的数据”（File 类无法操作文件内容，需 IO 流配合）。

### 第一节、File 类

File 类是 Java IO 包下的核心类，专门用于代表和操作**操作系统中的文件 / 文件夹**

一 File 类的本质

- **代表范围**：File 类的**对象**可代表两种载体 ——**具体文件**（如`test.txt`）和**文件夹（目录）**（如`D:\java\data`）（广义上 “文件” 包含文件和文件夹）；
- **核心作用**：仅操作 “文件 / 文件夹本身”，不涉及 “文件内部的数据读写”。

二\ File 对象的创建方式

- 核心是通过构造方法传入 “文件 / 文件夹路径” 创建对象，最常用以下方式

```java
// 方式1：直接传入完整路径（绝对/相对路径均可）
File file = new File("路径字符串");

//路径可以是不存在的
```

- 路径分隔符注意：
  - Windows 系统用`\`，但 Java 中需转义（写为`\\`），例如`"E:\\resource\\test.txt"`
  - 跨平台推荐用`/`（Java 支持），例如`"E:/resource/test.txt"`

三\绝对路径与相对路径

|   类型   |               定义               |                  特点                  |               适用场景                |
| :------: | :------------------------------: | :------------------------------------: | :-----------------------------------: |
| 绝对路径 | 带盘符的完整路径（如`E:/a.txt`） |        定位精确，与工程位置无关        | 操作电脑中**非项目内**的文件 / 文件夹 |
| 相对路径 | 不带盘符的路径（如`src/a.txt`）  | 默认从**当前 IDEA 工程根目录**开始查找 |       操作项目内的文件 / 文件夹       |

- 优势：相对路径可避免路径 “写死”，当项目迁移到其他电脑时，无需修改路径代码（绝对路径会因盘符 / 目录结构变化失效）。

 四、File 类核心功能：文件操作

1. 判断文件状态（常用方法）

| 方法名              | 返回值  | 功能描述                                  |
| ------------------- | ------- | ----------------------------------------- |
| `exists()`          | boolean | 判断 File 对象代表的文件 / 文件夹是否存在 |
| `isFile()`          | boolean | 判断是否为**文件**（非文件夹）            |
| `isDirectory()`     | boolean | 判断是否为**文件夹**（非文件）            |
| `getName()`         | String  | 获取文件 / 文件夹的 “名称（含后缀）”      |
| `length()`          | long    | 获取文件大小（单位：字节），文件夹无效    |
| `getAbsolutePath()` | String  | 获取文件 / 文件夹的**绝对路径**           |

2. 创建文件

- 方法：`createNewFile()`
- 说明：
  1. 仅能创建 “文件”，不能创建文件夹；
  2. 需处理`IOException`（可抛出或捕获）；
  3. 若文件已存在，调用该方法返回`false`（创建失败）。

```java
File file = new File("src/test.txt");
if (!file.exists()) {
    boolean isCreated = file.createNewFile(); // 需抛IOException
    System.out.println("文件是否创建成功：" + isCreated);
}
```

 五、File 类核心功能：文件夹操作

1. 创建文件夹（关键区分一级 / 多级）

| 方法名     | 功能                       | 注意事项                                 |
| ---------- | -------------------------- | ---------------------------------------- |
| `mkdir()`  | 创建**一级**文件夹         | 若父文件夹不存在，创建失败（返回 false） |
| `mkdirs()` | 创建**多级**文件夹（推荐） | 自动创建不存在的父文件夹，适用性更广     |

- 示例：

```java
// 1. 创建一级文件夹（需确保parent已存在）
File dir1 = new File("E:/resource/aa");
dir1.mkdir(); // 若resource不存在，创建失败

// 2. 创建多级文件夹（无需关心父文件夹是否存在）
File dir2 = new File("E:/resource/bb/cc/dd");
dir2.mkdirs(); // 自动创建bb、cc、dd，无论resource是否存在
```

2. 删除文件夹 / 文件

- 方法：`delete()`
- 核心注意事项：
  - 可删除**文件**或**空文件夹**；
  - **只能删除空文件夹**；
  - 删除后**不进回收站**，直接永久删除，需谨慎操作；
  - 若文件 / 文件夹不存在，返回`false`（删除失败）。
- 示例：

```java
File file = new File("src/test.txt");
File emptyDir = new File("E:/resource/aa");
file.delete(); // 删除文件
emptyDir.delete(); // 删除空文件夹
```

 六、文件夹遍历

用于获取文件夹下的 “一级文件 / 文件夹”（无法直接获取多级）：

| 方法名        | 返回值类型 | 功能描述                               | 优势                                                         |
| ------------- | ---------- | -------------------------------------- | ------------------------------------------------------------ |
| `list()`      | String[]   | 获取文件夹下所有一级内容的 “名称”      | 仅需名称时简单高效                                           |
| `listFiles()` | File[]     | 获取文件夹下所有一级内容的 “File 对象” | 可进一步调用 File 方法（如判断类型、删除），功能更强（推荐） |

- 示例：

```java
File dir = new File("E:/resource");

// 1. 获取一级内容名称
String[] names = dir.list();
for (String name : names) {
    System.out.println("名称：" + name);
}

// 2. 获取一级内容的File对象（可后续操作）
File[] files = dir.listFiles();
for (File f : files) {
    if (f.isFile()) {
        System.out.println("文件：" + f.getName());
    } else {
        System.out.println("文件夹：" + f.getName());
    }
}
```

- 遍历的注意事项

调用`list()`或`listFiles()`时，需满足前提条件，否则返回`null`或空数组：

1. **主调必须是文件夹且路径存在**：若 File 对象代表的路径不存在，返回`null`；
2. 主调是一个有内容的文件夹,会将里面所有的一级文件和文件夹路径放在File数组中返回
3. 当主调是一个文件夹，且里面有隐藏文件时，将里面所有文件和文件夹的路径放在File数组中返回，包含隐藏文件
4. **空文件夹处理**：若文件夹为空，返回 “长度为 0 的数组”（非`null`）；
5. **权限问题**：若文件夹无访问权限（如系统目录），返回`null`。

> 主调 : 调用方法的对象实例,比如,在file.listFiles() 调用中，file 对象就是主调

### 第二节 方法递归

一\递归的本质

- 递归是**程序设计中广泛使用的算法**，表现为**方法调用自身的形式**（核心特征），英文对应 “recursion”。
- **本质**：将复杂问题拆解为 “规模更小但逻辑相同” 的子问题，直到子问题达到可直接求解的 “终结点”，再反向推导得到原问题答案

- 在本章的应用场景:

  - **多级文件搜索**：需遍历文件夹下的所有内容，而文件夹可能嵌套文件夹，需递归逐层处理。

  - **删除非空文件夹**：Java 默认无法直接删除非空文件夹，需先递归删除文件夹内所有子文件 / 子文件夹，再删除空文件夹。

二、递归的两种形式

|   类型   |                       定义                       |
| :------: | :----------------------------------------------: |
| 直接递归 |             一个方法**直接调用自身**             |
| 间接递归 | 一个方法调用其他方法，**其他方法再回调用原方法** |

三、递归死循环与栈溢出

1. 递归死循环的成因

若递归未设置 “终止条件”，会导致方法**无限调用自身（或间接调用自身）**，形成死循环。

2. 死循环的后果：栈内存溢出（StackOverflowError）

- **原理**：Java 中方法的执行依赖 “方法栈”（内存区域），每调用一次方法，就会在栈中创建一个 “方法栈帧”（存储方法执行状态）；若方法无限递归调用，栈帧会**无限累积**，最终耗尽栈内存。
- **表现**：程序抛出`StackOverflowError`（属于严重错误，无法通过异常处理机制恢复），视频中运行示例代码时直接触发该错误。

四、递归算法的三要素

视频中明确归纳了递归必须满足的三个关键条件，缺一不可，否则会出现无限递归（栈溢出）：

![image-20251027195010630](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251027195010630.png)

1. **递归公式（核心逻辑）**

   找到原问题与子问题的数学 / 逻辑关系，用公式或表达式描述

2. **终结点（终止条件）**

   确定子问题的 “最小规模”，此时无需继续递归，可直接返回结果（避免无限递归）。

   - 阶乘：`F(1) = 1`（1 的阶乘是 1）

3. **递归方向（收敛性）**

   确保每次递归调用时，子问题的规模**向终结点靠近**，而非远离。例如：

   - 求`F(5)`（5 的阶乘）时，递归方向是`F(5)→F(4)→F(3)→F(2)→F(1)`（逐渐靠近终结点 F (1)）；

 五、递归与循环的对比

| 维度       | 递归                               | 循环（如 for/while）         |
| ---------- | ---------------------------------- | ---------------------------- |
| 代码简洁性 | 代码短，逻辑直观（符合人类思维）   | 代码稍长，需手动控制循环条件 |
| 执行效率   | 较低（栈操作开销大，可能重复计算） | 较高（无栈开销）             |
| 适用场景   | 复杂拆解问题（树形、嵌套结构）     | 简单重复迭代（如累加、遍历） |
| 调试难度   | 较难（栈帧多，调用链长）           | 较易（单线程线性执行）       |

六\文件搜索

1. 定义核心搜索方法

```java
/**
 * 搜索文件
 * @param dir 要搜索的文件夹（目录）
 * @param fileName 要搜索的目标文件名（可支持模糊匹配）
 */
public static void searchFile(File dir, String fileName) {
    // 方法体见后续步骤
}
```

2. 前置校验：规避异常场景

代码严谨性第一步 —— 先判断入参合法性，避免空指针、路径不存在等异常：

- 校验 1：`dir == null`（防止外部传参为`null`）；
- 校验 2：`!dir.exists()`（路径不存在则无需搜索）；
- 校验 3：`!dir.isDirectory()`（若传入的是文件而非文件夹，则无法继续搜索）。

满足任一校验则直接返回，不执行后续逻辑：

```java
if (dir == null || !dir.exists() || !dir.isDirectory()) {
    return; // 非法场景，直接结束
}
```

3. 获取文件夹下一级内容

通过`File.listFiles()`方法获取当前文件夹下的**所有一级文件 / 子文件夹对象**（注意：该方法仅能获取 “一级内容”，无法直接获取子文件夹的子内容）：

```java
File[] files = dir.listFiles(); // 返回一级文件/文件夹数组
```

4. 规避空数组 / 无权限场景

`listFiles()`存在两种特殊情况，需提前处理否则会报异常：

- 情况 1：文件夹无读取权限（如系统隐藏文件夹），返回`null`；
- 情况 2：文件夹为空（无任何一级内容），返回空数组（长度为 0）。

**处理逻辑**：先判断数组非`null`，再判断数组长度 > 0，避免空指针异常：

```java
// 先判null再判长度，顺序不可颠倒（否则null数组取length会报空指针）
if (files != null && files.length > 0) {
    // 有合法内容，执行遍历
}
```

5. 遍历 + 判断 + 递归

遍历一级文件对象，分两种情况处理（文件 / 文件夹）：

（1）若为文件：判断是否目标文件

- 精确匹配：使用`file.getName().equals(fileName)`（需完全一致，如`QQ.exe`）；

- 模糊匹配：使用`file.getName().contains(fileName)`（包含关键字即可，如输入`QQ`可匹配`QQ.exe`、`QQSetup.exe`）；

- 匹配成功则输出文件绝对路径（`file.getAbsolutePath()`）：

  ```java
  for (File file : files) {
      if (file.isFile()) { // 判断是否为文件（非文件夹）
          // 模糊匹配目标文件名
          if (file.getName().contains(fileName)) {
              System.out.println("找到目标文件：" + file.getAbsolutePath());
          }
      }
  ```

  

（2）若为文件夹：递归调用搜索方法

若遍历到的是子文件夹，则将该子文件夹作为 “新的搜索目录”，再次调用`searchFile()`方法，重复整个搜索流程：

```java
      else { // 是文件夹，递归搜索该子文件夹
          searchFile(file, fileName); // 子文件夹作为新dir，目标文件名不变
      }
  }
```

### 第三节 字符集

 一、字符集的核心作用

计算机底层仅能存储二进制（0/1），而人类使用的字符（文字、符号等）是 “明文”，**字符集本质是 “字符与二进制的映射规则”**,实现明文与字符集转换,分为两步：

1. **编码**：明文字符 → 二进制（存储 / 传输）；
2. **解码**：二进制 → 明文字符（读取 / 展示）。

二、核心字符集

|  字符集   |        设计背景        |             支持字符范围              |                       编码规则（核心）                       |           兼容性            |
| :-------: | :--------------------: | :-----------------------------------: | :----------------------------------------------------------: | :-------------------------: |
| **ASCII** | 美国设计，解决英文存储 | 英文字母、数字、基础符号（共 128 个） |                1 个字节存储，**首位固定为 0**                | 所有字符集的基础（必兼容）  |
|  **GBK**  | 中国设计，解决汉字存储 |     2 万 + 汉字、兼容 ASCII 字符      | 1. 英文 / 数字：1 个字节（同 ASCII）；<br>2. 汉字：2 个字节，**第一个字节首位固定为 1** | 兼容 ASCII，仅支持中 / 英文 |
| **UTF-8** | 国际通用，解决全球字符 |     全球所有文字、符号（含表情）      | 1. 英文 / 数字：1 个字节（同 ASCII）；<br>2. 汉字：3 个字节<br>3. 其他特殊字符：2~4 个字节（可变长） |  兼容 ASCII，支持全球字符   |

- UTF-8 的 “可变长优势”
  - 避免了 UTF-32（固定 4 字节）的 “空间浪费”
  - 通过 “前缀码” 区分字节数：1 字节以`0`开头，2 字节以`110`开头，3 字节以`1110`开头，4 字节以`11110`开头，后续字节均以`10`开头，彻底解决解析歧义。

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251027202019893.png" alt="image-20251027202019893" style="zoom:67%;" />

三、乱码问题的根源与解决

乱码的核心原因只有一个：**编码时使用的字符集 ≠ 解码时使用的字符集**

1. 乱码案例（GBK 编码 → UTF-8 解码）

- 编码过程（GBK）：汉字 “我”→ 2 个字节（首位为 1）；英文 “A”→ 1 个字节（首位为 0）；
- 解码过程（UTF-8）：
  - 读取 “A” 的 1 字节（首位 0）：正常解析为 “A”；
  - 读取 “我” 的 2 字节（首位 1）：UTF-8 要求 3 字节解析汉字，无法识别这 2 个字节，最终显示 “问号（？）” 或乱码符号。

2. 乱码的 “例外情况”

**英文 / 数字永远不会乱码**，因为所有字符集都 “兼容 ASCII”—— 英文 / 数字的编码规则完全一致（1 字节，首位 0），无论用哪种字符集解码，结果都相同。

3. 乱码解决方案

开发中必须保证 “编码与解码字符集一致”，推荐优先使用**UTF-8**（原因：支持全球字符，避免跨语言 / 跨地区的乱码问题，是 IDEA、操作系统、网页的默认编码）。

四\编码与解码的定义

在 Java 中，字符（`String`）与字节（`byte[]`）的转换是字符集操作的核心，两个关键动作的定义如下：

- 编码（Encode）：将字符串（文字信息)转换为字节数组的过程。

  用途：常用于数据传输（如网络通信）、数据存储，需指定字符集确保接收方可正确解析。

- 解码（Decode）：将字节数组转换回字符串的过程。

  用途：接收方获取字节数据后，通过解码还原为原始文字，需与编码时使用的字符集一致，否则会出现乱码。

五、Java 中实现编码的核心方法

Java 通过`String`类的`getBytes()`方法实现编码，支持 “平台默认字符集” 和 “指定字符集” 两种方式，具体用法如下：

|               方法签名                |            作用            |                             特点                             |
| :-----------------------------------: | :------------------------: | :----------------------------------------------------------: |
|          `byte[] getBytes()`          | 使用**平台默认字符集**编码 | 无需指定字符集，依赖运行环境（如 Windows 默认 GBK、Linux/macOS 默认 UTF-8） |
| `byte[] getBytes(String charsetName)` |   使用**指定字符集**编码   |    需传入字符集名称（如`"UTF-8"`、`"GBK"`），编码规则固定    |

六、Java 中实现解码的核心方法

Java 通过`String`类的构造器实现解码，同样支持 “平台默认字符集” 和 “指定字符集” 两种方式，具体用法如下：

|                 构造器签名                 |            作用            |              特点              |
| :----------------------------------------: | :------------------------: | :----------------------------: |
|           `String(byte[] bytes)`           | 使用**平台默认字符集**解码 |  无需指定字符集，依赖运行环境  |
| `String(byte[] bytes, String charsetName)` |   使用**指定字符集**解码   | 需传入与编码时一致的字符集名称 |

### 第四节 IO流

#### 一、**概述**

1. 概念：IO 流：“I” 即`Input`（输入流），“O” 即`Output`（输出流），“流” 指数据在不同存储位置间的 “流动” 过程，本质是**数据读写的技术工具**。

- 核心作用：解决 “程序（内存）与外部存储 / 设备” 间的数据传输问题（如磁盘文件、网络数据、用户输入等），因为程序运行在内存中，需通过 IO 流获取外部数据或持久化内部数据。

2. IO 流的两大分类维度

 **维度 1：按 “数据流动方向” 划分**

| 类型             | 核心功能                         | 数据流向        | 典型场景               |
| ---------------- | -------------------------------- | --------------- | ---------------------- |
| 输入流（Input）  | 从外部读取数据到内存（程序）     | 外部存储 → 内存 | 打开文件、读取网络消息 |
| 输出流（Output） | 从内存（程序）写出数据到外部存储 | 内存 → 外部存储 | 保存文件、发送网络消息 |

 **维度 2：按 “传输数据的最小单位” 划分**

| 类型   | 数据单位               | 适用场景                             | 局限性                             |
| ------ | ---------------------- | ------------------------------------ | ---------------------------------- |
| 字节流 | 字节（1byte）          | 所有类型文件（文本、图片、视频等）   | 操作文本时需手动处理字符集，易乱码 |
| 字符流 | 字符（如 'a'、' 中 '） | 仅纯文本文件（.txt、.java、.xml 等） | 无法操作非文本文件（如图片会损坏） |

- 原理说明：

  所有文件（包括图片、视频）本质都是 “字节组成的二进制文件”，因此字节流可通用；而字符流是 “字节流 + 字符集编码” 的封装，仅能识别文本的字符编码规则（如 UTF-8、GBK），故仅限文本操作。

3. IO 流的四大核心类型

|  组合类型  |             核心能力             |              适用场景              |
| :--------: | :------------------------------: | :--------------------------------: |
| 字节输入流 |  以字节为单位读取外部数据到内存  | 读取图片、视频、压缩包等非文本文件 |
| 字节输出流 | 以字节为单位将内存数据写出到外部 |   复制非文本文件、保存二进制数据   |
| 字符输入流 | 以字符为单位读取纯文本数据到内存 |   读取.txt、.java 等文本文件内容   |
| 字符输出流 | 以字符为单位将文本数据写出到外部 |     写入日志文件、编辑文本内容     |

4. IO 流的常见应用场景

- **文件操作**：记事本保存内容（输出流）、打开文件（输入流）；
- **数据持久化**：游戏保存最高分（输出流）、下次启动读取分数（输入流）；
- **文件复制**：先通过字节输入流读取原文件，再通过字节输出流写入新文件；
- **网络通信**：发送消息（输出流写数据到网络）、接收消息（输入流读网络数据）。

![image-20251028183536357](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251028183536357.png)

#### 二、**文件字节输入流（FileInputStream）**

1. 是`InputStream`的**文件操作实现类**，核心作用：**将磁盘文件中的数据以 “单个字节” 为单位，读取到内存中**（仅支持文件，不支持网络数据读取）

2. FileInputStream 核心用法（3 种读取方式）

**方式 1：每次读取 1 个字节**

- 通过`read()`方法读取：每次返回 1 个字节的 ASCII 码值（如字符`'A'`返回 97）；若已读完所有数据，返回`-1`（终止标志）。
- 需通过循环读取，直到`read()`返回`-1`结束。

```java
public static void main(String[] args) throws IOException {
        //1.创建文件字节输入流管道与源文件接通
        InputStream is =new FileInputStream("day-03\\src\\test.txt");

        //2.开始读取文件中的字节并输出
        //定义一个变量记住每次读取的一个字节
        int b;
        while((b=is.read())!=-1)
        {
            System.out.print((char)b);
        }
```

- 缺点：
  - 性能极差：频繁与磁盘交互；
  - 中文乱码：中文在 UTF-8 中占 3 个字节，逐字节读取会 “截断中文字节”（如`'我'`拆成 3 个单独字节，转字符后乱码）。

**方式 2：每次读取多个字节**

- 通过`read(byte[] buffer)`方法读取：用 “字节数组” 作为 “桶”，每次读取`buffer.length`个字节，返回**实际读取的字节数**（若未装满桶，返回实际数量；读完返回`-1`）。
- 关键：输出时需按 “实际读取数量” 截取数组（避免读取到上一轮的残留数据）。

```java
//2.开始读取文件中的字节并输出：每次读取多个字节
        //定义一个字节数组用于每次读取字节
        byte[] buffer=new byte[3];
        //定义一个变量记住每次读取了多少个字节
        int len;
        while((len=is.read(buffer))!= -1)
        {
            System.out.print(new String(buffer,0,len));
        }
```



- **优点**：减少磁盘与内存的交互次数，性能比 “逐字节读取” 大幅提升。
- **缺点**：仍可能中文乱码 —— 若 “桶大小” 不是 3 的倍数（UTF-8 中文占 3 字节），仍会截断中文字节（如`'我'`的 3 个字节被拆到 2 个 “桶” 中）。

**方式 3：一次性读取全部字节（小文件专用）**

- 通过 JDK1.8 + 提供的`readAllBytes()`方法：直接将文件所有字节读入一个字节数组（无需循环），返回完整字节数组。
- 本质：创建与 “文件大小相同的字节数组”，一次性装完所有数据，避免截断中文字节。

```java
import java.io.FileInputStream;
import java.io.IOException;

public class FileInputStreamDemo3 {
    public static void main(String[] args) throws IOException {
        // 1. 创建流对象
        FileInputStream fis = new FileInputStream("src/地雷04.txt"); // 含中文的文件
        
        // 2. 一次性读取全部字节，返回完整字节数组
        byte[] allBytes = fis.readAllBytes();
        
        // 3. 直接转字符串输出（无截断，中文正常）
        System.out.print(new String(allBytes));
        
        fis.close();
    }
}
```

- **缺点**：内存溢出风险 —— 若文件过大（如 100GB），创建的 “字节数组” 会超出内存容量，抛出`OutOfMemoryError`，仅适合**小文件**（如配置文件、文本日志）。

3. **FileInputStream 的适用场景**

- 适合**二进制文件**（如图片、视频、音频）的读取 / 复制（二进制数据无 “字符编码” 问题，字节完整即可还原）；
- 不适合**文本文件**（尤其是含中文的文本）—— 推荐用字符流（FileReader），从根源解决中文乱码。

#### 三、文件字节输出流

FileOutputStream 是 Java IO 流中**字节输出流**的核心实现类，其核心作用是：

- 以**内存为基准**，将内存中的**字节数据**以字节形式**写入到磁盘文件**中，是完成 “内存→磁盘” 数据持久化的基础工具。

- 本质：通过建立 “内存与目标文件” 的输出管道，提供写字节数据的方法，底层自动处理与磁盘的交互逻辑。

**使用步骤：**

遵循 “**建立管道→写字节数据→关闭管道**” 的三步流程

1. 第一步：建立输出管道（创建 FileOutputStream 对象）

|                       构造器格式                        |                      作用                      |                           关键特点                           |
| :-----------------------------------------------------: | :--------------------------------------------: | :----------------------------------------------------------: |
|         `new FileOutputStream(String filePath)`         |       建立到`filePath`路径文件的输出管道       | 1. 若目标文件不存在，会**自动创建**（区别于输入流必须文件存在）；2. 若文件已存在，会**清空原有内容**（覆盖模式） |
|            `new FileOutputStream(File file)`            |       建立到`File`对象对应文件的输出管道       | 功能与上一构造器一致，仅参数类型为`File`对象，适用于已存在`File`对象的场景 |
| `new FileOutputStream(String filePath, boolean append)` | 带追加模式的管道（`append`为`true`时启用追加） | 1. `append=true`：数据追加到文件末尾，不覆盖原有内容；2. `append=false`：等同于基础构造器（覆盖模式），默认值为`false` |
|    `new FileOutputStream(File file, boolean append)`    |      带追加模式的管道（参数为`File`对象）      |          功能与上一构造器一致，参数类型为`File`对象          |

2. 第二步：写入字节数据

 （1）写单个字节：`void write(int b)`：将单个字节写入文件（参数`int b`的低 8 位为有效字节，高 24 位会被忽略）。

 （2）写字节数组：`void write(byte[] b)`：将整个字节数组`b`中的数据一次性写入文件，效率高于单字节写入（减少 IO 交互次数）。

（3）写字节数组片段：`void write(byte[] b, int off, int len)`：仅写入字节数组`b`中 “从索引`off`开始的`len`个字节”，适用于只需要部分数据写入的场景。

3. 第三步：关闭管道（`void close()`）

- 必须操作：流使用完毕后，**必须调用`close()`关闭**，否则会导致资源泄漏。

1. 流底层占用**硬件资源**（如内存与磁盘之间的总线、CPU 资源）；
2. 磁盘速度远慢于内存，若不关闭流，会长期占用资源，影响其他程序的 IO 操作；
3. 关闭流时，会自动刷新缓冲区（若有），确保数据全部写入磁盘（避免数据残留内存）。

```java
package FilePutStream;

import java.io.*;
import java.nio.charset.StandardCharsets;

public class demo1 {
    public static void main(String[] args) throws IOException {
       //1.创建文件字节输出流管道与目标文接通
        OutputStream os=new FileOutputStream("day-03\\src\\test1.txt",true);//true代表文件为追加append模式
        
        
        //2.写数据
        os.write(97);
        os.write('c');
        os.write('我');

        byte[] bytes={65,66,67,68,69,70};
        os.write(bytes);

        os.write("\r\n".getBytes());//换行符
        byte[] bytes1="我爱你中国66".getBytes();
        os.write(bytes1);
        os.write(bytes1,0,3);

        os.close();
    }
}
```

#### 四、文件复制

![image-20251028193023786](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251028193023786.png)

1. 核心逻辑

- 字节流是 Java IO 中处理**任意文件**的基础（所有文件底层均由字节组成），其复制本质是 “一字不漏地转移源文件的全部字节”

- **关键优势**：无关文件格式（图片、视频、文档等均可复制），只要复制前后文件格式一致，即可保证文件完整性（不会损坏）。

```java
public class CopyDemo {
    public static void main(String[] args) throws IOException {
        // 1. 定义源路径和目标路径
        String srcPath = "E:/JT.jpg";
        String destPath = "D:/JT_copy.jpg";
        
        // 2. 创建字节流对象
        FileInputStream fis = new FileInputStream(srcPath); // 输入流（读）
        FileOutputStream fos = new FileOutputStream(destPath); // 输出流（写）
        
        // 3. 循环读写字节
        byte[] buffer = new byte[1024]; // 定义“桶”（1024字节）
        int len; // 记录每次实际读取的字节数
        while ((len = fis.read(buffer)) != -1) { // 读：直到返回-1（读完）
            fos.write(buffer, 0, len); // 写：仅写入实际读取的字节
        }
        
        // 临时关闭（存在风险，下文会优化）
        fos.close();
        fis.close();
        System.out.println("复制完成");
    }
}
```

2. IO 资源关闭的 3 种方案

IO 流属于 “稀缺资源”（如文件句柄、网络连接），若不主动关闭，会导致资源泄漏（占用系统内存，甚至无法再次操作文件）

**方案 1：直接关闭（不推荐）**

- 实现方式：在代码末尾直接调用 `close()` 方法（如上述示例）；
- 致命问题：若代码中间抛出异常（如文件路径错误、读写失败），程序会直接跳转到异常处理逻辑，`close()` 方法无法执行，导致资源泄漏。

 **方案 2：try-catch-finally（传统安全方案）**

`finally` 块的核心特性是 “**无论 try 块正常执行还是抛出异常，都会执行**”（除非虚拟机崩溃），因此适合用于资源释放。

- 步骤 1：在 `try` 外声明流对象（初始化为 `null`），保证 `finally` 块可访问；
- 步骤 2：在 `try` 内创建流对象并执行读写逻辑；
- 步骤 3：在 `finally` 内关闭流，需先判断流对象是否为 `null`（避免空指针异常），且每个流的关闭需单独处理异常（防止一个流关闭失败影响另一个）。

```java
public class CopyDemo {
    public static void main(String[] args) {
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            // 1. 路径与流对象创建
            String srcPath = "E:/JT.jpg";
            String destPath = "D:/JT_copy.jpg";
            fis = new FileInputStream(srcPath);
            fos = new FileOutputStream(destPath);
            
            // 2. 循环读写
            byte[] buffer = new byte[1024];
            int len;
            while ((len = fis.read(buffer)) != -1) {
                fos.write(buffer, 0, len);
            }
            System.out.println("复制完成");
        } catch (IOException e) {
            e.printStackTrace(); // 捕获异常并打印
        } finally {
            // 3. 关闭资源（先关输出流，再关输入流，避免数据未写入）
            try {
                if (fos != null) fos.close(); // 先判断非null，再关闭
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (fis != null) fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

- 优点：绝对安全，能覆盖所有异常场景；
- 缺点：代码臃肿（finally 块需嵌套 try-catch），可读性差。

**方案 3：try-with-resources**

JDK7 引入的语法糖，核心是 “**将资源定义在 try 后的小括号中，程序结束后自动调用资源的 close () 方法**”，无需手动关闭，代码简洁且安全。

核心要求

- 资源类必须实现 `AutoCloseable` 接口（Java IO 的所有流类均已实现，如 `FileInputStream`、`FileOutputStream`）；

![image-20251029132642337](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251029132642337.png)

- 资源定义在 `try(...)` 内，多个资源用分号分隔。

```java
public class CopyDemo {
    public static void main(String[] args) {
        String srcPath = "E:/JT.jpg";
        String destPath = "D:/JT_copy.jpg";
        
        // 资源定义在try()中，自动关闭
        try (FileInputStream fis = new FileInputStream(srcPath);
             FileOutputStream fos = new FileOutputStream(destPath)) {
            
            // 循环读写逻辑不变
            byte[] buffer = new byte[1024];
            int len;
            while ((len = fis.read(buffer)) != -1) {
                fos.write(buffer, 0, len);
            }
            System.out.println("复制完成");
        } catch (IOException e) {
            e.printStackTrace(); // 统一捕获异常
        }
    }
}
```

 核心特性：

- 自动关闭：无论 try 块正常执行还是抛出异常，JVM 都会自动调用资源的 `close()` 方法；
- 关闭顺序：多个资源时，**后定义的资源先关闭**（符合 IO 流 “先关输出流、再关输入流” 的规范）；
- 简洁性：省去 finally 块，代码行数减少 50% 以上，可读性大幅提升。

#### 五、文件字符输入流

1. **字节流 vs 字符流**

   - 字节流（如`FileInputStream`）：以 “字节” 为最小单位读写，适用于**所有文件类型**（图片、视频、文本等），但读取中文等多字节字符时易出现乱码（可能截断字节）。
   - 字符流：以 “字符” 为最小单位读写（1 个字符对应 1~3 个字节，取决于编码如 UTF-8），**仅适用于文本文件**（.txt、.java、.xml 等），能避免中文乱码问题。

2. **核心优势**

   - 直接操作 “字符”，无需手动处理编码转换（底层自动关联编码表）；
   - 提供字符串、字符数组等便捷 API，处理文本更高效。

3. **FileReader（文件字符输入流）**：以 “内存为基准”，将磁盘文本文件以字符形式**读取到内存（程序）中**，是字符输入流的基础实现类。

   - 关键 API（构造器 + 读取方法）

   |   类别   |           具体 API            |                             说明                             |
   | :------: | :---------------------------: | :----------------------------------------------------------: |
   |  构造器  | `FileReader(String filePath)` |        直接通过文件路径创建流对象，与目标文件建立连接        |
   |          |    `FileReader(File file)`    |         通过`File`对象创建流对象，需先构建`File`实例         |
   | 读取方法 |         `int read()`          | 每次读取 1 个字符的 “Unicode 编码值”（返回`int`），无字符可读时返回`-1`； |
   |          |   `int read(char[] buffer)`   | 每次读取多个字符到`char`数组（缓冲区），返回 “实际读取的字符数”；无字符时返回`-1`，避免频繁 IO 提升性能。 |

   - 读取文本文件

   ```java
   // 1. 构建FileReader流对象（try-with-resources自动关闭流，JDK7+特性）
   try (FileReader fr = new FileReader("src/demo06.txt")) {
      day03fileio\\src\\dLei06.txt // 缓冲区（建议1024或2048，平衡性能与内存）
       int readCount; // 记录每次实际读取的字符数
       
       // 2. 循环读取：当readCount=-1时表示读取完毕
       while ((readCount = fr.read(buffer)) != -1) {
           // 3. 转换为字符串（仅取实际读取的字符，避免缓冲区多余空字符）
           System.out.print(new String(buffer, 0, readCount));
       }
   } catch (IOException e) {
       e.printStackTrace();
   }
   ```

   -  关键注意事项

     - **乱码解决**：读取时需保证 “文件编码” 与 “程序编码” 一致（如均为 UTF-8），若文件是 GBK 编码，需后续结合转换流（`InputStreamReader`）处理；

     - **性能优化**：必须使用`char[]`缓冲区读取（而非单个字符读取），减少内存与磁盘的 IO 交互次数（10 万字符用 1024 缓冲区仅需 98 次 IO，单字符读取需 10 万次 IO）。

#### 六、文件字符输出流

1. 作用：以 “内存为基准”，将程序中的字符**写入到磁盘文本文件中**，支持字符串、字符数组等直接写入，是文本内容输出的常用类。

2. 关键 API

| 类别     | 具体 API                                      | 说明                                                         |
| -------- | --------------------------------------------- | ------------------------------------------------------------ |
| 构造器   | `FileWriter(String filePath)`                 | 按路径创建流对象，默认 “覆盖写入”（原文件内容会被清空）      |
|          | `FileWriter(String filePath, boolean append)` | 第二个参数为`true`时，开启 “追加写入”（内容追加到文件末尾，不覆盖原内容） |
| 写入方法 | `void write(int c)`                           | 写入单个字符（`int`接收字符的 Unicode 编码，如`'A'`或 65）   |
|          | `void write(String str)`                      | 直接写入字符串（最常用，如`write("Hello 字符流")`）          |
|          | `void write(String str, int off, int len)`    | 写入字符串的一部分（从`off`索引开始，取`len`个字符）         |
|          | `void write(char[] cbuf)`                     | 写入字符数组                                                 |
|          | `void write(char[] cbuf, int off, int len)`   | 写入字符数组的一部分                                         |
| 刷新方法 | `void flush()`                                | 强制刷新 “缓冲区”，将内存中暂存的数据写入磁盘（字符流底层默认有缓冲区） |

3. 写入 / 追加文本

```java
// 1. 构建FileWriter流对象（append=true开启追加，try-with-resources自动关闭）
try (Writer fw = new FileWriter("src/out.txt", true)
    // Writer fw=new FileWriter("day03-file/src/dlei07-out.txt",true)--追加到文件末尾
    ) {
    // 2. 写入内容（支持字符串、字符数组等）
    fw.write("长风破浪会有时"); // 写字符串
    fw.write("\r\n"); // 写换行符（Windows用\r\n，Linux用\n，字符流直接支持）
    fw.write(new char[]{'直', '挂', '云', '帆', '济', '沧', '海'}, 0, 7); // 写字符数组一部分
    
    // 3. 手动刷新（若不刷新，缓冲区数据可能未写入磁盘；关闭流会自动触发刷新）
    fw.flush();
} catch (IOException e) {
    e.printStackTrace();
}
```

- **为什么需要刷新？**

  - 字符流底层默认有 “内存缓冲区”：写入数据时先存到缓冲区，满了才自动写入磁盘（减少 IO 次数）；

  - 必须手动调用`flush()`或`close()`（默认会刷新）：否则程序结束后缓冲区数据会丢失，文件中无内容；

  - 区别：`flush()`后流可继续使用，`close()`后流被释放（包含刷新操作），不可再写入。

- **换行符处理**
  - 字符流可直接写入字符串形式的换行符：`"\r\n"`（Windows）或`"\n"`（Linux/Mac），无需像字节流那样写`\r`和`\n`的字节数组。

- **编码一致性原则**
  - 读取 / 写入文本时，必须保证 “文件编码”“流编码”“程序编码” 一致（默认 UTF-8）；
  - 若文件是 GBK 编码，直接用`FileReader`读取会乱码，需后续学习转换流`InputStreamReader`指定编码。

#### 七、缓冲流

- 缓冲流属于**高级流**，其核心作用是**包装低级流（如 FileInputStream、FileReader 等）**，通过内置 “缓冲池” 减少磁盘 IO 的系统调用次数，从而**显著提升 IO 操作性能**。

缓冲流分为两大类：

- 缓冲字节流：处理字节数据（如图片、视频、音频等二进制文件）
- 缓冲字符流：处理字符数据（如文本文件）

![image-20251029183551948](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251029183551948.png)

##### 7.1 缓冲字节流（BufferedInputStream / BufferedOutputStream）

1. 核心原理：减少系统调用

低级字节流（如 FileInputStream）每次读写数据都需直接与磁盘交互（即 “系统调用”），而缓冲字节流内置一个**默认 8KB （8192）大小的字节数组（缓冲池）**，实现 “批量读写”：

- **读取逻辑**：缓冲输入流先一次性从磁盘读取 8KB 数据到缓冲池，后续程序从缓冲池取数据（内存操作，速度极快），缓冲池空了再从磁盘补满。
- **写入逻辑**：缓冲输出流先将数据写入缓冲池，缓冲池满了再一次性写入磁盘。

![image-20251029183828765](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251029183828765.png)

2. 使用步骤（以文件复制为例）

- **创建低级流**：创建 FileInputStream（读）、FileOutputStream（写），关联目标文件；
- **包装为缓冲流**：通过缓冲流的构造器传入低级流，得到缓冲流对象；
- **读写操作**：用缓冲流调用 read ()/write () 方法（与低级流用法一致，功能无差异）；
- **关闭资源**：优先关闭高级流（关闭高级流会自动关闭其包装的低级流）。

```java
// 1. 创建低级流
FileInputStream fis = new FileInputStream("源文件路径");
FileOutputStream fos = new FileOutputStream("目标文件路径");

// 2. 包装为缓冲流
BufferedInputStream bis = new BufferedInputStream(fis); // 缓冲输入流
BufferedOutputStream bos = new BufferedOutputStream(fos); // 缓冲输出流

// 3. 读写数据（与低级流用法一致）
byte[] buffer = new byte[1024];
int len;
while ((len = bis.read(buffer)) != -1) {
    bos.write(buffer, 0, len);
}

// 4. 关闭资源（先关高级流）
bos.close();
bis.close();
```

##### 7.2 缓冲字符流（BufferedReader / BufferedWriter）

1. 核心原理：与缓冲字节流一致

内置**默认 8KB 大小的字符数组（缓冲池）**，减少字符读写的磁盘交互次数，提升性能；同时在缓冲字节流基础上，**新增了文本处理的专属功能**，更适合文本文件操作。

2. 核心特性：新增专属功能

缓冲字符流相比低级字符流（如 FileReader/FileWriter），除性能提升外，还新增了 2 个关键功能：

| 缓冲流类       | 新增功能         | 功能说明                                                     |
| -------------- | ---------------- | ------------------------------------------------------------ |
| BufferedReader | `readLine()`方法 | 按 “行” 读取文本，返回一行内容（字符串）；无数据时返回`null`（自动忽略换行符） |
| BufferedWriter | `newLine()`方法  | 按 “系统默认换行符” 换行（Windows：\r\n；Linux：\n），避免跨平台换行问题 |

 （1）BufferedReader：按行读文本

```java
// 1. 低级流+缓冲流包装
FileReader fr = new FileReader("文本文件路径");
BufferedReader br = new BufferedReader(fr);

// 2. 按行读取（循环读取，直到返回null）
String line;
while ((line = br.readLine()) != null) { // 核心：readLine()按行读
    System.out.println(line); // 打印每行内容（无换行符，需手动加）
}

// 3. 关闭资源
br.close();
```

（2）BufferedWriter：按行写文本

```java
// 1. 低级流+缓冲流包装（追加写需给FileWriter加true参数）
FileWriter fw = new FileWriter("目标文件路径", true);
BufferedWriter bw = new BufferedWriter(fw);

// 2. 写内容+换行（核心：newLine()换行）
bw.write("第一行文本");
bw.newLine(); // 自动按系统换行符换行
bw.write("第二行文本");
bw.newLine();

// 3. 关闭资源（缓冲流关闭前会自动刷新缓冲池，无需手动flush()）
bw.close();
```

- 低级管道决定特性，高级管道决定性能

##### 7.3 缓冲流的共性与注意事项

1. **包装特性**：缓冲流必须 “包装低级流” 使用，不能直接关联文件（无无参构造器，必须传入低级流对象）；
2. **缓冲池大小**：默认 8KB，也可通过构造器自定义（如`new BufferedInputStream(fis, 1024*16)`指定 16KB 缓冲池），需根据文件大小合理选择（大文件可适当增大缓冲池，小文件默认即可）；

#### 八、其他流

##### 8.1 字符输入转换流（InputStreamReader）

1. 核心作用：解决**不同编码格式下字符流读取文本乱码**的问题（如代码编码为 UTF-8，读取 GBK 编码的文件）。

2. 乱码根源：字符流（如 FileReader）默认使用**平台 / 代码的编码格式**解码文件字节流：比如，若文件编码（如 GBK）与代码编码（如 UTF-8）不一致，会导致 “字节解析规则不匹配”

3. 解决思路：先获取原始字节流，再按文件真实编码解码为字符流。语法：`publicInputStreamReader(InputStream is,String charset)`

   - 第一步：通过字节流（如 FileInputStream）读取文件**原始字节**（字节本身无编码属性，仅为二进制数据，不会乱码）；

   - 第二步：用 InputStreamReader 将原始字节流按**文件真实编码（如 GBK）** 转换为字符流；

   - 第三步：可进一步包装为缓冲字符流（BufferedReader）提升读取效率（因 InputStreamReader 属于字符流，可被缓冲流包装）。

```java
// 1. 获取GBK文件的原始字节流
FileInputStream fis = new FileInputStream("D:/test_gbk.txt");
// 2. 指定GBK编码，将字节流转换为字符流（核心步骤）
InputStreamReader isr = new InputStreamReader(fis, "GBK");
// 3. 包装为缓冲流，按行读取（可选，提升效率）
BufferedReader br = new BufferedReader(isr);

// 读取数据（无乱码）
String line;
while ((line = br.readLine()) != null) {
    System.out.println(line);
}

// 关闭资源（省略try-with-resources简化代码）
br.close();
isr.close();
fis.close();
```

##### 8.2 打印流（PrintStream / PrintWriter）

1. 核心特点：更方便、高效地 “写入数据”（本质是字节输出流 / 字符输出流的增强类）；"写什么存什么"，无需手动处理数据类型转换（区别于普通输出流）

- 普通流写`97`会存为 ASCII 码对应的字符`'a'`，打印流写`97`会直接存为字符串`"97"`；
- 支持直接写入布尔值（`true/false`）、小数、字符等，无需手动转字符串。

2. 两个实现类的区别与共性

|    类名     |  继承体系  |        适用场景         |                           核心共性                           |
| :---------: | :--------: | :---------------------: | :----------------------------------------------------------: |
| PrintStream | 字节输出流 | 写入字节数据 + 打印功能 | 1. 支持`print()`（不换行）、`println()`（换行）；<br>2. 自带缓冲（源码包装了 BufferedWriter），性能高；<br>3. 可直接指向文件路径，无需手动创建低级流；<br>4. 无需手动刷新（关闭时自动刷新）。 |
| PrintWriter | 字符输出流 | 写入字符数据 + 打印功能 |             打印逻辑完全一致，仅底层流类型不同。             |

 （1）直接写入文件（简化版）

```java
// PrintStream示例：直接指向文件路径
PrintStream ps = new PrintStream("D:/print_test.txt");
ps.println(97);       // 写入"97"（而非字符'a'）
ps.println(true);     // 写入"true"
ps.println("Hello");  // 写入"Hello"
ps.close(); // 关闭时自动刷新

// PrintWriter用法完全一致，仅类名不同
PrintWriter pw = new PrintWriter("D:/print_test.txt");
pw.println(3.14);
pw.close();
```

（2）追加写入（需包装低级流）

打印流直接指向文件时不支持追加，需通过 “低级流 + 追加参数” 实现：

```java
// 1. 低级字节流指定追加模式（第二个参数true表示追加）
FileOutputStream fos = new FileOutputStream("D:/print_test.txt", true);
// 2. 包装为PrintStream，实现追加打印
PrintStream ps = new PrintStream(fos);
ps.println("追加的内容");
ps.close();
```

##### 8.3 特殊数据流（DataOutputStream / DataInputStream）

![image-20251029194638344](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251029194638344.png)

1. 核心作用：实现 **“数据 + 类型” 一并读写 **（区别于普通流仅存数据本身，丢失类型信息），主要用于**数据持久化、网络通信**

2. 关键特性

- **写入时**：不仅存储数据的二进制，还隐式存储数据类型（如写`int 3666`，会存储 “int 类型标识 + 3666 的二进制”），且必须要包装低级管道
- **读取时**：必须按 “写入顺序 + 写入类型” 对应读取，否则会报`EOFException`或数据解析错误；
- **注意**：写入的文件内容为 **“带类型标识的二进制”**，直接打开会显示乱码（需用 DataInputStream 读取才能还原数据和类型）。

（1）用 DataOutputStream 写入带类型数据

```java
// 1. 低级字节流指向文件
FileOutputStream fos = new FileOutputStream("D:/data_test.txt");
// 2. 包装为特殊数据输出流
DataOutputStream dos = new DataOutputStream(fos);

// 按类型写入数据（顺序：字节→字符串→整数→小数）
dos.writeByte(10);          // 写入byte类型
dos.writeUTF("Java IO");    // 写入UTF-8字符串（自带长度标识）
dos.writeInt(3666);         // 写入int类型
dos.writeDouble(3.14);      // 写入double类型

dos.close();
```

（2）用 DataInputStream 读取带类型数据

```java
// 1. 低级字节流读取文件
FileInputStream fis = new FileInputStream("D:/data_test.txt");
// 2. 包装为特殊数据输入流
DataInputStream dis = new DataInputStream(fis);

// 按写入顺序读取对应类型（顺序：字节→字符串→整数→小数）
byte b = dis.readByte();
String s = dis.readUTF();
int i = dis.readInt();
double d = dis.readDouble();

// 输出结果：10, Java IO, 3666, 3.14（类型完全匹配）
System.out.println(b + ", " + s + ", " + i + ", " + d);

dis.close();
```

#### 九、IO框架

1. **框架**：框架（Framework）是预先封装的代码库 / 工具集合，以 JAR 包形式提供，本质是 “半成品代码”，封装了重复逻辑，降低开发复杂度，加速项目开发
2. **Commons-IO 框架的定位**：专门封装 Java 原生 IO 流的文件读写逻辑，对外提供更简洁的 API

3. Commons-IO 的核心功能

Commons-IO 的核心是**工具类**（如`FileUtils`），通过静态方法直接调用，无需创建对象，以下是视频重点演示的功能：

|     功能场景      |                  核心 API（`FileUtils`类）                   |
| :---------------: | :----------------------------------------------------------: |
|     复制文件      |          `FileUtils.copyFile(File src, File dest)`           |
|    复制文件夹     |     `FileUtils.copyDirectory(File srcDir, File destDir)`     |
| 删除文件 / 文件夹 |                `FileUtils.delete(File file)`                 |
|   文件内容读写    | `FileUtils.readFileToString(File file, String charset)`<br>`FileUtils.writeStringToFile(File file, String content, String charset, boolean append)` |

- JDK 1.7 后原生提供`java.nio.file.Files`类，也支持 “一行代码复制文件”等更加简便的操作