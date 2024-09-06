---
title: ELF与RELOCATION
author: lonelywatch
date: 2024-07-08 21:28 +0800
categories: [ELF]
tags: [ELF,RELOCATION,KERNEL]   
---

# ELF与RELOCATION

内核模块的加载同样涉及到了这些内容。

ELF(Extensible Linking Format)是一种用于描述可执行文件、目标文件、共享库等的文件格式，是Linux系统中最常见的文件格式之一。在Linux系统中，所有的可执行文件、目标文件、共享库等都是以ELF格式存储的。ELF文件格式的结构如下(图片来自elf的wiki)：

![](https://lonelywatch-1306651324.cos.ap-beijing.myqcloud.com/image-20240712164552778.png)

ELF文件总共包含四个部分： ELF Header, Program headers, Sections, Section headers, 其中ELF Header是整个文件的头部，包含了文件的基本信息，比如文件的类型，机器类型，入口地址等；Program headers用于描述程序的加载信息，比如程序的入口地址，程序的加载地址等；Sections用于描述程序的各个段的信息，比如代码段，数据段等；Section headers用于描述Sections的信息，比如Section的名字，Section的大小等。 Program headers和Section headers指向的都是sections，只是两者的作用不同，Program headers用于描述程序的加载信息，而Section headers用于描述Sections的信息。

ELF文件中段的种类主要有：

 - `.text`: 主要的代码段，由编译器生成，包含了程序的代码

 - `.data`: 主要的数据段，包含程序初始化了的全局数据

 - `.bss`: 未初始化的数据段，包含了程序未初始化的全局数据

 - `.rodata` : 只读数据段，包含了程序的只读数据

 - `.rela`: 重定位段，相比.rel段增加了r_addend字段，加快重定位速度

 - `.rel`: 重定位段，包含了程序的重定位信息

 - `.symtab` : 符号表，包含了程序的符号信息

 - `.strtab` : 字符串表，包含了程序的字符串信息，可以通过字符串索引查找字符串以减少文件大小

 - `.got` : 全局偏移表，包含了程序的全局变量地址信息（仅限动态链接）

 - `.plt` : 过程链接表，包含了程序的过程链接信息 （仅限动态链接）

 - `.shstrtab` : 节名表，包含了程序的节名信息
 
 - `.dyn` : 动态段，包含了程序的动态链接信息 （仅限动态链接）

 - `.dynstr`: 动态字符串表，包含了程序的动态字符串信息 （仅限动态链接）

- `.eh_frame`与`.debug_frame` : DWARF段，分别用于栈回溯与调试信息

除此以外一些编译器还会产生一些额外的段，包括`.comment`段，`.note`段等，用于提供编译器本身信息。

## 符号表


## 静态ELF文件加载

## RELOCATION

对于可重定位文件，由于其代码段和数据段的地址是未知的，所以在链接的时候需要对这些地址进行修正，这个过程就是重定位。重定位的过程主要包括两个步骤：1. 生成重定位表；2. 修正地址。

在编译可重定位库文件时，对于一些当前编译阶段无法确定地址的内容（全局变量，外来变量，其他函数等），会在当前位置填入0x0地址，然后在符号表中记录下这些位置，这些位置就是重定位表。在链接的时候，链接器会根据重定位表中的信息，修正这些位置的地址。

内核模块加载除了单纯的ELF解析外，就是需要实现动态链接的操作，利用目前内核已知的符号表去填充重定位表中的位置中涉及到的地址。

重定位有很多种，并且不同架构的种类基本都有所不同。

## DWARF与栈回溯

DWARF (Debugging With Attributed Record Formats)是一种用于描述调试信息的格式，是ELF文件中的一部分。DWARF文件主要包含了程序的调试信息，比如程序的变量名，程序的函数名，程序的行号等。

## 动态链接

和静态链接相比，动态链接的优点在于能够有效地减少程序的大小与运行时占用的内存，提高库代码的复用性，随之而来的代价则是对程序的启动速度以及对外部符号的调用速度有一定的影响。在动态链接中，代码中使用的外部符号会在运行时由动态链接器填充，而非在编译时确定。这一部分涉及到PIC代码，GOT表与PLT表对外来符号的查询与调用。

> 因为运行时并不能修改代码段，所以需要实现位于数据段的GOT表与PLT表这一机制来实现外部符号地址的填充。

### PIC代码

PIC(Position Independent Code)是一种与位置无关的代码，PIC代码可以在任何位置运行，而不需要进行修正。在动态链接中，由于代码的位置是未知的，所以需要使用PIC代码。PIC代码的特点是不使用绝对地址，而是使用相对地址，基于通用的重定位之上，使用GP指针来对外部符号进行定位,这样就可以在任何位置运行。PIC代码通常需要遵守某一ABI规范。

### GOT表与PLT表

GOT表（GLOBAL OFFSET TABLE）是一个全局偏移表，用于存放程序中使用的外部符号的地址。在程序运行时，动态链接器会将GOT表中的地址填充为外部符号的地址。PLT表（Procedure Linkage Table）是一个过程链接表，用于存放程序中使用的外部符号的调用地址。在程序运行时，动态链接器会将PLT表中的地址填充为外部符号的调用地址。

### 延迟加载

（Lazy Binding）是指在程序运行并调用该外部符号时，动态链接器才会将该外部符号的地址填充到GOT表中。这样可以很好地减少程序启动时的开销。在启动时，动态链接器会将并不需要在初始阶段就填入地址的外部符号对应的GOT表项填上特殊的跳转指令，跳转到PLT表中动态链接器预留的用于寻找外部符号地址的函数(__dl_runtime_resolve)调用代码处，


## 内核模块

## 参考文献

- [ELF - MAN](https://man7.org/linux/man-pages/man5/elf.5.html)

- [ELF - WIKI](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format)






