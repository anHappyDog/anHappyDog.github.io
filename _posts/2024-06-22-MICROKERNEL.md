---
title: MICROKERNEL
author: lonelywatch
date: 2024-06-22 21:28 +0800
categories: [KERNEL]
tags: [OS,KERNEL]   
---

# MICROKERNEL

MICROKERNEL在20世纪80年代就已提出，其最小化内核，易拓展，易模块化，易维护，较宏内核更为健壮的特点受到过过很多关注。在当时的实现中，也出现了较宏内核效率低的问题。MICROKERNEL由于将大部分内核内容转移至用户空间单独的服务，故而需要IPC进行通信，IPC在MICROKERNEL中的大量开销往往被认为是MICROKERNEL性能不足的主要原因。



## 参考文献
