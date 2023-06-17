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

 直接安装，但是我试了下测试文件发现报错没有相关头文件（比如说stdio.h not found ），然后去编译安装

```
git clone git@github.com:riscv-collab/riscv-gnu-toolchain.git
//
cd riscv-gnc-toolchain
//依赖
sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build

//prefix安装目录 --enable-multilib 支持32位
./configure --prefix=/opt/riscv --enable-multilib

//
sudo make  -j8
//编译完之后输入
riscv64-unknown-elf-gcc
//如果不是未找到就可以否则需要添加相关路径到PATH
```

前面编译安装的时候使用了--enable-multilibs，在riscv64-unknown-elf-gcc 后面加上--print-multi-lib 可以查看 --march 和 -mabi可以用的参数（就是之前编好了的库)。



### QEMU

​    去github下载源码安装。中间可能会报相关依赖项要安装。

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

​		默认安装

## LAB1

​      目标：内核启动与prink的实现

​	  实际上，prink之前就已经实现过了，大头是内核启动，需要了解riscv指令集（重头开始）。

### 内核启动

​		需要修改的地方只有include.mk, kernel.lds 以及部分汇编(start.s 和panic.c中的内嵌汇编)。

#### Include.mk

​		交叉编译工具使用riscv64-unknown-elf- 删除掉gcc和ld未知参数，为gcc添加架构和ABI参数设置

```
-march=rv32imafc -mabi=ilp32f 
```

​		如果是rv32i 和 ilp32 的话链接的时候会报错 ,其他的没试。

```
undefined reference 'umodsi3' 和 'udivsi3'
```

​		如果是编译安装的交叉编译工具链，gcc版本为12.2（大概）的话，需要删除链接参数中的 **-fatal-warnings**，或者改成**-no-fatal-warnings**，因为报警告说 **load segment has a RWX permessions**，好像是新版本gcc才会这么报错，然后之前这个参数是让警告编程错误，然后就编译不成功。

​      通过man手册查参数没有详细介绍（等于没有):anger:

```
\\gcc参数
-std=gnu99		
\\

-g
\\

-fno-pic
\\

--ffreestanding
\\

-nostartfiles
\\

-fno-stack-protector
\\

-fno-builtin
\\

-Wall
\\

-march=  -mabi= 


-verbose
\\

\\ld 参数

-nostdlib
\\

-m
\\
```





#### kernel.lds

​		按照教程修改。

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

  

#### 汇编修改

​	下面是中文版指令集手册

```
http://riscvbook.com/chinese/RISC-V-Reader-Chinese-v2p1.pdf
```

- start.S

 注意链接文件中多了.boot,作为引导，要把.text 改成.boot不然会直接连接到elf_from 出错.

```
//...
.section .boot
EXPORT(_start)
.option pic
	//mtc0 zero,CP0_STATUS
	
	li sp,0x80600000
	jal mips_init
```

- panic.c

​	这里用了内嵌汇编,move用mv代替，mfc0用csrr代替其中要打印的信息改为sp,ra,mtval,mstatus,mcause,mepc;

​	关于内嵌汇编

```
//sad
```

####  完成

​	这样就可以通过qemu启动内核了

### printk

​	使用opensbi带的ecall在S态下输出。

​    参考:[opensbi下的riscv64裸机系列编程1(串口输出) - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1770529)

​     





