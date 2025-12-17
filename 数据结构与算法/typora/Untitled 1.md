# priority_queue的自定义比较器

在 C++ 中，`priority_queue`（优先队列）的自定义比较器核心是**定义 “谁应该排在堆顶” 的规则**—— 默认是大顶堆（`<` 比较，大元素优先），自定义比较器需通过 “仿函数、lambda、函数指针” 等方式指定排序逻辑，以下是完整且易理解的实现方法：

### 一、核心原理

`priority_queue`的模板定义：

cpp



运行









```cpp
template <class T, class Container = vector<T>, class Compare = less<T>>
class priority_queue;
```

- `Compare`：比较器类型，返回`true`时，**第一个参数会被排在第二个参数后面**（即堆顶优先保留 “不满足该条件” 的元素）。
- 例如：`less<T>`（默认）→ `a < b` 为`true`时，`b`排在堆顶（大顶堆）；`greater<T>` → `a > b` 为`true`时，`b`排在堆顶（小顶堆）。

### 二、自定义比较器的 3 种常用写法

#### 方式 1：结构体 / 类仿函数（最通用，兼容所有 C++ 版本）

通过重载`()`运算符定义比较逻辑，适用于所有场景（包括自定义类型、`pair`、基础类型）。

##### 示例 1：基础类型（int）实现小顶堆

cpp



运行









```cpp
#include <iostream>
#include <queue>
using namespace std;

// 定义比较结构体（仿函数）
struct MyCompare {
    // 规则：a > b 时，a排在b后面 → 小顶堆（b更小，在堆顶）
    bool operator()(const int& a, const int& b) const {
        return a > b; 
    }
};

int main() {
    // 模板参数指定比较器类型
    priority_queue<int, vector<int>, MyCompare> pq;
    pq.push(3);
    pq.push(1);
    pq.push(5);

    // 输出：1 → 3 → 5（小顶堆，堆顶是最小元素）
    while (!pq.empty()) {
        cout << pq.top() << " ";
        pq.pop();
    }
    return 0;
}
```

##### 示例 2：自定义类型（比如节点）按指定字段排序

cpp



运行









```cpp
#include <iostream>
#include <queue>
#include <string>
using namespace std;

// 自定义类型：节点（id + 权重）
struct Node {
    int id;
    int weight;
    Node(int i, int w) : id(i), weight(w) {}
};

// 比较器：按weight升序（小顶堆），weight相等按id降序
struct NodeCompare {
    bool operator()(const Node& a, const Node& b) const {
        if (a.weight != b.weight) {
            return a.weight > b.weight; // weight小的优先
        }
        return a.id < b.id; // id大的优先
    }
};

int main() {
    priority_queue<Node, vector<Node>, NodeCompare> pq;
    pq.push(Node(1, 5));
    pq.push(Node(2, 3));
    pq.push(Node(3, 3));

    // 输出：(2,3) → (3,3) → (1,5)
    while (!pq.empty()) {
        auto top = pq.top();
        cout << "id: " << top.id << ", weight: " << top.weight << endl;
        pq.pop();
    }
    return 0;
}
```

#### 方式 2：lambda 表达式（C++11 及以上，简洁）

lambda 更灵活，但需注意：`priority_queue`的比较器参数需要是**可拷贝的类型**，因此需将 lambda 包装为`std::function`。

##### 示例：pair 类型按 second 降序排序

cpp



运行









```cpp
#include <iostream>
#include <queue>
#include <functional> // 必须包含该头文件
#include <utility>
using namespace std;

int main() {
    // 定义lambda比较器：按pair.second降序（大的优先）
    auto cmp = [](const pair<int, int>& a, const pair<int, int>& b) {
        return a.second < b.second; // a.second < b.second → b排在堆顶
    };

    // 模板参数指定function类型，构造函数传入lambda
    priority_queue<pair<int, int>, vector<pair<int, int>>, function<bool(const pair<int, int>&, const pair<int, int>&)>> pq(cmp);

    pq.push({1, 2});
    pq.push({2, 5});
    pq.push({3, 3});

    // 输出：(2,5) → (3,3) → (1,2)
    while (!pq.empty()) {
        auto top = pq.top();
        cout << top.first << " " << top.second << endl;
        pq.pop();
    }
    return 0;
}
```

#### 方式 3：函数指针（不推荐，仅作了解）

通过普通函数的指针作为比较器，缺点是不支持捕获外部变量，灵活性差。

cpp



运行









```cpp
#include <iostream>
#include <queue>
using namespace std;

// 普通比较函数
bool myCompare(int a, int b) {
    return a > b; // 小顶堆
}

int main() {
    // 模板参数指定函数指针类型，构造函数传入函数名
    priority_queue<int, vector<int>, decltype(&myCompare)> pq(myCompare);
    pq.push(3);
    pq.push(1);
    pq.push(5);

    // 输出：1 3 5
    while (!pq.empty()) {
        cout << pq.top() << " ";
        pq.pop();
    }
    return 0;
}
```

### 三、关键注意点

1. 比较器的返回值逻辑：

   - 返回`true` → 第一个参数 “优先级低”，排在第二个参数后面；

   - 返回

     ```
     false
     ```

      

     → 第一个参数 “优先级高”，排在堆顶方向。

     

     例如：想让

     ```
     x
     ```

     比

     ```
     y
     ```

     优先，需让

     ```
     cmp(x, y)
     ```

     返回

     ```
     false
     ```

     。

2. 自定义类型的比较器：

   - 若比较器涉及自定义类型的私有成员，可将比较器声明为该类的友元，或提供公开的访问接口。

3. 兼容性：

   - 仿函数（结构体）兼容所有 C++ 版本，是最推荐的写法；
   - lambda 需 C++11 及以上，结构化绑定等语法需更高版本。

### 四、常见错误避坑

- 错误：比较器返回`a < b`却想实现小顶堆 → 正确应为`a > b`；
- 错误：lambda 未用`function`包装 → 编译报错（`priority_queue`模板参数需类型，lambda 是匿名类型）；
- 错误：比较器未加`const` → 仿函数的`operator()`建议加`const`，避免编译警告。