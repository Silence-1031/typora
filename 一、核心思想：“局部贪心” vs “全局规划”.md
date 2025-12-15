贪心算法与动态规划是算法设计中解决**最优子结构问题**的两种核心策略，但二者在核心思想、决策逻辑和适用场景上存在本质区别，其核心差异可概括为：**贪心算法 “局部最优即全局最优”，动态规划 “全局最优依赖子问题最优解的存储与复用”**。以下从 6 个关键维度展开对比，并结合案例说明，帮助理解二者的本质差异。

### 一、核心思想：“局部贪心” vs “全局规划”

这是二者最根本的区别，直接决定了算法的决策逻辑。

- **贪心算法（Greedy Algorithm）**

  核心是 “**短视的局部最优**”：在每一步决策中，都选择当前状态下 “看起来最好” 的选项（即局部最优解），并期望通过一系列局部最优的选择，最终得到全局最优解。

  它不回溯、不考虑未来步骤的影响，仅基于当前信息做决策，本质是 “走一步看一步，选当下最好的”。

- **动态规划（Dynamic Programming, DP）**

  核心是 “**全局最优依赖子问题最优**”：先将原问题拆解为多个**重叠的子问题**，通过存储子问题的最优解（避免重复计算），逐步推导得到原问题的全局最优解。

  它会 “记住” 所有子问题的解，确保每一步决策都服务于全局最优，本质是 “先解决所有小问题，再用小问题的解拼出大问题的解”。

### 二、关键特性：是否满足 “贪心选择性质”

二者的适用场景由问题的特性决定，其中 “贪心选择性质” 是贪心算法的核心前提，动态规划则无此要求。

- **贪心算法的前提**

  问题必须同时满足两个条件：

  1. **最优子结构**：原问题的最优解包含子问题的最优解（这是贪心与 DP 的共同前提）；

  2. 贪心选择性质

     ：每一步的局部最优选择，能直接导向全局最优解（即 “局部最优→全局最优” 成立）。

     

     如果不满足贪心选择性质，贪心算法会得到错误的解

     。

- **动态规划的前提**

  问题只需满足两个条件：

  1. **最优子结构**：与贪心算法一致；

  2. 子问题重叠性

     ：多个子问题被重复计算（因此需要用 “备忘录” 或 “DP 表” 存储子问题解，避免冗余计算）。

     

     动态规划不要求 “贪心选择性质”，因此能处理更多贪心算法无法解决的问题。

### 三、决策逻辑：“不可逆” vs “回溯式推导”

二者的决策顺序和灵活性差异显著：

- **贪心算法**：**单向不可逆决策**

  每一步决策一旦做出，就无法修改（不回溯）。例如 “找零问题”（用最少硬币找零），贪心策略会优先选面值最大的硬币，选完后剩余金额减少，继续选当前最大面值，直到金额为 0—— 过程中不会因为 “后续没小硬币” 而反悔。

- **动态规划**：**逆向 / 正向推导 + 子问题复用**

  通常先拆解原问题为子问题（如 “求 n 的最优解” 需先求 n-1、n-2 的最优解），并将子问题的解存储在 DP 表或备忘录中，再基于子问题的解推导当前问题的解。决策过程是 “可回溯” 的（通过子问题解的复用间接实现）。

### 四、时间复杂度：通常更优 vs 通常更高

由于贪心算法不存储子问题解，也不重复计算，时间复杂度通常更低；动态规划需要存储子问题解，时间复杂度由子问题数量和每个子问题的计算成本决定，通常更高。

| 算法     | 时间复杂度示例                            | 原因                                             |
| -------- | ----------------------------------------- | ------------------------------------------------ |
| 贪心算法 | 霍夫曼编码：O (n log n)                   | 仅需排序 + 优先队列操作，无重复计算              |
| 动态规划 | 0-1 背包：O (n×C)（n 为物品数，C 为容量） | 需遍历所有子问题（n×C 个），每个子问题计算 O (1) |

### 五、典型案例对比：直观理解差异

通过两个经典问题，对比二者的应用场景和结果：

#### 案例 1：找零问题（是否满足贪心选择性质）

- **问题**：用面值为 25、10、5、1 的硬币，找零 41 元，要求硬币数量最少。

- 贪心算法

  ：

  

  每步选最大面值硬币：25 → 10（剩余 16）→ 10（剩余 6）→ 5（剩余 1）→ 1 → 共 5 枚硬币（25+10+10+5+1），这是全局最优解（因为硬币面值满足 “贪心选择性质”：大面值是小面值的倍数）。

- 动态规划

  ：

  

  会计算 “找零 1 元、2 元…41 元” 的所有子问题最优解，最终得到 41 元的最优解（同样 5 枚），但过程中需存储 41 个子问题的解，效率低于贪心。

#### 案例 2：0-1 背包问题（不满足贪心选择性质，贪心失效）

- **问题**：背包容量 C=10，物品为（重量 3，价值 4）、（重量 4，价值 5）、（重量 5，价值 6），每个物品只能选一次，求最大价值。

- 贪心算法

  ：

  

  按 “单位重量价值” 排序（物品 1：4/3≈1.33，物品 2：5/4=1.25，物品 3：6/5=1.2），优先选单位价值高的：

  

  先选物品 1（重量 3，价值 4，剩余容量 7）→ 再选物品 2（重量 4，价值 5，剩余容量 3）→ 无法选物品 3，总价值 = 9。

  

  但

  全局最优解

  是选物品 2 + 物品 3（重量 4+5=9≤10，价值 5+6=11），贪心算法因 “局部最优”（选物品 1）导致全局最优失效。

- 动态规划

  ：

  

  通过 DP 表存储 “前 i 个物品、容量 j” 的最大价值，最终计算出 “3 个物品、容量 10” 的最大价值 = 11，正确得到全局最优解。

### 六、总结：核心区别对照表

| 对比维度   | 贪心算法（Greedy）                           | 动态规划（DP）                                          |
| ---------- | -------------------------------------------- | ------------------------------------------------------- |
| 核心思想   | 局部最优 → 全局最优（短视决策）              | 子问题最优 → 全局最优（全局规划）                       |
| 关键前提   | 最优子结构 + 贪心选择性质                    | 最优子结构 + 子问题重叠性                               |
| 决策逻辑   | 单向不可逆，不回溯                           | 依赖子问题解，可间接回溯                                |
| 数据存储   | 无需存储子问题解                             | 需存储（DP 表 / 备忘录）                                |
| 时间复杂度 | 通常较低（O (n log n) 等）                   | 通常较高（O (n²)、O (n×C) 等）                          |
| 适用场景   | 满足贪心选择性质的问题（如找零、霍夫曼编码） | 不满足贪心选择性质的问题（如 0-1 背包、最长公共子序列） |

### 一句话记忆

- 贪心：“当下最好，就是最好”（适合问题简单、能 “一步对，步步对” 的场景）；
- 动态规划：“先解决所有小问题，再拼出大问题的答案”（适合问题复杂、需要 “统筹全局” 的场景）。





怎么读取一行字符串

分享



在 C++ 中读取一行字符串有多种方法，核心是区分**带空格 / 换行的整行读取**和**按空格分割的读取**，以下是最常用的方式及详细说明：

### 一、核心方法：`getline()`（读取整行，包含空格）

`getline()` 是 C++ 标准库中专门用于读取一行字符串的函数，会读取从当前输入位置到换行符（`\n`）的所有字符（换行符本身会被丢弃，不会存入字符串）。

#### 1. 基础用法（`cin.getline()`，字符数组版）

适用于字符数组（C 风格字符串），语法：

cpp



运行









```cpp
cin.getline(字符数组名, 数组长度, 终止符);
```

- 参数 1：存储字符串的字符数组；
- 参数 2：数组最大长度（避免越界，实际读取 `长度-1` 个字符，末尾自动加 `\0`）；
- 参数 3：可选终止符（默认是 `\n`，遇到该字符停止读取）。

示例：

cpp



运行









```cpp
#include <iostream>
using namespace std;

int main() {
    char str[100]; // 定义字符数组
    cout << "请输入一行字符串（含空格）：" << endl;
    cin.getline(str, 100); // 读取整行，最多99个字符+1个'\0'
    cout << "你输入的是：" << str << endl;
    return 0;
}
```

#### 2. 更推荐：`getline(cin, string)`（string 类版）

适用于 C++ 的 `string` 类（无需指定长度，更灵活），语法：

cpp



运行









```cpp
getline(cin, 字符串变量, 终止符);
```

- 参数 1：标准输入流 `cin`；
- 参数 2：`string` 类型变量，用于存储读取的内容；
- 参数 3：可选终止符（默认 `\n`）。

示例：

cpp



运行









```cpp
#include <iostream>
#include <string> // 必须包含string头文件
using namespace std;

int main() {
    string str;
    cout << "请输入一行字符串（含空格）：" << endl;
    getline(cin, str); // 读取整行，自动适配长度
    cout << "你输入的是：" << str << endl;
    return 0;
}
```

### 二、注意：`cin >> 字符串` 与 `getline()` 的冲突

如果先用 `cin >>` 读取数字 / 单个单词，再用 `getline()`，会出现`getline()` 读取到空行的问题（因为 `cin >>` 会留下换行符 `\n` 在输入缓冲区）。

#### 解决方法：读取数字后，用 `cin.ignore()` 清空缓冲区的换行符

示例：

cpp



运行









```cpp
#include <iostream>
#include <string>
#include <limits> // 用于numeric_limits
using namespace std;

int main() {
    int n;
    cout << "请输入一个数字：" << endl;
    cin >> n; // 读取数字，缓冲区留下'\n'
    
    // 清空缓冲区的换行符（关键！）
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
    
    string str;
    cout << "请输入一行字符串：" << endl;
    getline(cin, str); // 正常读取整行
    cout << "数字：" << n << "，字符串：" << str << endl;
    return 0;
}
```

- `numeric_limits<streamsize>::max()`：表示忽略尽可能多的字符，直到遇到 `\n`，需包含头文件 `<limits>`。

### 三、补充：按空格分割读取（`cin >> string`）

如果只需读取**无空格的单个单词**，直接用 `cin >> string` 即可（自动跳过空格 / 换行 / 制表符）：

cpp



运行









```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str;
    cout << "请输入一个无空格的字符串：" << endl;
    cin >> str; // 遇到空格/换行停止读取
    cout << "你输入的是：" << str << endl;
    return 0;
}
```

### 总结

| 场景                 | 推荐方法                  | 注意事项                           |
| -------------------- | ------------------------- | ---------------------------------- |
| 读取含空格的整行     | `getline(cin, string)`    | 避免与 `cin >>` 混用（需清缓冲区） |
| 读取字符数组的整行   | `cin.getline(数组, 长度)` | 注意数组长度，防止越界             |
| 读取无空格的单个单词 | `cin >> string`           | 自动跳过空白符                     |

### 一、结构体（struct）的核心概念

结构体（`struct`）是 C++ 中**用户自定义的复合数据类型**，用于将**不同类型**的变量（成员）封装在一起，描述一个 “实体” 的多个属性。

- 本质：把相关联的数据打包，让代码更模块化、易维护；
- 与 C 的区别：C++ 的结构体支持**成员函数、访问控制（public/private）、继承、重载**等面向对象特性（可看作 “轻量级类”，默认成员为`public`，而类`class`默认`private`）。

### 二、结构体的基本语法

#### 1. 定义结构体

cpp



运行









```cpp
// 语法：struct 结构体名 { 成员列表; };（末尾分号不可少）
struct Student {
    // 数据成员（不同类型）
    int id;         // 学号
    string name;    // 姓名
    float score;    // 成绩
    // 成员函数（C++特有，可直接定义）
    void showInfo() {
        cout << "学号：" << id << "，姓名：" << name << "，成绩：" << score << endl;
    }
};
```

- 结构体名（如`Student`）是**自定义类型名**，可用于声明变量；
- 成员可以是：基本类型（int/float）、自定义类型（string / 其他结构体）、函数。

#### 2. 声明结构体变量

有 3 种常见方式：

cpp



运行









```cpp
// 方式1：定义结构体后声明
Student s1; // 声明单个变量
Student s2, s3; // 声明多个变量

// 方式2：定义结构体时直接声明
struct Teacher {
    string name;
    int age;
} t1, t2; // t1、t2是Teacher类型的变量

// 方式3：匿名结构体（无结构体名，仅能在定义时声明变量，不可复用）
struct {
    int x;
    int y;
} point1, point2; // 仅能声明point1、point2，无法再定义其他变量
```

#### 3. 初始化结构体变量

C++ 支持多种初始化方式，推荐 “统一初始化”（C++11 及以上）：

cpp



运行









```cpp
// 方式1：默认初始化（成员为随机值，不推荐）
Student s1; 

// 方式2：按成员顺序初始化（C风格，易出错，成员顺序变了会出问题）
Student s2 = {101, "张三", 95.5}; 

// 方式3：统一初始化（C++11，推荐，顺序无关，可缺省）
Student s3{.id=102, .name="李四", .score=88.0}; 

// 方式4：构造函数初始化（C++特有，下文详解）
```

#### 4. 访问结构体成员

用**成员访问运算符 `.`**（普通变量）或 `->`（指针变量）：

cpp



运行









```cpp
// 普通变量：变量名.成员名
s1.id = 1001;
s1.name = "王五";
s1.score = 92.0;
s1.showInfo(); // 调用成员函数

// 指针变量：指针名->成员名（等价于 (*指针).成员名）
Student* p = &s1;
p->id = 1002; // 等价于 (*p).id = 1002
p->showInfo();
```

### 三、结构体的进阶用法

#### 1. 结构体的构造函数

构造函数是**无返回值、与结构体同名**的成员函数，用于初始化成员（解决 “默认初始化成员为随机值” 的问题）。

cpp



运行









```cpp
struct Student {
    int id;
    string name;
    float score;

    // 无参构造函数（默认构造）
    Student() {
        id = 0;
        name = "未知";
        score = 0.0;
    }

    // 有参构造函数（自定义初始化）
    Student(int i, string n, float s) {
        id = i;
        name = n;
        score = s;
    }

    void showInfo() {
        cout << id << " " << name << " " << score << endl;
    }
};

// 使用构造函数初始化
int main() {
    Student s1; // 调用无参构造：id=0, name=未知, score=0.0
    Student s2(103, "赵六", 90.5); // 调用有参构造
    s1.showInfo(); // 输出：0 未知 0
    s2.showInfo(); // 输出：103 赵六 90.5
    return 0;
}
```

#### 2. 结构体的访问控制

C++ 结构体支持`public`/`private`（默认`public`），用于控制成员的访问权限：

cpp



运行









```cpp
struct Person {
private: // 私有成员：仅结构体内部/友元可访问
    int age;
public: // 公有成员：外部可访问（默认）
    string name;
    // 公有函数（接口）：用于访问私有成员
    void setAge(int a) {
        if (a >= 0) age = a; // 可加合法性校验
    }
    int getAge() {
        return age;
    }
};

int main() {
    Person p;
    p.name = "小明"; // 公有成员可直接访问
    // p.age = 18; // 报错：private成员不可直接访问
    p.setAge(18); // 通过公有函数设置私有成员
    cout << p.getAge(); // 输出：18
    return 0;
}
```

#### 3. 结构体数组

结构体常用来定义数组，描述一组同类型实体：

cpp



运行









```cpp
struct Book {
    string title;
    float price;
};

int main() {
    // 定义结构体数组并初始化
    Book books[3] = {
        {"C++ Primer", 89.0},
        {"算法导论", 128.0},
        {"Python编程", 79.0}
    };
    // 遍历数组
    for (int i = 0; i < 3; i++) {
        cout << "书名：" << books[i].title << "，价格：" << books[i].price << endl;
    }
    return 0;
}
```

#### 4. 结构体嵌套

结构体的成员可以是另一个结构体（描述更复杂的实体）：

cpp



运行









```cpp
// 定义坐标结构体
struct Point {
    int x;
    int y;
};
// 定义矩形结构体（嵌套Point）
struct Rect {
    Point leftTop; // 左上角坐标
    Point rightBottom; // 右下角坐标
    // 计算矩形面积
    int getArea() {
        int width = rightBottom.x - leftTop.x;
        int height = rightBottom.y - leftTop.y;
        return width * height;
    }
};

int main() {
    Rect r{{1, 3}, {5, 1}}; // 嵌套初始化
    cout << "矩形面积：" << r.getArea(); // 输出：8（宽4，高2）
    return 0;
}
```

#### 5. 结构体作为函数参数 / 返回值

- 传值：拷贝整个结构体（效率低，适合小结构体）；
- 传引用 / 指针：仅传递地址（效率高，适合大结构体）。

cpp



运行









```cpp
struct Student {
    string name;
    int score;
};

// 传引用：修改原结构体
void updateScore(Student& s, int newScore) {
    s.score = newScore;
}

// 返回结构体
Student createStudent(string name, int score) {
    return {name, score};
}

int main() {
    Student s = createStudent("小红", 85);
    updateScore(s, 90);
    cout << s.name << " " << s.score; // 输出：小红 90
    return 0;
}
```

### 四、结构体与类（class）的区别

| 特性         | 结构体（struct）         | 类（class）                        |
| ------------ | ------------------------ | ---------------------------------- |
| 默认访问权限 | public                   | private                            |
| 设计初衷     | 侧重封装数据（C 兼容）   | 侧重面向对象（封装 + 继承 + 多态） |
| 用法         | 可当类用，也可仅封装数据 | 纯面向对象设计                     |

### 五、常见使用场景

1. 描述实体属性（如学生、图书、坐标）；
2. 封装函数参数（多个参数打包成结构体，简化函数签名）；
3. 数据存储（如结构体数组、链表节点）；
4. 与 STL 容器结合（如`vector<Student>`、`map<int, Book>`）。



### 一、按指定字符分割字符串（替代空格分割）

要实现**按任意指定字符（如逗号、竖线、分号）分割字符串**，需基于 `stringstream` 结合 `getline` 实现（核心是指定 `getline` 的终止符）。以下是通用分割函数及示例：

#### 1. 通用分割函数（按指定字符分割）

cpp



运行









```cpp
#include <iostream>
#include <vector>
#include <string>
#include <sstream> // 必须包含该头文件
using namespace std;

// 按指定分隔符delim分割字符串s，返回分割后的字符串数组
vector<string> split(const string& s, char delim) {
    vector<string> res;
    stringstream ss(s); // 将字符串s存入stringstream
    string token;
    // 按delim分割：每次读取到delim为止，不包含delim本身
    while (getline(ss, token, delim)) {
        // 可选：过滤空字符串（如连续分隔符导致的空值）
        if (!token.empty()) {
            res.push_back(token);
        }
    }
    return res;
}

// 测试示例
int main() {
    // 示例1：按逗号分割
    string s1 = "apple,banana,pear,orange";
    vector<string> res1 = split(s1, ',');
    cout << "按逗号分割：";
    for (const auto& str : res1) cout << str << " "; // 输出：apple banana pear orange

    // 示例2：按竖线分割（含连续分隔符）
    string s2 = "a||b|c";
    vector<string> res2 = split(s2, '|');
    cout << "\n按竖线分割：";
    for (const auto& str : res2) cout << str << " "; // 输出：a b c（过滤了空值）

    // 示例3：按空格分割（等价于你原有的split函数）
    string s3 = "hello world c++";
    vector<string> res3 = split(s3, ' ');
    cout << "\n按空格分割：";
    for (const auto& str : res3) cout << str << " "; // 输出：hello world c++
    return 0;
}
```

#### 2. 关键说明

- `getline(ss, token, delim)`：从 `stringstream` 中读取字符，直到遇到 `delim` 停止，读取的内容存入 `token`（**不包含 delim 本身**）；
- 若省略第三个参数 `delim`，默认按换行符 `\n` 分割；
- 过滤空字符串：避免连续分隔符（如 `a||b`）产生空元素，根据业务需求可选。

### 二、`sstream` 头文件详解

`<sstream>` 是 C++ 标准库中**字符串流**的头文件，核心是将字符串作为 “流” 处理（类似 `cin`/`cout` 对控制台的处理），支持字符串与基本数据类型的相互转换、字符串分割等操作。

#### 1. 核心类

`<sstream>` 包含 3 个核心类，均继承自标准流类：

| 类名            | 功能                           | 常用场景                 |
| --------------- | ------------------------------ | ------------------------ |
| `istringstream` | 输入字符串流（从字符串读数据） | 字符串分割、字符串转数值 |
| `ostringstream` | 输出字符串流（向字符串写数据） | 数值转字符串、拼接字符串 |
| `stringstream`  | 双向字符串流（可读可写）       | 同时需要读写字符串的场景 |

#### 2. 核心方法（以 `stringstream` 为例）

##### （1）构造与赋值

cpp



运行









```cpp
// 1. 空构造，后续赋值
stringstream ss;
ss.str("hello world"); // 将字符串"hello world"存入流

// 2. 构造时直接传入字符串
string s = "123 456";
stringstream ss2(s);
```

##### （2）读取数据（输入操作）

- 用 `>>` 运算符：按**空白符（空格 / 换行 / 制表符）** 分割读取（与 `cin` 行为一致）；
- 用 `getline`：按指定分隔符读取（支持非空白符分割）。

cpp



运行









```cpp
stringstream ss("123 abc 45.6");
int a; string b; double c;
ss >> a >> b >> c; // a=123, b="abc", c=45.6
```

##### （3）写入数据（输出操作）

用 `<<` 运算符：将数据写入字符串流，支持多类型拼接。

cpp



运行









```cpp
ostringstream oss; // 输出流更高效
oss << "年龄：" << 18 << "，分数：" << 95.5;
string res = oss.str(); // 提取拼接后的字符串："年龄：18，分数：95.5"
```

##### （4）提取字符串

用 `str()` 方法：获取流中存储的完整字符串（无参数），或覆盖流中的字符串（传参数）。

cpp



运行









```cpp
stringstream ss("test");
cout << ss.str(); // 输出：test
ss.str("new test"); // 覆盖流内容
cout << ss.str(); // 输出：new test
```

##### （5）状态重置（复用流）

若需重复使用 `stringstream`，需清空状态和内容：

cpp



运行









```cpp
stringstream ss;
ss << "123";
// 重置：清空内容 + 重置流状态
ss.str(""); // 清空内容
ss.clear(); // 重置流状态（如eofbit/failbit）
ss << "456";
cout << ss.str(); // 输出：456
```

#### 3. 常用场景示例

##### （1）字符串转数值

cpp



运行









```cpp
string s = "12345";
istringstream iss(s);
int num;
iss >> num; // num=12345

string s2 = "3.1415";
istringstream iss2(s2);
double pi;
iss2 >> pi; // pi=3.1415
```

##### （2）数值转字符串

cpp



运行









```cpp
int a = 100;
double b = 2.5;
ostringstream oss;
oss << a << "_" << b;
string res = oss.str(); // res="100_2.5"
```

##### （3）拼接多类型数据

cpp



运行









```cpp
ostringstream oss;
oss << "姓名：" << "张三" << "，年龄：" << 20 << "，成绩：" << 88.0;
string info = oss.str(); // info="姓名：张三，年龄：20，成绩：88"
```

### 三、原空格分割函数的等价替换

你原有的 `split` 函数（按空格分割），本质是 `stringstream` 的 `>>` 运算符（自动跳过空白符），等价于：

cpp



运行









```cpp
vector<string> split(const string& s) {
    vector<string> res;
    istringstream iss(s);
    string token;
    // >> 自动跳过空格/换行/制表符，读取到下一个空白符为止
    while (iss >> token) {
        res.push_back(token);
    }
    return res;
}
```

### 总结

| 操作             | 方法                                       | 适用场景                    |
| ---------------- | ------------------------------------------ | --------------------------- |
| 按空格分割字符串 | `stringstream + >>`                        | 空白符分隔的简单场景        |
| 按指定字符分割   | `stringstream + getline(ss, token, delim)` | 任意分隔符（逗号 / 竖线等） |
| 字符串↔数值转换  | `istringstream/ostringstream`              | 类型转换                    |
| 多类型字符串拼接 | `ostringstream + <<`                       | 避免频繁字符串拼接（高效）  |



### 一、进阶 split 函数（支持多分隔符）

该函数支持传入**多个分隔符**（如逗号、空格、竖线等），只要字符串中出现任意一个分隔符，就会分割字符串，同时过滤空字符串（避免连续分隔符产生无效元素）。

#### 完整代码实现

cpp



运行









```cpp
#include <iostream>
#include <vector>
#include <string>
#include <cctype> // 用于isspace（可选，若需兼容空格分类）
using namespace std;

/**
 * 按多分隔符分割字符串
 * @param s: 待分割的原字符串
 * @param delimiters: 分隔符集合（如 ", " 表示逗号和空格都是分隔符）
 * @return: 分割后的字符串数组（过滤空字符串）
 */
vector<string> split(const string& s, const string& delimiters) {
    vector<string> res;
    string token; // 存储当前分割出的子串

    // 遍历原字符串的每个字符
    for (char c : s) {
        // 判断当前字符是否是分隔符
        bool is_delimiter = false;
        for (char delim : delimiters) {
            if (c == delim) {
                is_delimiter = true;
                break;
            }
        }

        if (is_delimiter) {
            // 遇到分隔符：若当前token非空，存入结果并清空
            if (!token.empty()) {
                res.push_back(token);
                token.clear();
            }
        } else {
            // 非分隔符：追加到当前token
            token += c;
        }
    }

    // 处理最后一个token（字符串末尾无分隔符的情况）
    if (!token.empty()) {
        res.push_back(token);
    }

    return res;
}

// 测试示例
int main() {
    // 示例1：按逗号+空格分割
    string s1 = "apple, banana  pear | orange";
    vector<string> res1 = split(s1, ", |"); // 分隔符：逗号、空格、竖线
    cout << "示例1（分隔符：, |）分割结果：" << endl;
    for (const auto& str : res1) {
        cout << "\"" << str << "\" ";
    }
    cout << "\n\n";

    // 示例2：按分号+换行符分割（含连续分隔符）
    string s2 = "a;;b\nc||d";
    vector<string> res2 = split(s2, ";\n|"); // 分隔符：分号、换行符、竖线
    cout << "示例2（分隔符：;\\n|）分割结果：" << endl;
    for (const auto& str : res2) {
        cout << "\"" << str << "\" ";
    }
    cout << "\n\n";

    // 示例3：兼容纯空格分隔（等价于原split函数）
    string s3 = "hello   world c++";
    vector<string> res3 = split(s3, " "); // 仅空格为分隔符
    cout << "示例3（分隔符：空格）分割结果：" << endl;
    for (const auto& str : res3) {
        cout << "\"" << str << "\" ";
    }

    return 0;
}
```

#### 输出结果

plaintext











```plaintext
示例1（分隔符：, |）分割结果：
"apple" "banana" "pear" "orange" 

示例2（分隔符：;\n|）分割结果：
"a" "b" "c" "d" 

示例3（分隔符：空格）分割结果：
"hello" "world" "c++" 
```

### 二、核心逻辑解析

1. 遍历字符 + 判断分隔符

   ：

   - 逐个遍历原字符串的字符，对每个字符检查是否属于传入的 “分隔符集合”；
   - 若属于分隔符：若当前已拼接的 `token` 非空，则存入结果并清空 `token`；
   - 若不属于分隔符：将字符追加到 `token` 中。

2. **处理末尾 token**：字符串遍历结束后，若最后一个 `token` 非空（即末尾无分隔符），需手动存入结果；

3. **过滤空字符串**：连续分隔符（如 `a;;b`）只会触发一次 “清空 token”，不会产生空元素。

### 三、灵活扩展说明

1. 支持任意多分隔符

   ：只需在调用时传入包含所有分隔符的字符串，例如：

   - 分隔符为 `,;:|` → 传入 `" ,;:|"`；
   - 分隔符为换行符 + 制表符 → 传入 `"\n\t"`。

2. **兼容原单分隔符场景**：若仅需按单个分隔符分割（如逗号），调用 `split(s, ",")` 即可，等价于单分隔符版本。

3. **大小写不敏感（可选扩展）**：若需忽略分隔符大小写（如同时识别 `,` 和 `，`），可在判断分隔符时统一转换为小写 / 大写。

### 四、性能说明

该实现的时间复杂度为 `O(n*m)`（`n` 是原字符串长度，`m` 是分隔符数量），在分隔符数量较少（如 10 个以内）的场景下效率足够；若需极致性能，可将分隔符存入 `unordered_set`，将判断分隔符的时间复杂度降至 `O(1)`，优化版本如下：

#### 优化版（基于 unordered_set，更高性能）

cpp



运行









```cpp
#include <iostream>
#include <vector>
#include <string>
#include <unordered_set>
using namespace std;

vector<string> split(const string& s, const string& delimiters) {
    vector<string> res;
    string token;
    // 将分隔符存入unordered_set，O(1)查找
    unordered_set<char> delim_set(delimiters.begin(), delimiters.end());

    for (char c : s) {
        if (delim_set.count(c)) { // O(1)判断是否是分隔符
            if (!token.empty()) {
                res.push_back(token);
                token.clear();
            }
        } else {
            token += c;
        }
    }

    if (!token.empty()) {
        res.push_back(token);
    }

    return res;
}

// 测试代码与之前一致，性能更优
```

分层图 BFS 是一种**处理 “带状态约束的最短路径问题” 的 BFS 扩展算法**，核心是把 “状态” 作为 “层”，在不同层之间 / 层内部进行 BFS，从而同时维护 “节点位置” 和 “状态信息”，找到满足约束的最短路径。

### 一、为什么需要分层图 BFS？

普通 BFS 只能处理**无状态约束**的无权图最短路径（比如 “从 A 到 B 的最少步数”），但如果问题中存在**额外约束**（比如 “最多使用 k 次道具”“最多经过 m 个特殊节点”），普通 BFS 就无法直接记录这些约束的消耗情况。

而分层图 BFS 通过**把 “约束的消耗程度” 作为 “层”**，将原问题转化为 “多层图” 上的 BFS，每一层对应一种 “约束消耗状态”，从而同时追踪 “节点位置” 和 “约束消耗”。

### 二、分层图 BFS 的核心思想

假设约束是 “最多使用 k 次某道具”，那么分层图的设计是：

1. **分层**：将图分为`k+1`层（对应 “使用了 0 次道具”“使用了 1 次道具”……“使用了 k 次道具”）。
2. **同层边**：在同一层内移动，代表 “不消耗道具” 的操作（比如走普通路径）。
3. **跨层边**：从第`t`层移动到第`t+1`层，代表 “消耗 1 次道具” 的操作（比如使用道具走特殊路径）。
4. **目标**：找到从 “起点（第 0 层）” 到 “终点（任意≤k 层）” 的最短路径。

### 三、分层图 BFS 的直观例子（结合你之前的题目）

以 “最多增设 k 个路由器” 的问题为例：

- **层的定义**：第`t`层对应 “已经使用了`t`个新增路由器” 的状态。
- **同层 BFS**：在第`t`层内移动 → 只走**原路由器**（不新增，所以不消耗 k 的配额）。
- **跨层 BFS**：从第`t`层跳到第`t+1`层 → 走**新增路由器**（消耗 1 次 k 的配额）。
- **状态表示**：每个状态是`(层t, 节点u)`，代表 “在第`t`层（用了 t 个新增），当前位于节点 u”。

### 四、分层图 BFS 的执行流程（步骤）

以 “最多用 k 次道具” 为例，步骤如下：

1. **状态初始化**：定义`dist[t][u]`表示 “在第`t`层，到达节点`u`的最短路径长度”，初始化为无穷大；起点状态`dist[0][起点] = 0`（或 1，依路径长度定义）。

2. **队列初始化**：将起点状态`(t=0, u=起点)`加入队列。

3. BFS 遍历

   ：

   - 取出队首状态`(t, u)`。
   - **同层扩展**：遍历所有 “不消耗道具” 的可达节点`v`，若`dist[t][v]`可更新，则更新并将`(t, v)`入队。
   - **跨层扩展**：若`t < k`，遍历所有 “消耗 1 次道具” 的可达节点`v`，若`dist[t+1][v]`可更新，则更新并将`(t+1, v)`入队。

4. **结果计算**：遍历所有`dist[t][终点]`（`t ≤ k`），取最小值即为满足约束的最短路径。

### 五、分层图 BFS 的特点

1. **适用场景**：无权图 + 有 “最多使用 X 次某操作” 的约束（如最多用 k 个道具、最多走 m 条特殊边）。
2. **优势**：相比普通 BFS，能同时维护 “节点位置” 和 “约束消耗”；相比迪杰斯特拉，在无权图中效率更高（时间复杂度更低）。
3. **本质**：是 “状态 BFS” 的一种形式，将 “约束状态” 显式分层，让 BFS 的队列天然按 “路径长度” 有序遍历。