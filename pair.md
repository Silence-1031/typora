#pair

在 C++ 中，`pair` 是标准库（`<utility>` 头文件）提供的一个**模板结构体**，核心作用是 “将两个不同类型的数据打包成一个整体”，方便在函数返回、容器存储等场景中传递或管理关联数据（比如键值对的雏形、坐标的 x/y 值等）。

### 一、pair 的基本结构

`pair` 是模板类，定义格式为：

```cpp
template <class T1, class T2>
struct pair {
    T1 first;  // 第一个成员变量（通常对应“键”或“第一个数据”）
    T2 second; // 第二个成员变量（通常对应“值”或“第二个数据”）
    
    // 构造函数、运算符重载等（编译器自动生成或标准库实现）
};
```

- `T1` 和 `T2` 可以是任意数据类型（如 `int`、`string`、自定义类，甚至另一个 `pair`），二者类型可以相同（如 `pair<int, int>`），也可以不同（如 `pair<string, int>`）。
- 成员变量 `first` 和 `second` 是**公有的**，可以直接通过 `.` 访问（无需 getter 函数）。

### 二、pair 的核心用法

#### 1. 定义与初始化

`pair` 的初始化有多种方式，推荐使用**统一初始化（C++11 及以后）** 或 `make_pair` 函数，代码更简洁。

| 初始化方式                 | 示例代码                                                     | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 默认构造（成员默认初始化） | `pair<int, string> p1;`                                      | `p1.first` 为 `int` 默认值 `0`，`p1.second` 为 `string` 默认值空串 |
| 直接赋值初始化             | `pair<int, string> p2(10, "hello");`                         | 显式指定 `first=10`，`second="hello"`                        |
| 统一初始化（`{}`）         | `pair<int, string> p3 = {20, "world"};` 或 `auto p3 = {20, "world"};` | C++11 后推荐，类型可自动推导（`auto` 需确保编译器能识别 `pair` 类型） |
| `make_pair` 函数           | `auto p4 = make_pair(30, 3.14);`                             | 自动推导 `T1=int`、`T2=double`，无需手动写模板参数，最简洁   |
| 拷贝初始化                 | `pair<int, string> p5 = p2;` 或 `auto p5 = p2;`              | `p5` 复制 `p2` 的 `first` 和 `second` 值                     |

#### 2. 访问成员变量

由于 `first` 和 `second` 是公有成员，直接用 **`.` 运算符**访问：

```cpp
#include <iostream>
#include <utility>  // pair 所在头文件
#include <string>

using namespace std;

int main() {
    auto p = make_pair("apple", 5);  // T1=const char*, T2=int
    cout << "商品：" << p.first << endl;  // 输出 "商品：apple"
    cout << "数量：" << p.second << endl; // 输出 "数量：5"
    
    // 也可以修改成员值（非 const 情况下）
    p.second = 10;
    cout << "修改后数量：" << p.second << endl; // 输出 "修改后数量：10"
    return 0;
}
```

#### 3. 比较运算（自动支持）

`pair` 标准库已重载了 `<`、`<=`、`>`、`>=`、`==`、`!=` 等比较运算符，规则是：

1. 先比较 `first` 成员，`first` 大的 `pair` 整体更大；
2. 若 `first` 相等，再比较 `second` 成员。

这一特性在排序场景（如 `sort` 排序 `pair` 数组 / 容器）中非常实用：

```cpp
#include <algorithm>  // sort 所在头文件
#include <vector>

int main() {
    vector<pair<int, string>> vec = {
        {3, "C"},
        {1, "A"},
        {2, "B"},
        {1, "D"}
    };
    
    // 按 pair 自带的比较规则排序（先比 first，再比 second）
    sort(vec.begin(), vec.end());
    
    // 输出排序结果：(1,"A"), (1,"D"), (2,"B"), (3,"C")
    for (auto& p : vec) {
        cout << "(" << p.first << "," << p.second << ") ";
    }
    return 0;
}
```

#### 4. 函数返回多个值

C++ 函数默认只能返回一个值，若需返回两个关联值（如 “计算结果 + 状态码”“最大值 + 索引”），`pair` 是常用方案（替代自定义结构体的简化选择）：

```cpp
// 函数：返回两个整数的 最大值 和 最小值
pair<int, int> getMaxAndMin(int a, int b) {
    if (a > b) {
        return {a, b};  // 返回最大值在前，最小值在后
    } else {
        return {b, a};
    }
}

int main() {
    auto res = getMaxAndMin(5, 9);
    cout << "最大值：" << res.first << endl;  // 输出 9
    cout << "最小值：" << res.second << endl; // 输出 5
    return 0;
}
```

#### 5. 作为容器的元素

`pair` 常作为关联容器（如 `map`、`unordered_map`）的**内部存储单元**—— 这些容器的 “键值对” 本质就是 `pair`（`map<K, V>` 中存储的是 `pair<const K, V>`，`const` 确保键不可修改）。

也可以作为 `vector`、`list` 等序列容器的元素，存储结构化数据：

```cpp
#include <unordered_map>

int main() {
    // unordered_map 的每个元素是 pair<const string, int>
    unordered_map<string, int> scoreMap = {
        {"Alice", 95},
        {"Bob", 88}
    };
    
    // 遍历 unordered_map：迭代器指向的是 pair 对象
    for (auto& iter : scoreMap) {
        // iter.first 是键（string），iter.second 是值（int）
        cout << iter.first << " 的分数：" << iter.second << endl;
    }
    
    // vector 存储 pair（比如存储学生的“学号+分数”）
    vector<pair<int, int>> studentScores = {
        {101, 92},
        {102, 85}
    };
    return 0;
}
```

#### 6. 嵌套使用（pair 套 pair）

若需要打包 **3 个或更多数据**，可以嵌套 `pair`（但可读性较差，超过 2 个数据建议用 `tuple` 或自定义结构体）：

```cpp
// 存储“姓名（string）+ 年龄（int）+ 身高（double）”
auto person = make_pair("Zhang San", make_pair(20, 175.5));

// 访问嵌套的 second：先取外层 second，再取内层 first/second
cout << "姓名：" << person.first << endl;          // 输出 "Zhang San"
cout << "年龄：" << person.second.first << endl;   // 输出 20
cout << "身高：" << person.second.second << endl;  // 输出 175.5
```

### 三、pair 的替代方案：tuple

如果需要打包 **3 个及以上不同类型的数据**，`pair` 嵌套会导致代码可读性下降，此时推荐使用 C++11 引入的 `tuple`（`<tuple>` 头文件）——`tuple` 支持任意数量的模板参数，例如 `tuple<string, int, double>` 可直接存储 “姓名 + 年龄 + 身高”，无需嵌套。

但 `pair` 在 “仅需两个数据” 的场景中（如键值对、双值返回），比 `tuple` 更简洁、易用。

### 四、常见误区

1. **忘记包含头文件**：使用 `pair` 必须包含 `<utility>` 头文件，否则编译器会报 “未定义标识符 pair” 错误（`map`/`unordered_map` 会间接包含 `<utility>`，但显式包含更规范）。
2. **修改 `map` 中的 `pair.first`**：`map` 存储的是 `pair<const K, V>`，`first`（键）是 `const` 类型，无法修改（若需修改键，需先删除旧键值对，再插入新键值对）。
3. **`auto` 推导歧义**：直接写 `auto p = {1, "a"}` 时，编译器可能推导为 `initializer_list` 而非 `pair`，建议显式用 `make_pair` 或 `pair<int, string>{1, "a"}` 避免歧义。