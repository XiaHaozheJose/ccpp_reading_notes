[TOC]



## 1. 静态链接: 将所有的 ==目标文件== 链接生成 ==可执行文件==

![](01.png)

![](02.png)



## 2. 静态链接 解决的问题: 如何将多个目标文件的 ==各种段== 取出来再进行 ==合并==

![](03.png)

- 1) 取出 A.o , B.o , C.o 的 **text section** 合并到 a.out
- 2) 取出 A.o , B.o , C.o 的 **data section** 合并到 a.out



## 3. 简单的合并多个目标文件: 依次往下叠加

![](04.png)

存在的 **缺点** :

- 会存在大量的 **零散** 段 (碎片化)
- 会造成 **可执行文件的体积** 较大
- 也会导致 **占用的内存** 也比较大



## 4. 实际的合并多个目标文件: ==相似段== 进行合并

![](05.png)

![](06.png)



## 5. 链接器: 为 ==目标文件== 分配 ==空间== 与 ==地址==

![](07.png)

- 1) **链接** 结束，最终存在于 **可执行文件** 中的占用的 **文件** 空间
- 2) 可执行文件被 **加载到内存** 分配到的 **内存** 空间



## 6. Two-pass Linking (两步链接): 链接分为 2个 步骤

![](08.png)



## 7. ld `-e` 指定可执行文件的 ==入口符号==

![](09.png)



## 8. 对比 链接 前后 地址分配 情况

### 1. 代码

```c
//xzh@xzh-VirtualBox:~/src$ cat people.c
int age = 99;

void run(){}
```

```c
//xzh@xzh-VirtualBox:~/src$ cat main.c
extern int age;
extern void run();

int main(int argv, char* argr[])
{
	int x = age;
	run();
}
```

### 2. objdump -h main.o

```
xzh@xzh-VirtualBox:~/src$ gcc -c main.c
```

```
xzh@xzh-VirtualBox:~/src$ objdump -h main.o

main.o:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000029  0000000000000000  0000000000000000  00000040  2**0
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, CODE
  1 .data         00000000  0000000000000000  0000000000000000  00000069  2**0
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000000  0000000000000000  0000000000000000  00000069  2**0
                  ALLOC
  3 .comment      0000002b  0000000000000000  0000000000000000  00000069  2**0
                  CONTENTS, READONLY
  4 .note.GNU-stack 00000000  0000000000000000  0000000000000000  00000094  2**0
                  CONTENTS, READONLY
  5 .eh_frame     00000038  0000000000000000  0000000000000000  00000098  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, DATA
```

### 3. objdump -h people.o

```
xzh@xzh-VirtualBox:~/src$ gcc -c people.c
```

```
xzh@xzh-VirtualBox:~/src$ objdump -h people.o

people.o:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000007  0000000000000000  0000000000000000  00000040  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
  1 .data         00000004  0000000000000000  0000000000000000  00000048  2**2
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000000  0000000000000000  0000000000000000  0000004c  2**0
                  ALLOC
  3 .comment      0000002b  0000000000000000  0000000000000000  0000004c  2**0
                  CONTENTS, READONLY
  4 .note.GNU-stack 00000000  0000000000000000  0000000000000000  00000077  2**0
                  CONTENTS, READONLY
  5 .eh_frame     00000038  0000000000000000  0000000000000000  00000078  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, DATA
```

### 4. objdump -h a.out

```
xzh@xzh-VirtualBox:~/src$ gcc main.o people.o
```

```
xzh@xzh-VirtualBox:~/src$ objdump -h a.out

a.out:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .interp       0000001c  0000000000000238  0000000000000238  00000238  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  1 .note.ABI-tag 00000020  0000000000000254  0000000000000254  00000254  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  2 .note.gnu.build-id 00000024  0000000000000274  0000000000000274  00000274  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  3 .gnu.hash     0000001c  0000000000000298  0000000000000298  00000298  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .dynsym       00000090  00000000000002b8  00000000000002b8  000002b8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  5 .dynstr       0000007d  0000000000000348  0000000000000348  00000348  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  6 .gnu.version  0000000c  00000000000003c6  00000000000003c6  000003c6  2**1
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  7 .gnu.version_r 00000020  00000000000003d8  00000000000003d8  000003d8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  8 .rela.dyn     000000c0  00000000000003f8  00000000000003f8  000003f8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  9 .init         00000017  00000000000004b8  00000000000004b8  000004b8  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 10 .plt          00000010  00000000000004d0  00000000000004d0  000004d0  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 11 .plt.got      00000008  00000000000004e0  00000000000004e0  000004e0  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 12 .text         000001b2  00000000000004f0  00000000000004f0  000004f0  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 13 .fini         00000009  00000000000006a4  00000000000006a4  000006a4  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 14 .rodata       00000004  00000000000006b0  00000000000006b0  000006b0  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 15 .eh_frame_hdr 00000044  00000000000006b4  00000000000006b4  000006b4  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 16 .eh_frame     00000128  00000000000006f8  00000000000006f8  000006f8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 17 .init_array   00000008  0000000000200df0  0000000000200df0  00000df0  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 18 .fini_array   00000008  0000000000200df8  0000000000200df8  00000df8  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 19 .dynamic      000001c0  0000000000200e00  0000000000200e00  00000e00  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 20 .got          00000040  0000000000200fc0  0000000000200fc0  00000fc0  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 21 .data         00000014  0000000000201000  0000000000201000  00001000  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 22 .bss          00000004  0000000000201014  0000000000201014  00001014  2**0
                  ALLOC
 23 .comment      0000002a  0000000000000000  0000000000000000  00001014  2**0
                  CONTENTS, READONLY
```

### 5. 三个输出的区别

- 1、main.o、people.o 输出中的 **VMA** 和 **LMA** 都是 **0000000000000000**
- 2、a.out 输出中的 **VMA** 和 **LMA** 都是有 **实际值** 的，而且都不一样

### 6. VMA 和 LMA

![](10.png)

### 7. 链接前 和 链接后，代码中使用的都是 ==虚拟地址==

![](11.png)



## 9. 目标文件 / 可执行文件 / 进程空间

![](12.png)

- 1) 目标文件 ==> **链接** ==> 可执行文件
- 2) 可执行文件 ==> **加载** ==> 内存中分配虚拟内存存储空间



## 10. 为什么给 text(段) 和 data(段) 分配 ==固定== 起始地址？

![](13.png)

- 各个操作系统的不同而不同
- 因为最终负责 **衔接硬件** 那部分 **启动代码**  是由 **各个操作系统** 提供
- 那么也就有不同的 **起始地址 分配规则**



## 11. 链接器 通过 ==段内 偏移== 计算 各个符号 虚拟地址

![](14.png)



## 12. 链接器 处理 目标文件内的 ==外部符号引用==

### 1. objdump -d xx.o 反汇编

![](15.png)

### 2. 此时 main 符号的 起始地址 是 0

![](16.png)

### 3. 如何看 objdump -d 输出

![](17.png)

### 4. main.o 引用 外部 shard 变量

![](18.png)

----

![](19.png)

### 5. main.o 引用 外部 swap 函数 (近址相对位移调用)

![](20.png)

### 6. call 跳转的地址 = 0x2b + (-4) = 0x27 (一个 ==临时的假地址==)

![](21.png)

----

![](22.png)

### 7. ==链接器== 对需要进行 ==重定位的指令== 进行 ==地址修正==

![](23.png)

### 8. 链接之后的 ==可执行文件== 内的 外部符号引用 ==地址修正==

![](24.png)



## 13. 重定位表: 记录 ==谁要== 要修正、==哪部分== 需要修正、==如何== 修正

![](25.png)



## 14. 重定位==表== == 重定位==段== (==objdump -r== 查看重定位段)

![](26.png)



## 15. 重定位表(段): 一个 elf rel struct 实例 数组

> /usr/include/elf.h

### 1. 32 位 elf rel struct

```c
/* Relocation table entry without addend (in section of type SHT_REL).  */

typedef struct
{
  Elf32_Addr  r_offset;   /* Address */
  Elf32_Word  r_info;     /* Relocation type and symbol index */
} Elf32_Rel;
```

### 2. 64 位 elf rel struct

```c
/* I have seen two different definitions of the Elf64_Rel and
   Elf64_Rela structures, so we'll leave them out until Novell (or
   whoever) gets their act together.  */
/* The following, at least, is used on Sparc v9, MIPS, and Alpha.  */

typedef struct
{
  Elf64_Addr  r_offset;   /* Address */
  Elf64_Xword r_info;     /* Relocation type and symbol index */
} Elf64_Rel;
```

### 3. struct 成员含义

![](27.png)



## 16. 链接 时，会经常报错: 符号 ==未定义==

![](28.png)



## 17. 链接器 从 ==全局符号表== 中查找 ==未定义符号的引用== 进行 ==重定位==

![](29.png)



## 18. COMMON (COM) 块

### 1. 在不同文件中，相同名字 的符号，但是 ==类型== 不一致的情况

![](30.png)

### 2. COMMON (COM) 机制，最早源于 Fortran

![](31.png)

### 3. 链接器 处理 ==弱符号== 时，与合并 COMMON (COM) 一样，选择 ==长度最大== 的

![](32.png)

### 4. 早期 链接器 当处理 相同名字 sizeof(weak 符号) > sizeof(strong 符号) 会报警告

![](33.png)

### 5. 总结为何 编译器 会将 ==没初始化的全局变量的符号== 放在 bss(COM) 

![](34.png)



## 19. 全局变量、static 全局变量、static 局部变量(未初始化 和 已经初始化)

### 1. main.c

```c
//xzh@xzh-VirtualBox:~/src$ cat main.c
int var1 = 99;
static int var2 = 100;

int var3;
static int var4;

int main(int argv, char* argr[])
{
	static int var5;
	static int var6 = 101;
}
```

### 2. 编译 阶段

```
xzh@xzh-VirtualBox:~/src$ gcc -c main.c
```

#### 1. 所有的 section

```
xzh@xzh-VirtualBox:~/src$ readelf -S main.o
There are 11 section headers, starting at offset 0x2d8:

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .text             PROGBITS         0000000000000000  00000040
       0000000000000012  0000000000000000  AX       0     0     1
  [ 2] .data             PROGBITS         0000000000000000  00000054
       000000000000000c  0000000000000000  WA       0     0     4
  [ 3] .bss              NOBITS           0000000000000000  00000060
       0000000000000008  0000000000000000  WA       0     0     4
  [ 4] .comment          PROGBITS         0000000000000000  00000060
       000000000000002b  0000000000000001  MS       0     0     1
  [ 5] .note.GNU-stack   PROGBITS         0000000000000000  0000008b
       0000000000000000  0000000000000000           0     0     1
  [ 6] .eh_frame         PROGBITS         0000000000000000  00000090
       0000000000000038  0000000000000000   A       0     0     8
  [ 7] .rela.eh_frame    RELA             0000000000000000  00000268
       0000000000000018  0000000000000018   I       8     6     8
  [ 8] .symtab           SYMTAB           0000000000000000  000000c8
       0000000000000168  0000000000000018           9    12     8
  [ 9] .strtab           STRTAB           0000000000000000  00000230
       0000000000000035  0000000000000000           0     0     1
  [10] .shstrtab         STRTAB           0000000000000000  00000280
       0000000000000054  0000000000000000           0     0     1
```

#### 2. 符号表

```
xzh@xzh-VirtualBox:~/src$ readelf -s main.o

Symbol table '.symtab' contains 15 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS main.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    2
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    3
     5: 0000000000000004     4 OBJECT  LOCAL  DEFAULT    2 var2
     6: 0000000000000000     4 OBJECT  LOCAL  DEFAULT    3 var4
     7: 0000000000000008     4 OBJECT  LOCAL  DEFAULT    2 var6.1801
     8: 0000000000000004     4 OBJECT  LOCAL  DEFAULT    3 var5.1800
     9: 0000000000000000     0 SECTION LOCAL  DEFAULT    5
    10: 0000000000000000     0 SECTION LOCAL  DEFAULT    6
    11: 0000000000000000     0 SECTION LOCAL  DEFAULT    4
    12: 0000000000000000     4 OBJECT  GLOBAL DEFAULT    2 var1
    13: 0000000000000004     4 OBJECT  GLOBAL DEFAULT  COM var3
    14: 0000000000000000    18 FUNC    GLOBAL DEFAULT    1 main
```

#### 3. var1 ~ var6 符号存储位置

- 1) var1 -- 2号 -- data 段
- 2) var2 -- 2号 -- data 段
- 3) var3 -- 弱符号 -- COM 段
- 4) var4 -- 弱符号 -- bss 段
- 5) var5 -- 弱符号 -- bss 段
- 6) var6 -- 2号 -- data 段

### 3. 链接 阶段

```
xzh@xzh-VirtualBox:~/src$ gcc main.o
```

#### 1. 所有的 section

```
xzh@xzh-VirtualBox:~/src$ readelf -S a.out
There are 28 section headers, starting at offset 0x19a8:

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .interp           PROGBITS         0000000000000238  00000238
       000000000000001c  0000000000000000   A       0     0     1
  [ 2] .note.ABI-tag     NOTE             0000000000000254  00000254
       0000000000000020  0000000000000000   A       0     0     4
  [ 3] .note.gnu.build-i NOTE             0000000000000274  00000274
       0000000000000024  0000000000000000   A       0     0     4
  [ 4] .gnu.hash         GNU_HASH         0000000000000298  00000298
       000000000000001c  0000000000000000   A       5     0     8
  [ 5] .dynsym           DYNSYM           00000000000002b8  000002b8
       0000000000000090  0000000000000018   A       6     1     8
  [ 6] .dynstr           STRTAB           0000000000000348  00000348
       000000000000007d  0000000000000000   A       0     0     1
  [ 7] .gnu.version      VERSYM           00000000000003c6  000003c6
       000000000000000c  0000000000000002   A       5     0     2
  [ 8] .gnu.version_r    VERNEED          00000000000003d8  000003d8
       0000000000000020  0000000000000000   A       6     1     8
  [ 9] .rela.dyn         RELA             00000000000003f8  000003f8
       00000000000000c0  0000000000000018   A       5     0     8
  [10] .init             PROGBITS         00000000000004b8  000004b8
       0000000000000017  0000000000000000  AX       0     0     4
  [11] .plt              PROGBITS         00000000000004d0  000004d0
       0000000000000010  0000000000000010  AX       0     0     16
  [12] .plt.got          PROGBITS         00000000000004e0  000004e0
       0000000000000008  0000000000000008  AX       0     0     8
  [13] .text             PROGBITS         00000000000004f0  000004f0
       0000000000000192  0000000000000000  AX       0     0     16
  [14] .fini             PROGBITS         0000000000000684  00000684
       0000000000000009  0000000000000000  AX       0     0     4
  [15] .rodata           PROGBITS         0000000000000690  00000690
       0000000000000004  0000000000000004  AM       0     0     4
  [16] .eh_frame_hdr     PROGBITS         0000000000000694  00000694
       000000000000003c  0000000000000000   A       0     0     4
  [17] .eh_frame         PROGBITS         00000000000006d0  000006d0
       0000000000000108  0000000000000000   A       0     0     8
  [18] .init_array       INIT_ARRAY       0000000000200df0  00000df0
       0000000000000008  0000000000000008  WA       0     0     8
  [19] .fini_array       FINI_ARRAY       0000000000200df8  00000df8
       0000000000000008  0000000000000008  WA       0     0     8
  [20] .dynamic          DYNAMIC          0000000000200e00  00000e00
       00000000000001c0  0000000000000010  WA       6     0     8
  [21] .got              PROGBITS         0000000000200fc0  00000fc0
       0000000000000040  0000000000000008  WA       0     0     8
  [22] .data             PROGBITS         0000000000201000  00001000
       000000000000001c  0000000000000000  WA       0     0     8
  [23] .bss              NOBITS           000000000020101c  0000101c
       0000000000000014  0000000000000000  WA       0     0     4
  [24] .comment          PROGBITS         0000000000000000  0000101c
       000000000000002a  0000000000000001  MS       0     0     1
  [25] .symtab           SYMTAB           0000000000000000  00001048
       0000000000000648  0000000000000018          26    46     8
  [26] .strtab           STRTAB           0000000000000000  00001690
       0000000000000218  0000000000000000           0     0     1
  [27] .shstrtab         STRTAB           0000000000000000  000018a8
       00000000000000f9  0000000000000000           0     0     1
```

#### 2. 符号表

```
xzh@xzh-VirtualBox:~/src$ readelf -s a.out

Symbol table '.dynsym' contains 6 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     4: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
     5: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@GLIBC_2.2.5 (2)

Symbol table '.symtab' contains 67 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000238     0 SECTION LOCAL  DEFAULT    1
     2: 0000000000000254     0 SECTION LOCAL  DEFAULT    2
     3: 0000000000000274     0 SECTION LOCAL  DEFAULT    3
     4: 0000000000000298     0 SECTION LOCAL  DEFAULT    4
     5: 00000000000002b8     0 SECTION LOCAL  DEFAULT    5
     6: 0000000000000348     0 SECTION LOCAL  DEFAULT    6
     7: 00000000000003c6     0 SECTION LOCAL  DEFAULT    7
     8: 00000000000003d8     0 SECTION LOCAL  DEFAULT    8
     9: 00000000000003f8     0 SECTION LOCAL  DEFAULT    9
    10: 00000000000004b8     0 SECTION LOCAL  DEFAULT   10
    11: 00000000000004d0     0 SECTION LOCAL  DEFAULT   11
    12: 00000000000004e0     0 SECTION LOCAL  DEFAULT   12
    13: 00000000000004f0     0 SECTION LOCAL  DEFAULT   13
    14: 0000000000000684     0 SECTION LOCAL  DEFAULT   14
    15: 0000000000000690     0 SECTION LOCAL  DEFAULT   15
    16: 0000000000000694     0 SECTION LOCAL  DEFAULT   16
    17: 00000000000006d0     0 SECTION LOCAL  DEFAULT   17
    18: 0000000000200df0     0 SECTION LOCAL  DEFAULT   18
    19: 0000000000200df8     0 SECTION LOCAL  DEFAULT   19
    20: 0000000000200e00     0 SECTION LOCAL  DEFAULT   20
    21: 0000000000200fc0     0 SECTION LOCAL  DEFAULT   21
    22: 0000000000201000     0 SECTION LOCAL  DEFAULT   22
    23: 000000000020101c     0 SECTION LOCAL  DEFAULT   23
    24: 0000000000000000     0 SECTION LOCAL  DEFAULT   24
    25: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    26: 0000000000000520     0 FUNC    LOCAL  DEFAULT   13 deregister_tm_clones
    27: 0000000000000560     0 FUNC    LOCAL  DEFAULT   13 register_tm_clones
    28: 00000000000005b0     0 FUNC    LOCAL  DEFAULT   13 __do_global_dtors_aux
    29: 000000000020101c     1 OBJECT  LOCAL  DEFAULT   23 completed.7696
    30: 0000000000200df8     0 OBJECT  LOCAL  DEFAULT   19 __do_global_dtors_aux_fin
    31: 00000000000005f0     0 FUNC    LOCAL  DEFAULT   13 frame_dummy
    32: 0000000000200df0     0 OBJECT  LOCAL  DEFAULT   18 __frame_dummy_init_array_
    33: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS main.c
    34: 0000000000201014     4 OBJECT  LOCAL  DEFAULT   22 var2
    35: 0000000000201020     4 OBJECT  LOCAL  DEFAULT   23 var4
    36: 0000000000201018     4 OBJECT  LOCAL  DEFAULT   22 var6.1801
    37: 0000000000201024     4 OBJECT  LOCAL  DEFAULT   23 var5.1800
    38: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    39: 00000000000007d4     0 OBJECT  LOCAL  DEFAULT   17 __FRAME_END__
    40: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS
    41: 0000000000200df8     0 NOTYPE  LOCAL  DEFAULT   18 __init_array_end
    42: 0000000000200e00     0 OBJECT  LOCAL  DEFAULT   20 _DYNAMIC
    43: 0000000000200df0     0 NOTYPE  LOCAL  DEFAULT   18 __init_array_start
    44: 0000000000000694     0 NOTYPE  LOCAL  DEFAULT   16 __GNU_EH_FRAME_HDR
    45: 0000000000200fc0     0 OBJECT  LOCAL  DEFAULT   21 _GLOBAL_OFFSET_TABLE_
    46: 0000000000000680     2 FUNC    GLOBAL DEFAULT   13 __libc_csu_fini
    47: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
    48: 0000000000201000     0 NOTYPE  WEAK   DEFAULT   22 data_start
    49: 000000000020101c     0 NOTYPE  GLOBAL DEFAULT   22 _edata
    50: 0000000000000684     0 FUNC    GLOBAL DEFAULT   14 _fini
    51: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@@GLIBC_
    52: 0000000000201000     0 NOTYPE  GLOBAL DEFAULT   22 __data_start
    53: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    54: 0000000000201008     0 OBJECT  GLOBAL HIDDEN    22 __dso_handle
    55: 0000000000000690     4 OBJECT  GLOBAL DEFAULT   15 _IO_stdin_used
    56: 0000000000000610   101 FUNC    GLOBAL DEFAULT   13 __libc_csu_init
    57: 0000000000201030     0 NOTYPE  GLOBAL DEFAULT   23 _end
    58: 0000000000201010     4 OBJECT  GLOBAL DEFAULT   22 var1
    59: 00000000000004f0    43 FUNC    GLOBAL DEFAULT   13 _start
    60: 0000000000201028     4 OBJECT  GLOBAL DEFAULT   23 var3
    61: 000000000020101c     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    62: 00000000000005fa    18 FUNC    GLOBAL DEFAULT   13 main
    63: 0000000000201020     0 OBJECT  GLOBAL HIDDEN    22 __TMC_END__
    64: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
    65: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@@GLIBC_2.2
    66: 00000000000004b8     0 FUNC    GLOBAL DEFAULT   10 _init
```

#### 3. var1 ~ var6 符号存储位置

- 1) var1 -- 22号 -- data 段
- 2) var2 -- 22号 -- data 段
- 3) var3 -- 23号 -- bss 段
- 4) var4 -- 23号 -- bss 段
- 5) var5 -- 23号 -- bss 段
- 6) var6 -- 22号 -- data 段

最终符号就只存在 **data 和 bss** 两种段，因为 **COM** 中的符号，会被转义到 **bss** 段中。

### 4. 总结下上述 main.c 中变量 对应的 ==符号== 存储位置

#### 1. 编译 阶段

```c
//xzh@xzh-VirtualBox:~/src$ cat main.c
int var1 = 99; // .data
static int var2 = 100; // .data

int var3; // COM
static int var4; // .bss

int main(int argv, char* argr[])
{
	static int var5; // .bss
	static int var6 = 101; // .data
}
```

#### 2. 链接 阶段

```c
//xzh@xzh-VirtualBox:~/src$ cat main.c
int var1 = 99; // 不变
static int var2 = 100; // 不变

int var3; // 【变化】COM ==> .bss
static int var4; // 不变

int main(int argv, char* argr[])
{
	static int var5; // 不变
	static int var6 = 101; // 不变
}
```






