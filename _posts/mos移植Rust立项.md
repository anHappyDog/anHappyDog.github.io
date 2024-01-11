## mos移植Rust立项

支持功能：

- 内存空间管理
- 进程
- 多线程与同步
- 用户程序
- 文件系统
- 网络
- shell

基于原有的mos，提出了网络，多线程和同步两个新的功能。

---

## 工具链

rust，riscv64-gc-unknown-none-elf (通过cargo安装下载),rust-binutils(通过cargo下载安装)。qemu-system-riscv64，riscv64-unknown-none-gdb,rust_sbi（实现了riscv64调用规范的bootloader）

---

## 交叉编译

在项目根目录下新建`.cargo`文件夹，在该文件夹中新建`config`文件，写入如下内容：
```toml
[build]
target = "riscv64gc-unknown-none-elf"
[target.riscv64gc-unknown-none-elf]
rustflags=[
	"-Clink-args=-Tsrc/linker.lds","-Cforce-frame-pointers=yes"
]
```

cargo指令会查找当前以及所有父目录下的.cargo文件夹，其中的config文件就是该项目的配置文件，这样就不需要每次编译时指定`--target riscv64-unknown-none-elf`，同时还增加了编译用的参数。

## 裸机程序

0x80000_0000 开始都是rust_sbi的空间，这里设置内核的开始地址为0x8020_0000。



这里暂时通过调用sbi存在的接口来进行字符输入输出以及关机重启等。

## sbi接口与字符串输入输出

调用rust_sbi中的接口来进行关机重启，字符的读取和写入。

实现了print!和println!宏来进行封装。（）

这里使用了core::fmt::Write来自定义输出方式（通过实现其中的 write_str），并且通过默认的`write_fmt`来传递参数。

内核中使用的局部变量都需要使用栈空间，所以需要在链接脚本中让编译器划分出一段空间。这里指定为16KB。











