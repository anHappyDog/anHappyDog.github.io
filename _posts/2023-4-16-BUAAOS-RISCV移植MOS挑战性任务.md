---
title: BUAAOS RISCV移植MOS挑战性任务
author: lonelywatch
date: 2023-4-16 00:14 +0800
categories: [BUAA,OS]
tags: [BUAA,OS,RISCV]
---

## 目标

将mos一直到RISCV平台上。

## LAB0 

 ### 虚拟机配置

 配置相关环境。

 这里我使用win10自带的wsl2，安装ubuntu进行操作。

 电脑设置搜索 **启用或关闭windows功能**，打开 适用于Linux平台的windows子系统 和 虚拟机平台。

用管理员打开cmd，输入

```
wsl --update
//更新wsl

wsl --version
//查看wsl版本

wsl --set-default-version 2
//改为linux分发版本 2

wsl -l --online
//查看可用版本

wsl --install -d Ubuntu
//安装Ubuntu（需要科学上网，或者去win Store下载，好像也要）

wsl -d Ubuntu
//进入系统
```

​     这个虚拟机默认装在C盘，用cmd不好操作，我们可以用xshell连接虚拟机。

```
sudo apt-get install openssh-server
//安装openssh-server

//关闭wsl sudo密码请求
sudo visudo 
//在最后加上这段
<用户名> ALL=(ALL) NOPASSWD:ALL

//为了开机自动启动ssh,开机会自动执行.bashrc中的内容
vim ~/.bashrc
在最后一行输入下面这段
sudo service ssh start

//最后修改ssh配置
vim /etc/ssh/sshd_config

//在文本最后输入
Port 2222   #ssh端口号
PermitRootLogin yes  #可以root远程等率  
PasswordAuthentication yes   # 密码验证登录

//重启服务
sudo service ssh --full-restart

//这里用的是sftp协议,用filezila传文件的时候需要注意。
//参考 :https://blog.csdn.net/u013250861/article/details/128372550
```

### Python和vim

​	我因为vim插件要用到所以之前就装了python3.10，把自带的python删了，跟之前os跳板机一样编译安装python和vim，安装插件。

    ###  交叉编译工具链

​      教程说可以直接用

```
apt install gcc-riscv64-unknown-elf
```

 直接安装，但是我试了下发现没有相关头文件（比如说stdio.h not found ，但是裸机情况下没试过就去编译安装了），这好像是后面32位没有编译相关库，后面编译安装也有这样的情况。我喜欢编译安装，去Github下载源码按照说明文件安装。

```
git clone git@github.com:riscv-collab/riscv-gnu-toolchain.git
//
cd riscv-gnc-toolchain
//依赖
sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build

//prefix安装目录 --enable-multilib 支持32位
./configure --prefix=/opt/riscv --enable-multilib

//
sudo make linux -j8
//编译完之后输入
riscv64-unknown-elf-gcc
//如果不是未找到就可以否则需要添加相关路径到PATH
```

 这样安装完之后发现如果这样会

```
riscv64-unknown-elf-gcc hello.c -march=rv32i -mabi=ilp32
//hello.c含有stdio.h但是编成32位时会报stdio notFound，裸机没测试
```

​      并且如果你这样用来编译mos的话，它链接的时候会报一些错误，就像

```
//
 ABI is incompatible with that of the selected emulation:
  target emulation `elf32-littleriscv' does not match `elf64-littleriscv'
// 我之前看64位是能匹配32位的，但是这里好像不行，自己不是很懂，自己觉得是相关库没编译啥的，后面估计能用-print-multi-lib验证但是没有去验证了
//
riscv64-unknown-elf-ld:lib/elfloader.o: file format not recognized; treating as linker script
//这里的错我没有看懂，但是后面还是解决了
```

但是如果使用riscv64-unknown-linux-gnu-gcc的话就可以

```
 riscv64-unknown-linux-gnu-gcc hello.c -march=rv32imac -mabi=ilp32
```

前面编译安装的时候使用了--enable-multilibs，在riscv-**-gcc 后面加上--print-multi-lib 可以查看 --march 和 -mabi可以用的参数（就是之前编好了的库），后面mos的编译也是用的linux-gnc-gcc。



### QEMU

​    这个任务使用gdb调试要方便很多，现在安装QEMU，去github下载源码安装。中间可能会报相关依赖项

```
//sudo apt install build-essential


ERROR: Cannot find Ninja
sudo apt-get install ninja-build
ERROR: pkg-config binary 'pkg-config' not found
//sudo apt install pkg-config
ERROR: glib-2.56 gthread-2.0 is required to compile QEMU
//sudo apt-get install libglib2.0-dev
Dependency “mount” not found, 
//sudo apt-get install libmount-dev
 ERROR: Dependency "pixman-1" not found,
//sudo apt-get install libpixman-1-dev
```

然后

```
sudo ln -s /home/<user>/qemu/build/qemu-system-riscv32  /usr/bim/qemu-system-riscv32
//设置软连接快捷方式，把路径放到PATH也可以

//全局环境变量设置,编辑/etc/profile
sudo vim /etc/profile
//文件生效?
source /etc/profile
```

### OPENSBI

 ```
 $ export CROSS_COMPILE=riscv64-unknown-elf-
 $ export PLATFORM_RISCV_XLEN=32
 $ make PLATFORM=generic
 ```

​    这里也可以设置快捷方式

​     这里按照教程编译安装，目前还没有用到,使用方式

```
-bios fw_dynamic.bin
```

## LAB1

​      目标：内核启动与prink的实现

​	  实际上，prink之前就已经实现过了，大头是内核启动，需要了解riscv指令集（重头开始）。

### 内核启动

​		首先修改include.mk，把编译器相关改成riscv64-unknown-linux-gnu-gcc,然后把未识别参数删了，然后添加（最朴素做法），参数可以通过man指令查，但是没有具体说明，，，，，，，，，，，，其实我想把Makefile自己全重写一遍但是时间不够只能延后了。

```
-march=rv32imac -mabi=ilp32 
```

​        然后按照教程修改链接文件

```
OUTPUT_ARCH(riscv)

ENTRY(_start)

SECTIONS {
    kernel_start = .;
    . = 0x80200000;
    .text : {
        *(.boot)
        *(.text)
    }

    .data : {
        *(.data .data* .sdata .sdata*)
    }

    .bss  : {
        *(.bss .bss* .sbss .sbss*)
    }
    bss_end = .;
    . = 0x80600000;
    end = . ;
}

```

​     这里和mips有点不同，然后就需要修改所有汇编写的，将mips汇编改成riscv汇编，指令集需要重头学，下面是中文版指令集手册

```
http://riscvbook.com/chinese/RISC-V-Reader-Chinese-v2p1.pdf
```

​	今天一天就到这了，还有巨tm多事情要做，写写来放松

---

---

2023.4.16.0.59

---

---



