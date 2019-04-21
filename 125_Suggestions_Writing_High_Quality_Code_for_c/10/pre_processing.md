[TOC]



## 1. 宏定义表达式必须使用括号

![](01.png)



## 2. 宏参数如果是 i++、++i、i--、--i

### 1. MIN(x, y) 宏定义

![](02.png)

### 2. MIN(type, x, y) 宏定义

![](03.png)



## 3. 尽量使用【inline 内联函数】而不是【宏定义】

### 1. inline 函数

![](04.png)

inline 函数类似于 宏定义，也是在 **编译时** 展开。

### 2. C99 static inline

![](05.png)



## 3. `#include <xx.h>` 与 `#include "xx.h"`

![](06.png)

![](07.png)



## 4. `#ifndef/#define/#endif`

![](08.png)



## 5. `#if/#elif/#elif/#else/#endif`

![](09.png)



## 6. defined

![](10.png)



## 7. `#undef/#define`

![](11.png)





