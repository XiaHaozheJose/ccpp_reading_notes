[TOC]


## 1. c 也可以 oop

```c
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

///////////////////////// 1. 类 //////////////////////////

struct person 
{
  // 1.1 普通数据类型的对象成员
  int age;
  char name[64];

  /**
   *	1.2 【函数指针】类型的对象成员
   *	
   *	成员函数，必须包含【person对象指针】这个参数，
   *	被调用的函数，才能知道操作的是哪个person对象
   */
  int (*getAge)(struct person* this);
  void (*setAge)(struct person* this, int age);
  char* (*getName)(struct person* this);
  void (*setName)(struct person* this, char* name);
  void (*print_age)(struct person* this);
  void (*print_name)(struct person* this);
};

/////////////////// 2. 函数指针指向的具体c函数 //////////////////////

/** 
 *	所有的对象方法必须至少存在一个参数，
 *  接收当前要操作的是哪一个 struct person 对象，
 *  然后在函数中读写传入的struct person 对象
 */
int getAge(struct person* this)
{
  return this->age;
}

void setAge(struct person* this, int age)
{
  this->age = age;
}

char* getName(struct person* this)
{
  return this->name;
}
void setName(struct person* this, char* name)
{
  strcpy(this->name, name);
}

void print_age(struct person* this)
{
  printf("age = %d\n", this->age);
}

void print_name(struct person* this)
{
  printf("name = %s\n", this->name);
}
///////////////////////////////////////////////////////////

#if 1
int main()
{
  // 创建对象
  struct person p = {
    .age = 19,
    .name = "hahahah",
    .getAge = getAge,
    .setAge = setAge,
    .getName = getName,
    .setName = setName,
    .print_age = print_age,
    .print_name = print_name
  };

  // 调用对象的成员函数
  p.print_age(&p);
  p.print_name(&p);
}
#else
int main()
{
  // 创建对象
  struct person* p = (struct person*)malloc(sizeof(struct person));

  //【重要】初始化struct实例的成员函数指针变量，指向对应的具体c函数
  // 否则会报空指针错误，导致程序崩溃
  p->getAge = getAge;
  p->setAge = setAge;
  p->getName = getName;
  p->setName = setName;
  p->print_age = print_age;
  p->print_name = print_name;

  // 调用struct实例的成员函数指针变量指向的c函数
    // 从效果上开起来就好像调用成员函数似的
  p->setAge(p, 19);
  p->setName(p, "hahahah");
  p->print_age(p);
  p->print_name(p);

  //
  free(p);
}
#endif

```

c++ 类的 成员函数，也就是通过 **成员函数指针变量** 来实现的。

