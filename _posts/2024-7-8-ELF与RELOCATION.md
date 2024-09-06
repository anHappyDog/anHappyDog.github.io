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

 - `.shstrtab` : 节名表，包含了程序的节名信息
 
 - `.dyn` : 动态段，包含了程序的动态链接信息

 - `.dynstr`: 动态字符串表，包含了程序的动态字符串信息

- `.eh_frame`与`.debug_frame` : DWARF段，分别用于栈回溯与调试信息

除此以外一些编译器还会产生一些额外的段，包括`.comment`段，`.note`段等，用于提供编译器本身信息。

## 符号表


## RELOCATION

对于可重定位文件，由于其代码段和数据段的地址是未知的，所以在链接的时候需要对这些地址进行修正，这个过程就是重定位。重定位的过程主要包括两个步骤：1. 生成重定位表；2. 修正地址。

在编译可重定位库文件时，对于一些当前编译阶段无法确定地址的内容（全局变量，外来变量，其他函数等），会在当前位置填入0x0地址，然后在符号表中记录下这些位置，这些位置就是重定位表。在链接的时候，链接器会根据重定位表中的信息，修正这些位置的地址。

内核模块加载除了单纯的ELF解析外，就是需要实现动态链接的操作，利用目前内核已知的符号表去填充重定位表中的位置中涉及到的地址。

重定位有很多种，并且不同架构的种类基本都有所不同。

## DWARF与栈回溯

DWARF (Debugging With Attributed Record Formats)是一种用于描述调试信息的格式，是ELF文件中的一部分。DWARF文件主要包含了程序的调试信息，比如程序的变量名，程序的函数名，程序的行号等。

## 动态链接


## 内核模块

## 参考文献

- [ELF - MAN](https://man7.org/linux/man-pages/man5/elf.5.html)

- [ELF - WIKI](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format)






