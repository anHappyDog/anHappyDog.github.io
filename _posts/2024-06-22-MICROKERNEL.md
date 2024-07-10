---
title: MICROKERNEL
author: lonelywatch
date: 2024-06-22 21:28 +0800
categories: [KERNEL]
tags: [OS,KERNEL]   
---

# MICROKERNEL

MICROKERNEL在20世纪80年代就已提出，其最小化内核，易拓展，易模块化，易维护，较宏内核更为健壮的特点受到过过很多关注。在当时的实现中，也出现了较宏内核效率低的问题。MICROKERNEL由于将大部分内核内容转移至用户空间单独的服务，故而需要IPC进行通信，IPC在MICROKERNEL中的大量开销往往被认为是MICROKERNEL性能不足的主要原因。


## 中断与异常处理

通常来说，MICROKERNEL本身并不会处理绝大部分异常和中断，除去和调度相关的时间中断和NMI中断，其他中断和异常通常交由MICROKERNEL创建的用户线程处理。


## 隔离的实现

KERNEL如果需要更高的安全性，除了像MICROKERNEL那样减少攻击面以外，还需要实现组件与组件间的隔离，比如MM（内存管理），Scheduler(调度)，IPC和其他组件间的隔离。组件的隔离性又可以分为软件和硬件两种方式进行实现。



## 错误处理

## SAS与MAS

SAS（Single Address Space）是指KERNEL只使用单一地址空间，这就意味着需要将原本拥有各自地址空间的组件加载入单一地址空间的KERNEL中，并实现与MAS(Multiple Address Space)相类似的地址隔离以确保内存访问的安全性。

## 语言层面的安全检查

有学者尝试利用一些语言的安全特性与编译器检查来提高KERNEL的安全性，包括语言的类型系统，内存管理，编译器对引用的悬垂检查等。J-KERNEL由Java写成，如今最多用于该领域测试的语言则是RUST,RUST的静态类型系统以及其所有权和生命周期内存管理系统将很多Runtime error便成为Compile-time error，RUST的编译器支持多种安全检查。

尽管如此，但是RUST还是存在着一些问题，比如跨语言间的安全性很难如RUST内部一样得到保证，并且在某些情况下RUST所谓的0成本抽象也失去了效果。


## 参考文献
