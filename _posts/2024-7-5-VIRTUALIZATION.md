---
title: Virtualization
author: lonelywatch
date: 2024-07-05 21:28 +0800
categories: [KERNEL]
tags: [KERNEL,ARM,VIRTUALIZATION,Xen]   
---

# Virtualization

虚拟化，也就是在一台机器上同时提供多个相同环境，在这些环境中运行不同的操作系统与应用程序，以充分利用计算机硬件资源。这主要在云平台被广泛使用。从1974年第一篇相关论文被发表到今约50年的发展历史里，虚拟化探寻了多种方法，从单纯地使用软件进行模拟，到软硬件结合的方式，解决了很多诸如虚拟机或者性能不足，或者适配性不足，或者安全性不足的问题。

虚拟化最早由该论文提出，作者提出了虚拟化的组成与若干特征：

![](https://lonelywatch-1306651324.cos.ap-beijing.myqcloud.com/image-20240729120356046.png)

虚拟化往往由`Virtual Machine Manager`(VMM)提供相同的虚拟环境，VMM对硬件具有完全的控制；在提供的虚拟环境或者是虚拟机中，可以运行其他内容。

下面是若干特征：

- 提供与原物理环境尽可能相同的虚拟环境

- 尽可能减少性能的损耗

- 对资源的完全控制与隔离


## VMM

VMM可被看作是VM的控制程序，通常由若干模块组成：

- **Dispatch Module**: 分发模块，主要负责接收来自VM的请求，并将其分发给其他模块。这一模块通常被放置在异常入口的某处，使用硬件提供的异常机制来实现VMM与VM的交互。（即使如此，我还没有想出开销更低的办法能够满足使用异常的优点）

- **Allocator Module**: 分配模块，主要负责资源管理，包括内存、CPU、I/O等资源的分配与回收。当VMM需要与多个VM交互时，需要注意并发问题。这一模块通常被分发模块调用。

- **Interpretor Module** : 解释模块，主要负责对特权指令的解释和执行。对于每一条特权级指令都会有对应的解释器例程（Routine）,其目的是为了在VM中生成对应的行为。 可以将VM视为状态机，每一套特权级指令都可能会改变自身状态，解释器例程就是为了在VM中模拟这种状态变化。


对于**effciency**,所有VM的指令都应直接由CPU执行，而不是由VMM进行**直接**干涉，这很好地减少了性能损耗。allocator模块实现了**resource control**;interpretor模块则实现了**equivalence**。

只要实现了上面的特征和模块，就可以被称为VMM，并且**建立VMM的机器通常具有一定要求**，比如之前提到的改变VM状态的指令必须只能是该类机器的特权级指令，因为这一方式的VMM是通过特权级和异常来与VM交互的。

由于上面的原因，在论文被发表的时期，并没有很好地用于Virtualization的架构，作者又提出了`Hybrid Virtual Machines`(HVM)，他和之前的Virtualzation 相比，为了解决“只能通过特权级与异常来进行VMM与VM间的交互”这一问题，在解释模块中增加了更多的解释例程来达到原来的目的。

## Xen

[2][Xen](https://xenproject.org/) 是一个基于X86的开源虚拟化软件。就如之前所说，随着时代的发展，越来越多的硬件厂商为虚拟化提供了很好地支持，通过这种硬件支持，能够提供比纯软件虚拟化更优秀的性能。

这里其实涉及到`Full Virtualization`和`Para Virtualization`的概念，完全虚拟化是指在VM中运行的OS通常完全不需要任何修改便可直接运行，但是会带来很多性能上的问题，而为了解决这一问题,[4]便提出了半虚拟化。半虚拟化需要对OS进行一定的修改，但是**ABI是不需要修改**的，而这对于将半虚拟化推向实用化非常重要。

> 全虚拟化与半虚拟化的区别在于 Guest OS是否能够感知其自身是Virtualized的，如果Guest OS能够感知到自己是Virtualized的，那么这种虚拟化方式就是半虚拟化，反之则是全虚拟化。

### Memory Virtualization

内存虚拟化通常是虚拟化中最复杂的一部分，对于Xen基于的X86架构，TLB经由硬件自身查找页表来解决缺失异常，并非通过软件管理，并且也没有tag来区分不同地址空间的cache，这就会导致在切换地址空间时很大的性能开销，TLB需要在切换地址空间时刷新所有的TLB项。Xen为了解决这些问题将自身放置在所有VM的顶层64MB段中，从而减少进出VMM的开销，并且**硬件页表的管理交由客机负责**，Xen只负责安全管理和隔离。（具体实现你得看代码）比如当需要新页表时，VM会自己从其保留内存中分配出来，并且通过Xen注册。可见Xen并非内存的管理者，而是VM与内存的中介。同时VM也会打包对Xen的请求，从而进一步减少其开销。

### CPU Virtualization

如之前所说,VMM与VM的交互媒介就是硬件提供的异常机制，所以VMM的特权级必定要比VM要高。如今的很多架构都实现了多特权级，比如ARM的EL0,1,2,3,RISC-V的Hypervisor被夹在Machine和Supervisor之间，甚至MIPS与最近的Loongarch也有类似的设计。这些架构的设计都是为了更好地支持虚拟化。

再回到CPU Virtualization,X86本身就有多特权级。




### IO Virtualization












## 参考文献

1. [Formal Requirements for Virtualizable Third Generation Architectures](https://dl.acm.org/doi/abs/10.1145/361011.361073)

2. [Xen and the Art of Virtualization](https://dl.acm.org/doi/abs/10.1145/945445.945462)

3. [A comparison of software and hardware techniques for x86 virtualization](https://dl.acm.org/doi/abs/10.1145/1168918.1168860)

4. [Lightweight Virtual Machines for Distributed and Networked Applications](https://api.semanticscholar.org/CorpusID:11809347)

