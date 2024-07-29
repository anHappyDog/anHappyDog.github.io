---
title: UNIKERNEL与UNIKRAFT
author: lonelywatch
date: 2024-4-21 21:28 +0800
categories: [KERNEL]
tags: [KERNEL,UNIKERNEL] 
---

# UNIKERNEL

[UNIKERNEL](http://unikernel.org/) 单内核是一种通常被裁剪的轻量级KERNEL，只包含应用程序运行所需的最小功能。其目标是将操作系统的功能最小化，以减少资源消耗，提高性能和安全性。

除了UNIKERNEL外，还有这么一些OS架构，比如：

- Monolithic kernel：宏内核，所有功能都在一个内核空间中，较微内核而言，内核大，攻击面广，难以维护，但是性能较好

- Microkernel：微内核，内核中只包含MM,IPC等功能，其余功能通过服务的方式在用户态提供，内核小，维护简单，但是性能开销受IPC等影响较大，可能需要更多的硬件适配与支持才能发挥优势

- Hybrid kernel：混合内核，介于宏内核和微内核之间，有一部分功能在内核空间，有一部分功能在用户空间，这一动作的平衡点为将严重依赖IPC的功能放回到内核中，从而尽量提升性能

- Exokernel：外核，将硬件资源直接暴露给用户态，用户态可以直接控制硬件，内核只负责硬件资源的分配，这样可以提升性能，但是安全性较差

然后就是UNIKERNEL,通过如开头所述的裁剪，将内核与应用程序捆绑从而形成单一的可执行文件，从而运行在hypervisor上，其主要适用于云平台等使用场景，相较于上面的架构,可以很好地利用其可定制，轻量级，高效率且高安全的优势。


## 单地址空间



## 参考文献



- [UNIKENREL -WIKI](https://en.wikipedia.org/wiki/Unikernel)

- [ArceOS](https://rcore-os.cn/arceos-tutorial-book/ch02-00.html)