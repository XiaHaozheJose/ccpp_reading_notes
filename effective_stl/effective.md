[TOC]



## 1.【标准】STL 容器

### 1. ==线性/序列== 容器

| 容器        | 容器名称      | 迭代器类型      | 适用情况                      |
| :---------- | :------------ | :-------------- | ----------------------------- |
| std::string | C++ 字符串    | 随机 ramdom     | 字符串操作                    |
| std::vector | **可变数组**  | 随机 ramdom     | 常用数组结构，适用于 **查找** |
| std::list   | 双向 **链表** | **双向** double | 任意位置 **删除、插入** 元素  |
| std::deque  | 双向 **队列** | 随机 ramdom     | **尾部** 追加，**头部** 删除  |

### 2. ==关联== 类型容器（set、map）

#### 1. 容器元素【有序】排列

- 1、**关联** 类型容器
- 2、**insert()** 默认会对 **插入的元素**  自动进行 **排序**
- 3、 **容器** 会自己 **找到合适** 的插入位置，然后进行插入

| 容器                  | 容器描述                                   | 特点                              |
| :-------------------- | :----------------------------------------- | --------------------------------- |
| `std::set<T>`         | 内部使用 **二叉树排序数** 据就结构存储元素 | **不能** 存在 **多个相同** 的元素 |
| `std::multiset<T>`    | set 增强版                                 | **可以** 存在 **多个相同** 的元素 |
| `std::map<K, V>`      | **Key:value** 键值对存储                   | **不能** 存在多个 **相同的key**   |
| `std::multimap<K, V>` | map 增强版                                 | **可以** 存在多个 **相同的key**   |

#### 2. 容器元素【无序】排列

- 1、内部的 **数据结构** 与上面的 **有序** 版本的容器一样
- 2、唯一区别是 **不会** 对 **插入的元素** 进行 **排序**

| 容器                       | 容器描述                          |
| :------------------------- | :-------------------------------- |
| `std::unordered_set<T>`         | **不排序** 版本的 set 类模板      |
| `std::unordered_multiset<T>`    | **不排序** 版本的 multiset 类模板 |
| `std::unordered_map<K, V>`      | **不排序** 版本的 map 类模板      |
| `std::unordered_multimap<K, V>` | **不排序** 版本的 multimap 类模板 |

### 3. 各种 STL 标准容器 对比

#### 1. 图示

![](Snip20180316_30.png)

#### 2. string、vector、deque

- 1) 都属于 **随机读写** 容器内的元素
- 2) **元素读取** 效率是 **慢** 的
- 3) 但是元素的 **插入 或 删除** 效率比较 **低** 
- 4) **vector** (单端 - 数组) 容器元素 **只能** 限制在 **末尾** 进行 **追加**
- 5) **deque** (双端 - 数组) 容器元素 可以在 **头部** 和 **尾部**  进行 **插入**

#### 3. list

- 1) 虽然是 双向链表，但是需要 **从头至尾** 一个一个比较访问
- 2) list (双向链表) 可以在 **任意位置** **插入** 或 **删除** 元素
- 3) 元素 **读取** 效率是 **最慢** 的
- 4) 适用于经常 **插入** 或 **删除** 元素

#### 4. set、multiset

- 1) 内部都使用 **二叉树** 数据结构 **存储元素**

- 2) 元素 **读取** 效率是 **最高** 的
- 3) **不允许** 外接指定元素的 **插入位置** 

#### 5. map、multimap

- 1) 内部都使用 **二叉树** 数据结构 **存储元素** (同上)
- 2) 【map】key **不允许** 存在 **相同** 的，内部按照 **二叉搜索树** 存储，元素 **读取** 速度 **最高**
- 3) 【multimap】可以 **存在多个相同 key** ，所以 **key** 需要  **单向遍历** 
- 4) 同 set、multiset 都 **不允许** 外接指定元素的 **插入位置** (同上)

### 4. ==随机迭代== 容器选择

| 需求场景                          | STL 容器 |
| --------------------------------- | -------- |
| **字符串** 存储                   | string   |
| 容器 长度 **可变**                | vector   |
| 容器 长度 **不可变**              | array    |
| 面向 **数值计算** 的数组（C++11） | valarray |
| **两端** 增加 或 删除             | deque    |
| 只存储 **0 或 1** 状态值          | bitset (非 STL） |

### 5. ==插入与删除== 容器选择

| 需求场景                               | STL 容器          |
| -------------------------------------- | ----------------- |
| 可以在【任意位置】插入 或 删除         | list (双向链表)   |
| 【头部】和【尾部】【两端】插入 或 删除 | deque (双向队列)  |
| 【只允许】【尾部】插入 或 删除         | vector (单向数组) |

### 6. 要求 ==读取== 效率

#### 1. 元素 ==value== 形式

| 需求场景        | STL 容器 |
| --------------- | -------- |
| 元素 **不重复** | set      |
| 元素 **重复**   | multiset |

#### 2. 元素 ==key : value== 形式

| 需求场景                                    | STL 容器             |
| ------------------------------------------- | -------------------- |
| Key **不重复** ，对 插入元素 自动排序       | map                  |
| Key **重复** ，对 插入元素 自动排序         | multimap             |
| Key **不重复** ，**不对** 插入元素 自动排序 | `unordered_map`      |
| Key **重复** ，**不对** 插入元素 自动排序   | `unordered_multimap` |

### 7. ==队列== 数据结构

| 容器类型                 | 容器类型       | 数据结构   |
| ------------------------ | -------------- | ---------- |
| 标准 STL 容器            | deque          | 双向 队列  |
| 标准 STL 容器 **适配器** | queue          | 单向 队列  |
| 标准 STL 容器 **适配器** | priority_queue | 优先级队列 |

### 8. 检查 容器 ==是否空==

- 1、优先使用 **empty()** ，而不要使用 **size()**
- 2、**empty()** 访问的是容器的 **实例变量** 记录的值，消耗的是 **常数级** 的时间

```c++
if (容器.empty())
  return;
```



## 2.【非标准】STL容器

### 1. ==序列== 容器

| 容器  | 容器描述               |
| :---- | :--------------------- |
| slist | **单向** 链表          |
| rope  | 加强版 **std::string** |

### 2. ==关联== 容器

#### 1. hash set

- 1、`hash_set`

- 2、`hash_multiset`

#### 2. hash map

- 1、`hash_map`
- 2、`hash_multiset`

全部都是 **hash** 类型的容器。

#### 3. 使用 `unordered_set、unordered_map` 取代

- 1、上面的这些 **hash** 类型，都已经被 **废弃**

- 2、提示替换为 **unordered_set、unordered_map**

```c++
#include <iostream>
#include <ext/hash_set>
#include <ext/hash_map>

int main()
{}
```

```
➜  main make lan=c++
g++ main.cpp
In file included from main.cpp:2:
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/ext/hash_set:205:5: warning:
      Use of the header <ext/hash_set> is deprecated. Migrate to <unordered_set> [-W#warnings]
#   warning Use of the header <ext/hash_set> is deprecated.  Migrate to <unordered_set>
    ^
In file included from main.cpp:3:
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/ext/hash_map:212:5: warning:
      Use of the header <ext/hash_map> is deprecated. Migrate to <unordered_map> [-W#warnings]
#   warning Use of the header <ext/hash_map> is deprecated.  Migrate to <unordered_set>
    ^
2 warnings generated.
./a.out
➜  main
```

提示将 hash_set、hash_map 替换为 unordered_set、unordered_set。



## 3. 不要试图在 ==STL 容器基础== 上再 ==封装通用泛化型== 容器

### 1. 在【不同类型】容器，提供【不同功能】

- **序列** 类型的容器，**vector** 提供 **push_front()**、**push_back()** 等方法
- 而 **关联** 类型的容器，提供了比如 **lower_bound()**、**upper_bound()**、**equal_range()**、**count()** 等方法
- 即 **不同类型** 容器，本身的 **成员方法** 也是不同的
- STL 提供的 **不同类型** 容器 就是用来做 **不同的事**
- 所以不要对 **不同类型** 的 **容器** 进行 **泛化** 

### 2. 即使【相同类型】容器，也存在【差异性】

- 即使相同类型的 **容器**，对于 **insert()、erase()** 等操作也会有 **区别**
- **序列**容器 与 **关联**容器 在 **insert()** 的差别：
  - 对于 **关联** 类型容器
    - 会自动对插入元素进行排序
    - 找到合适的位置进行插入
    - 不能随意的指定元素的插入位置

- **序列**容器 与 **关联**容器 在 **erase()** 方法的 **返回值** 存在差别：
  - 对于**序列** 类型容器时，返回的是 **一个新的容器**
  - 而对于 **关联** 类型容器，则 **没有返回值**

### 3. 不同类型 STL 容器【交集很小】

- 比如尝试编写同时适用于 **vector、deque、list** 这三种容器的代码
- 不能使用 **reverse()、capacity()**，因为 **deque、list** 这两种容器 **不存在** 这两个操作
- 因为存在 **list** ，所以不能使用 **operator[]** 运算符重载
- **deque** 不支持 **随机** 访问
  - 只支持 **pop_front()、pop_back()** 访问头尾元素
  - 所以 **任何的随机访问** 也不能使用
- **vector** 没有 **push_front()、pop_front()** ，所以也不能使用
- 只有 **vector** 支持把容器中的元素，传递到某个C语法的接口中

结果：最终泛化容器能提供的接口 **很少**。

### 4. 结论：不要试图编写泛化通用型的 STL 容器

...



## 4. STL 容器默认存储 元素 的 ==浅拷贝==



