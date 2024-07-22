---
title: THESEUS
author: lonelywatch
date: 2024-6-4 00:14 +0800
categories: [SAFE_OS]
tags: [SAFE_OS,RUST]
---

# THESEUS

[Theseus](https://github.com/theseus-os/Theseus) 是一个基于RUST的操作系统，旨在实现一个安全的，高性能的操作系统，在USENIX(信息安全领域四大顶级学术会议之一)上发表了多篇论文。

下面是对其理论的一些总结和思考。


## RUST safe

## ELF动态加载

Theseus支持crate动态加载，其本身由多个小的crate组成，并且要求每个crate不包含子模块，从而确保了模块之间的边界和依赖明确。Theseus将动态加载的内容称为`Cell`，在不同时期有不同指向：在编译时期，`Cell`是一个crate，而在链接时期，`Cell`是一个目标o文件，最终在运行时期，`Cell`是一个？。

为了确保安全性，Theseus需要明确各个crate之间的依赖性，而这主要通过分析载入的crate库文件中的metadata实现（RUST对于rlib中的metadata的序列化与反序列化在不同版本中可能有所不同），并且实现了`Namespace`，用于划分crate之间的界限。

对于一个`Cell`(或者说是目标文件),包含若干Section，其中有若干函数，函数的信息被记录在了符号表中，函数的实现则保存在Section中，所以Theseus实现了对ELF文件的解析和重定位。crate的依赖可以通过解析metadata来得到，可是Section之间的依赖却很难得到。Theseus在得到依赖关系和数据后，根据ELF的重定位规则进行重定位，并且利用RUST的强弱引用和生命周期来确保依赖的正确性。


[](https://lonelywatch-1306651324.cos.ap-beijing.myqcloud.com/%25E6%259C%25AA%25E5%2591%25BD%25E5%2590%258D%25E6%2596%2587%25E4%25BB%25B6%2520(4).png)

下面是对过程的具体说明：

主要是对于ELF文件的解析和重定位，以及对于RUST的一些特性的利用。RUST源码中的[Libraries and Metadata](https://rustc-dev-guide.rust-lang.org/backend/libs-and-metadata.html)
 `crate_metadata`等crate负责编译时crate元数据的生成。


解析ELF文件并不困难，具体是对ELF文件的重定位，需要利用其中的REL,RELA，DYNAMIC等段结合符号表进行重定位。并且由于THESEUS SAS的特性，需要制定一定的规则用于地址范围的分配和管理，在确定好各个段的分配地址后，需要记录相关信息。

在上面的基础上开始加载crate，加载前需要利用得到的metadata加载所有的依赖项，会利用其中给定的路径尝试加载，并且利用RUST的强弱引用和生命周期来确保依赖的正确性。

在Theseus中还需要记录每个crate中对外暴露的函数接口，提供相应的调用接口，这样也可以便于


### 名称修饰

RUST生成的ELF文件中对于非no_mangle函数的修饰类似于：`_ZN5alloc5alloc5alloc17h66d4e14ff3fac21aE`,以`_ZN`开头，以`E`结尾，其开头每个域为`名称长` + `名称`的形式组成，最后添加上16位的HASH码。




## SAS

## Cell动态加载

## panic 资源回收




## 参考文献

1. Kevin Boos, Namitha Liyanage, Ramla Ijaz, and Lin Zhong, "Theseus: an Experiment in Operating System Structure and State Management", 14th USENIX Symposium on Operating Systems Design and Implementation (OSDI 20), 2020.
