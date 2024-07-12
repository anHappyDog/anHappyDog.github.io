---
title: ELF与RELOCATION
author: lonelywatch
date: 2024-07-08 21:28 +0800
categories: [ELF]
tags: [ELF,RELOCATION,KERNEL]   
---

# ELF与RELOCATION

ELF(Extensible Linking Format)是一种用于描述可执行文件、目标文件、共享库等的文件格式，是Linux系统中最常见的文件格式之一。在Linux系统中，所有的可执行文件、目标文件、共享库等都是以ELF格式存储的。ELF文件格式的结构如下(图片来自elf的wiki)：

![](https://lonelywatch-1306651324.cos.ap-beijing.myqcloud.com/image-20240712164552778.png)

ELF文件总共包含四个部分： ELF Header, Program headers, Sections, Section headers, 其中ELF Header是整个文件的头部，包含了文件的基本信息，比如文件的类型，机器类型，入口地址等；Program headers用于描述程序的加载信息，比如程序的入口地址，程序的加载地址等；Sections用于描述程序的各个段的信息，比如代码段，数据段等；Section headers用于描述Sections的信息，比如Section的名字，Section的大小等。 Program headers和Section headers指向的都是sections，只是两者的作用不同，Program headers用于描述程序的加载信息，而Section headers用于描述Sections的信息。

ELF文件中段的种类主要有：



## RELOCATION



## DWARF


## 参考文献

- [ELF - MAN](https://man7.org/linux/man-pages/man5/elf.5.html)

- [ELF - WIKI](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format)






