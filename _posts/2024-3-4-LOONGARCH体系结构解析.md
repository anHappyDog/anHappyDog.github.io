---
title: LOONGARCH体系结构解析
author: lonelywatch
date: 2024-3-4 00:14 +0800
categories: [LOONGARCH]
tags: [LOONGARCH,体系结构]
---

# LOONGARCH体系结构解析

LOONGARCH架构是一种现代化的RISC体系架构，其基于MIPS架构并融入了现代化体系结构的诸多特点。对于一种架构的使用，往往基于指令集，特权模式，寄存器，异常中断与IO与硬件实现等方面进行学习。

## 寄存器

### 通用寄存器

| 寄存器名  | 别名    | 用途               | 跨调用保持 |
|-----------|---------|--------------------|------------|
| $r0       | $zero   | 常量0              | 不使用     |
| $r1       | $ra     | 返回地址           | 否         |
| $r2       | $tp     | TLS/线程信息指针   | 不使用     |
| $r3       | $sp     | 栈指针             | 是         |
| $r4-$r11  | $a0-$a7 | 参数寄存器         | 否         |
| $r4-$r5   | $v0-$v1 | 返回值             | 否         |
| $r12-$r20 | $t0-$t8 | 临时寄存器         | 否         |
| $r21      | $u0     | 每CPU变量基地址    | 不使用     |
| $r22      | $fp     | 帧指针             | 是         |
| $r23-$r31 | $s0-$s8 | 静态寄存器         | 是         |

### 向量寄存器

Loongarch主要由128位的LSX扩展与256位的LSAX向量扩展。分别使用`$v0`~`$v31`与`$x0`~`$x31`表示。

### 系统寄存器

## 指令集

## 异常中断

## IO

## 硬件实现

## 参考文献

1. [Loongarch Linux](https://www.kernel.org/doc/html/latest/translations/zh_CN/arch/loongarch/introduction.html)
