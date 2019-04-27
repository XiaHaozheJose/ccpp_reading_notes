[TOC]


## 1. pointer(指针) VS references(引用)

### 1. 允许 null pointer

```c++
#include <iostream>

int main()
{
  int* ptr;
}
```

编译 ok

```
 ~/Desktop/main  g++ -c main.cpp
 ~/Desktop/main 
```

### 2. 不允许 null references

```c++
#include <iostream>

int main()
{
  int& ref;
}
```

编译报错

```c++
 ~/Desktop/main  g++ -c main.cpp
main.cpp:5:8: error: declaration of reference variable 'ref' requires an initializer
  int& ref;
       ^~~
1 error generated.
```

- 一个 **references** 必须代表一个 **变量**
- 且必须在 **定义** references 时，指定 **关联** 一个变量

- 不允许 null references 为了禁止出现 null pointer

### 3. 函数参数 pointer

```c++
#include <iostream>

void func(int* p) {
  /**
   * 需要判断传入的【指针变量】是否有指向【可用】的【内存地址】
   */
  if (!p) return;
  
  *p = 99;
}

int main()
{
  int* p = NULL;
  func(p);
}
```

### 4. 函数参数 references

```c++
#include <iostream>

void func(int& p) {
  /**
   * 完全不需要添加像 pointer 判空的逻辑
   * 直接使用 ref 变量即可
   */
  p = 99;
}

int main()
{
  int age = 98;
  std::cout << "age = " << age << std::endl;
  
  func(age);
  std::cout << "age = " << age << std::endl;
}
```

```
 ~/Desktop/main  make lan=c++
g++ main.cpp
./a.out
age = 98
age = 99
```

### 5. pointer 可以【重新】赋值

```c++
#include <iostream>

int main()
{
  int age = 98;
  int level = 99;
  
  // 第一次 赋值 pointer 变量
  int* p1 = &age;

  // 第二次 赋值 pointer 变量
  p1 = &level;
}
```

```
 ~/Desktop/main  make lan=c++
g++ main.cpp
./a.out
 ~/Desktop/main 
```

运行正常。

### 6. references【不可以】【重新】赋值

#### 1. 假象

```c++
#include <iostream>

int main()
{
  int age = 98;
  int level = 99;
  
  // 第一次 赋值 references 变量
  int& ref = age;
  std::cout << "1. ref = " << ref << std::endl;

  // 第二次 赋值 references 变量
  ref = level;
  std::cout << "2. ref = " << ref << std::endl;
}
```

```
 ~/Desktop/main  make lan=c++
g++ main.cpp
./a.out
1. ref = 98
2. ref = 99
```

- 看起来在 **第二次** 将 level 变量，赋值 references 变量后，
-  references 变量的值，变成了 第二个 level 变量的值
- 那是否是意味着 **references 变量**，也关 **联换** 成了 第二个 level 变量 ？？？

#### 2. 打印两次 references 变量的内存地址

```c++
#include <iostream>

int main()
{
  int age = 98;
  int level = 99;
  
  // 第一次 赋值 references 变量
  int& ref = age;
  std::cout << "1. ref = " << ref << " , &ref = " << &ref << std::endl;

  // 第二次 赋值 references 变量
  ref = level;
  std::cout << "2. ref = " << ref << " , &ref = " << &ref << std::endl;
}
```

```
 ~/Desktop/main  make lan=c++
g++ main.cpp
./a.out
1. ref = 98 , &ref = 0x7ffee234b11c
2. ref = 99 , &ref = 0x7ffee234b11c
```

- references 变量 **内存地址** 没有发生改变
- 也就是说或 references 变量 **并没有** 替换关联的 age 变量
- 那么对于第二次 `ref = level;` 
  - 1) 并 **不会** 让 ref 关联到 **level 变量**
  - 2) 只是将  level 变量的 **值** 赋值给 ref 关联的 **age 变量**
- 结论: 在 **定义** references 变量时，指定 **关联的变量** 之后，就 **不会再改变** 关联其他的变量

### 7. 如果在不同时间，需要指向不同的内存地址，则使用 pointer

#### 1. 错误: `std::vector<int&>`

```c++
#include <iostream>
#include <vector>

int main()
{
  std::vector<int&> v;
}
```

```c++
error: 'pointer' declared as a
      pointer to a reference of type 'int &'
    typedef _Tp*              pointer;
```

- 但是对于` vector[i]` 很可能是需要在后面某个时刻，才会对齐设置元素的
- 但是对于 **references** 必须在 定义时 就指定关联的变量

#### 2. 正确: `std::vector<int*>`

```c++
#include <iostream>
#include <vector>

int main()
{
  std::vector<int*> v;
}
```

```
 ~/Desktop/main  make lan=c++
g++ main.cpp
./a.out
```

### 8. 方法返回值 pointer vs references

#### 1. 返回 pointer

```c++
#include <iostream>
#include <string>
using namespace std;

const int SIZE = 10;

class safearay
{
private:
  int arr[SIZE];

public:
  safearay() 
  {
    register int i;

    for(i = 0; i < SIZE; i++)
    {
      arr[i] = i;
    }
  }

  // 返回的 &arr[i] 内存地址
  int* operator[](int i)
  {
    if( i > SIZE )
    {
      cout << "索引超过最大值" << endl; 
      return &arr[0]; // 返回第一个元素
    }

    return &arr[i];
  }
};


int main()
{
  safearay A;

  // 读取 arr[i]
  cout << "*A[2] 的值为 : " << *A[2] << endl;

  //-------------------------------------------
  // 修改 arr[i]
  *A[2] = 99;
  //-------------------------------------------

  // 读取 arr[i]
  cout << "*A[2] 的值为 : " << *A[2] << endl;
}
```

```
 ~/Desktop/main  make lan=c++
g++ main.cpp
./a.out
*A[2] 的值为 : 2
*A[2] 的值为 : 99
```

#### 2. 返回 references

```c++
#include <iostream>
#include <string>
using namespace std;

const int SIZE = 10;

class safearay
{
private:
  int arr[SIZE];

public:
  safearay() 
  {
    register int i;

    for(i = 0; i < SIZE; i++)
    {
      arr[i] = i;
    }
  }

  // 返回 arr[i] 引用
  int& operator[](int i)
  {
    if( i > SIZE )
    {
      cout << "索引超过最大值" << endl; 
      return arr[0]; // 返回第一个元素
    }

    return arr[i];
  }
};


int main()
{
  safearay A;

  // 读取 arr[i]
  cout << "A[2] 的值为 : " << A[2] << endl;

  //-------------------------------------------
  // 修改 arr[i]
  A[2] = 99;
  //-------------------------------------------

  // 读取 arr[i]
  cout << "A[2] 的值为 : " << A[2] << endl;
}
```

```
 ~/Desktop/main  make lan=c++
g++ main.cpp
./a.out
A[2] 的值为 : 2
A[2] 的值为 : 99
```

#### 3. 对比 `operator[](int i)` 返回值类型

```c++
// 返回的 &arr[i] 内存地址
int* operator[](int i)
{
  if( i > SIZE )
  {
    cout << "索引超过最大值" << endl; 
    return &arr[0]; // 返回第一个元素
  }

  return &arr[i];
}
```

- 读取: `*A[2]`
- 写入: `*A[2] = 99`

```c++
// 返回 arr[i] 引用
int& operator[](int i)
{
  if( i > SIZE )
  {
    cout << "索引超过最大值" << endl; 
    return arr[0]; // 返回第一个元素
  }

  return arr[i];
}
```

- 读取: `A[2]`
- 写入: `A[2] = 99`

完全去除了我们自己 **取地址** 和 **解引用(反地址)** 的操作，由 C++ 编译器替我们完成。



