[TOC]



## 1. 目标文件

- 1) 源码 **经过编译**，但是 **没有链接** 的中间文件
- 2) windows : xx.obj
- 3) linux/unix/macosx : xx.o
- 4) 目标文件 **文件格式** 与 可执行文件 结构很相似
- 5) 以及与 静态链接库 和 动态链接库 的结构都是很相似 (同一种文件格式) 



## 2. linux 目标文件格式 (elf/elf64)

![](01.png)

![](02.png)



## 3. 目标文件 与 可执行文件 的历史

![](03.png)

![](04.png)



## 4. 目标文件 中的 section 和 segment

![](05.png)



## 5. 一个 main.c 编译后的 目标文件 的结构

![](06.png)



## 6. elf File Header

![](07.png)



## 7. bss section 只占用 目标文件，但在链接时

### 1. bss section 的历史

![](08.png)

### 2. bss section 存储 ==未初始化== 全局变量、局部 static 变量

![](09.png)



## 8. code (代码) 与 data (数据) 分开的好处

![](10.png)

![](11.png)



## 9. 实验一个简单的 main.o

### 1. main.c

```c
// 未初始化全局变量
int var1;

// 未初始化的static全局变量
static int var2;

// 已经初始化全局变量
int var3 = 1;

// 已经初始化的static全局变量
static int var4 = 2;

int main(int argv, char* argr[])
{
	// 未初始化的static局部变量
	static int var5;

	// 已经初始化的static局部变量
	static int var6 = 1;

	// 未初始化的局部变量
	int var7;

	return 1;
}
```

### 2. 32 位 linux ubuntu + gcc 编译 得到 main.o

```
xzh@xzh-VirtualBox:~$ gcc -c main.c
```

### 3. `objdump -h` main.o

```
xzh@xzh-VirtualBox:~$ objdump -h main.o

main.o:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000012  0000000000000000  0000000000000000  00000040  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
  1 .data         0000000c  0000000000000000  0000000000000000  00000054  2**2
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000008  0000000000000000  0000000000000000  00000060  2**2
                  ALLOC
  3 .comment      0000002b  0000000000000000  0000000000000000  00000060  2**0
                  CONTENTS, READONLY
  4 .note.GNU-stack 00000000  0000000000000000  0000000000000000  0000008b  2**0
                  CONTENTS, READONLY
  5 .eh_frame     00000038  0000000000000000  0000000000000000  00000090  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, DATA
```

### 4. `readelf -S` main.o

```
xzh@xzh-VirtualBox:~$ readelf -S main.o
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

比 `objdump -h` 得到更多的 section 

### 5. 多出来的其他 section

- 1) `.rodate` **只读** 数据段
- 2) `.comment` **注释** 信息段
- 3) `.note.GNU-stack` 堆区提示段

### 6. 如上 section 在 elf 文件中的布局

![](12.png)

![](13.png)



## 10. size 查看 elf 中的 text、data、bss section 长度

```
xzh@xzh-VirtualBox:~$ size main.o
   text	   data	    bss	    dec	    hex	filename
     74	     12	      8	     94	     5e	main.o
```



## 11. objdump `-s -d` 查看 main.o 代码段 ==反汇编===

![](14.png)

```
xzh@xzh-VirtualBox:~$ objdump -s -d main.o

main.o:     file format elf64-x86-64

Contents of section .text:
 0000 554889e5 897dfc48 8975f0b8 01000000  UH...}.H.u......
 0010 5dc3                                 ].
Contents of section .data:
 0000 01000000 02000000 01000000           ............
Contents of section .comment:
 0000 00474343 3a202855 62756e74 7520372e  .GCC: (Ubuntu 7.
 0010 332e302d 32377562 756e7475 317e3138  3.0-27ubuntu1~18
 0020 2e303429 20372e33 2e3000             .04) 7.3.0.
Contents of section .eh_frame:
 0000 14000000 00000000 017a5200 01781001  .........zR..x..
 0010 1b0c0708 90010000 1c000000 1c000000  ................
 0020 00000000 12000000 00410e10 8602430d  .........A....C.
 0030 064d0c07 08000000                    .M......

Disassembly of section .text:

0000000000000000 <main>:
   0:	55                   	push   %rbp
   1:	48 89 e5             	mov    %rsp,%rbp
   4:	89 7d fc             	mov    %edi,-0x4(%rbp)
   7:	48 89 75 f0          	mov    %rsi,-0x10(%rbp)
   b:	b8 01 00 00 00       	mov    $0x1,%eax
  10:	5d                   	pop    %rbp
  11:	c3                   	retq
```



## 12. `objdump -x -s -d` 查看 rodata 只读数据段

```c
char* name = "xiong";

int main(int argv, char* argr[])
{
}
```

```
xzh@xzh-VirtualBox:~$ gcc -c main.c
xzh@xzh-VirtualBox:~$ objdump -x -s -d main.o

main.o:     file format elf64-x86-64
main.o
architecture: i386:x86-64, flags 0x00000011:
HAS_RELOC, HAS_SYMS
start address 0x0000000000000000

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000012  0000000000000000  0000000000000000  00000040  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
  1 .data         00000000  0000000000000000  0000000000000000  00000052  2**0
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000000  0000000000000000  0000000000000000  00000052  2**0
                  ALLOC
  3 .rodata       00000006  0000000000000000  0000000000000000  00000052  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .data.rel.local 00000008  0000000000000000  0000000000000000  00000058  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, DATA
  5 .comment      0000002b  0000000000000000  0000000000000000  00000060  2**0
                  CONTENTS, READONLY
  6 .note.GNU-stack 00000000  0000000000000000  0000000000000000  0000008b  2**0
                  CONTENTS, READONLY
  7 .eh_frame     00000038  0000000000000000  0000000000000000  00000090  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, DATA
SYMBOL TABLE:
0000000000000000 l    df *ABS*	0000000000000000 main.c
0000000000000000 l    d  .text	0000000000000000 .text
0000000000000000 l    d  .data	0000000000000000 .data
0000000000000000 l    d  .bss	0000000000000000 .bss
0000000000000000 l    d  .rodata	0000000000000000 .rodata
0000000000000000 l    d  .data.rel.local	0000000000000000 .data.rel.local
0000000000000000 l    d  .note.GNU-stack	0000000000000000 .note.GNU-stack
0000000000000000 l    d  .eh_frame	0000000000000000 .eh_frame
0000000000000000 l    d  .comment	0000000000000000 .comment
0000000000000000 g     O .data.rel.local	0000000000000008 name
0000000000000000 g     F .text	0000000000000012 main


Contents of section .text:
 0000 554889e5 897dfc48 8975f0b8 00000000  UH...}.H.u......
 0010 5dc3                                 ].
Contents of section .rodata:
 0000 78696f6e 6700                        xiong.
Contents of section .data.rel.local:
 0000 00000000 00000000                    ........
Contents of section .comment:
 0000 00474343 3a202855 62756e74 7520372e  .GCC: (Ubuntu 7.
 0010 332e302d 32377562 756e7475 317e3138  3.0-27ubuntu1~18
 0020 2e303429 20372e33 2e3000             .04) 7.3.0.
Contents of section .eh_frame:
 0000 14000000 00000000 017a5200 01781001  .........zR..x..
 0010 1b0c0708 90010000 1c000000 1c000000  ................
 0020 00000000 12000000 00410e10 8602430d  .........A....C.
 0030 064d0c07 08000000                    .M......

Disassembly of section .text:

0000000000000000 <main>:
   0:	55                   	push   %rbp
   1:	48 89 e5             	mov    %rsp,%rbp
   4:	89 7d fc             	mov    %edi,-0x4(%rbp)
   7:	48 89 75 f0          	mov    %rsi,-0x10(%rbp)
   b:	b8 00 00 00 00       	mov    $0x0,%eax
  10:	5d                   	pop    %rbp
  11:	c3                   	retq
```

- 在 `.rodata` section 中，可以看到一个字符串 **xiong** 
- 字符串 **xiong** 作为 **字符串常量** 只读



## 13. `objdump -x -s -d` 查看 data 数据段

### 1. main.c

```c
char* name = "xiong";
int age = 99;

int main(int argv, char* argr[])
{
}
```

### 2. 编译生成 main.o

```
gcc -c main.c
```

### 3. `objdump -x -s -d` 查看 data 数据段

```
xzh@xzh-VirtualBox:~$ objdump -x -s -d main.o

main.o:     file format elf64-x86-64
main.o
architecture: i386:x86-64, flags 0x00000011:
HAS_RELOC, HAS_SYMS
start address 0x0000000000000000

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000012  0000000000000000  0000000000000000  00000040  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
  1 .data         00000004  0000000000000000  0000000000000000  00000054  2**2
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000000  0000000000000000  0000000000000000  00000058  2**0
                  ALLOC
  3 .rodata       00000006  0000000000000000  0000000000000000  00000058  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .data.rel.local 00000008  0000000000000000  0000000000000000  00000060  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, DATA
  5 .comment      0000002b  0000000000000000  0000000000000000  00000068  2**0
                  CONTENTS, READONLY
  6 .note.GNU-stack 00000000  0000000000000000  0000000000000000  00000093  2**0
                  CONTENTS, READONLY
  7 .eh_frame     00000038  0000000000000000  0000000000000000  00000098  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, DATA
SYMBOL TABLE:
0000000000000000 l    df *ABS*	0000000000000000 main.c
0000000000000000 l    d  .text	0000000000000000 .text
0000000000000000 l    d  .data	0000000000000000 .data
0000000000000000 l    d  .bss	0000000000000000 .bss
0000000000000000 l    d  .rodata	0000000000000000 .rodata
0000000000000000 l    d  .data.rel.local	0000000000000000 .data.rel.local
0000000000000000 l    d  .note.GNU-stack	0000000000000000 .note.GNU-stack
0000000000000000 l    d  .eh_frame	0000000000000000 .eh_frame
0000000000000000 l    d  .comment	0000000000000000 .comment
0000000000000000 g     O .data.rel.local	0000000000000008 name
0000000000000000 g     O .data	0000000000000004 age
0000000000000000 g     F .text	0000000000000012 main


Contents of section .text:
 0000 554889e5 897dfc48 8975f0b8 00000000  UH...}.H.u......
 0010 5dc3                                 ].
Contents of section .data:
 0000 63000000                             c...
Contents of section .rodata:
 0000 78696f6e 6700                        xiong.
Contents of section .data.rel.local:
 0000 00000000 00000000                    ........
Contents of section .comment:
 0000 00474343 3a202855 62756e74 7520372e  .GCC: (Ubuntu 7.
 0010 332e302d 32377562 756e7475 317e3138  3.0-27ubuntu1~18
 0020 2e303429 20372e33 2e3000             .04) 7.3.0.
Contents of section .eh_frame:
 0000 14000000 00000000 017a5200 01781001  .........zR..x..
 0010 1b0c0708 90010000 1c000000 1c000000  ................
 0020 00000000 12000000 00410e10 8602430d  .........A....C.
 0030 064d0c07 08000000                    .M......

Disassembly of section .text:

0000000000000000 <main>:
   0:	55                   	push   %rbp
   1:	48 89 e5             	mov    %rsp,%rbp
   4:	89 7d fc             	mov    %edi,-0x4(%rbp)
   7:	48 89 75 f0          	mov    %rsi,-0x10(%rbp)
   b:	b8 00 00 00 00       	mov    $0x0,%eax
  10:	5d                   	pop    %rbp
  11:	c3                   	retq
```

- 可以看到 `.data` 段的长度为 `00000004` 字节 (4 字节).
- 刚好就是 `int age;` 占的 4 字节
- 但好像 `char* name = "xiong";` 并未占用字节

### 4. data 段中 4 字节的数据

```
Contents of section .data:
 0000 63000000
```

- 1) 63000000 ==> 0x63 , 0x00 , 0x00 , 0x00 
- 2) int age = **99**;
- 3) 99 = `16 * 6` + 3 = **0x63**
- 4) 99 按照尝试划分为 4 字节: `[00][00][00][99]` (每一个 `[]` 表示 **一个字节** 的内存块)
- 5) 因为 **大端** 存储 => **数据的 低字节** 存放在 **内存块的 高字节**
- 6) 所以在 **大端** 存储下，99 在内存中的 4 字节: `[99][00][00][00]` => `[0x16][0x00][0x00][0x00]`

### 5. readelf -S 查看 main.o 所有的 section 编号

```
xzh@xzh-VirtualBox:~$ readelf -S main.o
There are 14 section headers, starting at offset 0x2c8:

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .text             PROGBITS         0000000000000000  00000040
       0000000000000012  0000000000000000  AX       0     0     1
  [ 2] .data             PROGBITS         0000000000000000  00000054
       0000000000000004  0000000000000000  WA       0     0     4
  [ 3] .bss              NOBITS           0000000000000000  00000058
       0000000000000000  0000000000000000  WA       0     0     1
  [ 4] .rodata           PROGBITS         0000000000000000  00000058
       0000000000000006  0000000000000000   A       0     0     1
  [ 5] .data.rel.local   PROGBITS         0000000000000000  00000060
       0000000000000008  0000000000000000  WA       0     0     8
  [ 6] .rela.data.rel.lo RELA             0000000000000000  00000220
       0000000000000018  0000000000000018   I      11     5     8
  [ 7] .comment          PROGBITS         0000000000000000  00000068
       000000000000002b  0000000000000001  MS       0     0     1
  [ 8] .note.GNU-stack   PROGBITS         0000000000000000  00000093
       0000000000000000  0000000000000000           0     0     1
  [ 9] .eh_frame         PROGBITS         0000000000000000  00000098
       0000000000000038  0000000000000000   A       0     0     8
  [10] .rela.eh_frame    RELA             0000000000000000  00000238
       0000000000000018  0000000000000018   I      11     9     8
  [11] .symtab           SYMTAB           0000000000000000  000000d0
       0000000000000138  0000000000000018          12    10     8
  [12] .strtab           STRTAB           0000000000000000  00000208
       0000000000000016  0000000000000000           0     0     1
  [13] .shstrtab         STRTAB           0000000000000000  00000250
       0000000000000071  0000000000000000           0     0     1
```

### 6. readelf -s 查看 main.o 符号表

```
xzh@xzh-VirtualBox:~$ readelf -s main.o

Symbol table '.symtab' contains 13 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS main.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    2
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    3
     5: 0000000000000000     0 SECTION LOCAL  DEFAULT    4
     6: 0000000000000000     0 SECTION LOCAL  DEFAULT    5
     7: 0000000000000000     0 SECTION LOCAL  DEFAULT    8
     8: 0000000000000000     0 SECTION LOCAL  DEFAULT    9
     9: 0000000000000000     0 SECTION LOCAL  DEFAULT    7
    10: 0000000000000000     8 OBJECT  GLOBAL DEFAULT    5 name
    11: 0000000000000000     4 OBJECT  GLOBAL DEFAULT    2 age
    12: 0000000000000000    18 FUNC    GLOBAL DEFAULT    1 main
```

- 1) name 符号 : 出现在 5 号 section => `.data.rel.local`
- 2) age 符号 : 出现在 1 号 section => `.data`



## 14. bss 段

### 1. main.c

```c
int aa;
int bb = 0;
int cc = 1;

int main(int argv, char* argr[])
{
}
```

### 2. 编译生成 main.o

```
gcc -c main.c
```

### 3. readelf -S 查看 main.o 所有的 section 编号

```
xzh@xzh-VirtualBox:~$ readelf -S main.o
There are 11 section headers, starting at offset 0x268:

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .text             PROGBITS         0000000000000000  00000040
       0000000000000012  0000000000000000  AX       0     0     1
  [ 2] .data             PROGBITS         0000000000000000  00000054
       0000000000000004  0000000000000000  WA       0     0     4
  [ 3] .bss              NOBITS           0000000000000000  00000058
       0000000000000004  0000000000000000  WA       0     0     4
  [ 4] .comment          PROGBITS         0000000000000000  00000058
       000000000000002b  0000000000000001  MS       0     0     1
  [ 5] .note.GNU-stack   PROGBITS         0000000000000000  00000083
       0000000000000000  0000000000000000           0     0     1
  [ 6] .eh_frame         PROGBITS         0000000000000000  00000088
       0000000000000038  0000000000000000   A       0     0     8
  [ 7] .rela.eh_frame    RELA             0000000000000000  000001f8
       0000000000000018  0000000000000018   I       8     6     8
  [ 8] .symtab           SYMTAB           0000000000000000  000000c0
       0000000000000120  0000000000000018           9     8     8
  [ 9] .strtab           STRTAB           0000000000000000  000001e0
       0000000000000016  0000000000000000           0     0     1
  [10] .shstrtab         STRTAB           0000000000000000  00000210
       0000000000000054  0000000000000000           0     0     1
```

### 4. readelf -s 查看 main.o 符号表

```
xzh@xzh-VirtualBox:~$ readelf -s main.o

Symbol table '.symtab' contains 12 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS main.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    2
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    3
     5: 0000000000000000     0 SECTION LOCAL  DEFAULT    5
     6: 0000000000000000     0 SECTION LOCAL  DEFAULT    6
     7: 0000000000000000     0 SECTION LOCAL  DEFAULT    4
     8: 0000000000000004     4 OBJECT  GLOBAL DEFAULT  COM aa
     9: 0000000000000000     4 OBJECT  GLOBAL DEFAULT    3 bb
    10: 0000000000000000     4 OBJECT  GLOBAL DEFAULT    2 cc
    11: 0000000000000000    18 FUNC    GLOBAL DEFAULT    1 main
```

### 5. 变量 (符号) 出现的 section

| 变量 (符号) | 出现的 section                                  |
| ----------- | ----------------------------------------------- |
| int aa;     | **COM** 段 (编译时存在，在链接后不会存在这个段) |
| int bb = 0; | bss 段                                          |
| int cc = 1; | data 段                                         |



## 15. 其他的 section 段

![](15.png)



## 16. 将一个 ==二进制== 文件 (图片、MP3 ..) 作为 目标文件 中的一个 section

![](16.png)



## 17. 自定义 section 段

![](17.png)



## 18. elf Header

### 1. `readelf -h` 

```
xzh@xzh-VirtualBox:~$ readelf -h main.o
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              REL (Relocatable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x0
  Start of program headers:          0 (bytes into file)
  Start of section headers:          616 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           0 (bytes)
  Number of program headers:         0
  Size of section headers:           64 (bytes)
  Number of section headers:         11
  Section header string table index: 10
```

![](18.png)

### 2. `/usr/include/elf.h`

#### 1. elf 32 header

```c
typedef struct
{
  unsigned char e_ident[EI_NIDENT]; /* Magic number and other info */
  Elf32_Half  e_type;     /* Object file type */
  Elf32_Half  e_machine;    /* Architecture */
  Elf32_Word  e_version;    /* Object file version */
  Elf32_Addr  e_entry;    /* Entry point virtual address */
  Elf32_Off e_phoff;    /* Program header table file offset */
  Elf32_Off e_shoff;    /* Section header table file offset */
  Elf32_Word  e_flags;    /* Processor-specific flags */
  Elf32_Half  e_ehsize;   /* ELF header size in bytes */
  Elf32_Half  e_phentsize;    /* Program header table entry size */
  Elf32_Half  e_phnum;    /* Program header table entry count */
  Elf32_Half  e_shentsize;    /* Section header table entry size */
  Elf32_Half  e_shnum;    /* Section header table entry count */
  Elf32_Half  e_shstrndx;   /* Section header string table index */
} Elf32_Ehdr;
```

#### 2. elf 64 header

```c
typedef struct
{
  unsigned char e_ident[EI_NIDENT]; /* Magic number and other info */
  Elf64_Half  e_type;     /* Object file type */
  Elf64_Half  e_machine;    /* Architecture */
  Elf64_Word  e_version;    /* Object file version */
  Elf64_Addr  e_entry;    /* Entry point virtual address */
  Elf64_Off e_phoff;    /* Program header table file offset */
  Elf64_Off e_shoff;    /* Section header table file offset */
  Elf64_Word  e_flags;    /* Processor-specific flags */
  Elf64_Half  e_ehsize;   /* ELF header size in bytes */
  Elf64_Half  e_phentsize;    /* Program header table entry size */
  Elf64_Half  e_phnum;    /* Program header table entry count */
  Elf64_Half  e_shentsize;    /* Section header table entry size */
  Elf64_Half  e_shnum;    /* Section header table entry count */
  Elf64_Half  e_shstrndx;   /* Section header string table index */
} Elf64_Ehdr;
```

### 3. elf header struct 成员含义

![](19.png)

![](20.png)

### 4. `e_ident[EI_NIDENT];` elf 魔数

![](21.png)

### 5. elf 魔数中，最开始的 4 个字节

![](22.png)

### 6. `e_type` elf 文件类型

![](23.png)

### 7. `e_machine` 可执行文件 支持的 cpu 架构

![](24.png)



## 19. Section header table (段表)











