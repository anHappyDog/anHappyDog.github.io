---
title: 在树莓派Raspberry4B下编译Tensorflowlite
author: lonelywatch
date: 2023-03-7 20:13 +0800
categories: [深度学习,tensorflow]
tags: [深度学习,tensorflow,树莓派]
---

### 故事背景

​			科研课堂选的是TinyML，在小型设备上的深度学习应用。看到第六章布置最简单的程序到开发板上的时候，书上用的库都需要编译(其实官网都有现成的下载，，)，然后就去试着弄弄，结果一弄弄了好久，总结经验教训就是一定要按照官网的做法，用别人用过的方法（惨痛的教训）-_-

---

​			以下的步骤文档都有(但是他没有说会编译静态库，，)，但是记录了过程中自己出现的一些错误。

---

​			**这是在树莓派4B aarch64 Debian11 上进行的。**

### 开始

​		**下面会用到一些工具，如果过程中出现工具上的问题，可以搜索相关问题就可以解决啦。**（~~当然不是因为事后忘记装了哪些东西了~~）

​			去github clone 下来tflite-micro 仓库(tensorflow仓库移除了lite用到的micro文件夹，新建了tflite-micro仓库)

```
https://github.com/tensorflow/tflite-micro
```

​			前往

```
./tflite-micro/
```

​			执行

```
make -f tensorflow/lite/micro/tools/make/Makefile evaluate_cc_test
//这个指令会编译执行 helloworld的测试cc文件同时编译静态库
```

​			如果嫌下载太慢，可以在自己电脑本地下载压缩包传到

```
tensorflow/lite/micro/tools/make/downloads/
```

​			总共有3个，分别为pigweed（这个最难下载下来- -），kissfft，flatbuffers。

​			然后去这个目录

```
./tflite-micro/gen/linux_aarch64_default/lib
```

​			这里有我们想要的C++ 静态库**libtensorflow-microlite.a** ，我们可以选择把它放在系统的lib文件夹里，我这里是

```
/usr/lib
```

​			然后我们需要找到所有的头文件，我尝试了用find + xargs tar 但是不奏效， **并且，有些头文件需要自己单独找出来**，为了赶时间（之前耽搁太多时间），所以我直接把 ./tflite-micro/tensorflow 直接放进了系统的头文件家里面，可以不这样。

```
/usr/include/
```

​			执行到这里，我们可以尝试写个带有helloworld所有的测试文件的头文件的cc文件编译试试，或者直接copy

比如这里我的文件名为test1.cc,另外我为了方便我还把helloworld里面两个cc和两个h文件和它放在一起。

```
g++ test1.cc hello_world_intXXX.cc hello_world-floXXX.cc -o -lm -g -ltensorflow-microlite -DTF_LITE_STATIC_MEMORY

```

​		这里会报一系列错误，最开始是头文件的错误，因为有些头文件如all_ops_resolver.h 被挪动了位置，而有些头文件如flatbaffers/flatbaffers.h这些第三方是因为在这个目录中

```
/home/pi/tflite-micro/tensorflow/lite/micro/tools/make/downloads
```

​		找到缺失的头文件，然后按照目录复制进系统的头文件目录，比如 把 上面的路径/flatbuffers/include/ 中的flatbuffers文件夹复制过去。

​		如果其他类似的头文件没找到，基本都在tflite-micro文件夹中，使用 

```
grep -R /（或者 ./） 文件名 
```

​		如果用gcc 会报错，如果不用 -lm 会报math函数的错， -l + 静态库的文件名，省去前缀lib和后缀.a或者.so,  另外  -L + 路径指定库的位置， **-DTF_LITE_STATIC_MEMORY** 这里不加的话如果只引入头文件没问题，但是后续会RE报段错误， 因为 **input->data.f[0] 并没有赋内存，使用这个参数可以让他赋予内存**(这里加了 -g， 方便用gdb调试，调试可以看看我的帖子)。

​		如果没有什么其他的错误的话那么基本成功了，但是这样是不够的，我想要使用Make来构建项目，这样非常有效率，不用每次都编译长时间，自己亲自动手用Make，Make的基本知识就真的完全记在脑子里了。关于Makefile的编写可以看看我的帖子^ ^.

### 后记

​		总算找到了一条正确的路，浪费了好几天时间，要是其他人来估计早弄完了，，中间碰到一大堆破事，梯子突然证书出问题了，然后重弄，树莓派系统还重装了两次，弄乱了只好重装，后面干脆只用终端，但是现在不能关闭了- -，还有OO和OS 蓝桥杯等着折磨我，菜狗渴望救赎。

​		另外，如果有错误的话，请尽量使用必应和Google，而且在这里碰到的大多数问题github的issue那里基本都有（事后经验），最近打算做个防摔倒的案例给那个科研课堂。

---

---

---

### 分割线！！！！！！！！！！！！！！！！！！！

​			**以下是本人弄错了地方（没看文档，没去仔细看仓库，只需要编译lite就好了，浪费好几天的时间，不过还是编译下来了）orz.**        

​			下面的东西都可以不用看----

---

---

---

### 开始

​			我们需要通过Bazel（跟Make类型差不多）编译Tensorflow源码，Bazel通过[(github.com)](https://github.com/bazelbuild/bazel) 这里可以下载（注意一定要选择版本适合的，这里tensorflow官网(https://www.tensorflow.org/install/source?hl=zh-cn)有对应的版本,我之前没看浪费了好久时间> - <）

---

---

​			~~Tensorflow的源代码可以从(https://github.com/tensorflow/tensorflow) 这里下载（安装时记得切换到相应版本的分支）~~。去Tensorflow 仓库 的micro 前往 tflite仓库，附上直达链接：

```
https://github.com/tensorflow/tflite-micro
```

---

---



### Bazel

​			直接去Github下载和tensorflow对应版本的源码包，然后直接安装，输入:

```
sudo ./compile.sh
```

​			过程需要联网下载java安装（如果之前没有装的话），安装完之后将bazel拷贝到/usr/local/bin 里面去

```
sudo cp output/bazel /usr/local/bin/bazel

```

​			在命令行输入bazel判断是否成功

### Python

​			tensorflow2.6对应的python从3.6到3.9,我选了3.6,但是树莓派自身带了其他版本的python。

​			从Python官网下载源码包，编译安装，然后在目录执行

```
sudo ./configure && sudo make && sudo make install
```

​			这里我碰到过不能获取本地字符集（GB13020）的情况，后面改成UTF8后系统更新就就变得奇怪起来，后面干脆装了最轻的64位系统只用命令行，字符集也懒得改中文了，然后我们需要修改软连接，这个时候输入Python通常会显示系统预装的，我们通过

```
sudo ln -s /(Python path)/python  /usr/bin/python3
```

​			在没有改软连接之前（直接用 alternatives 改默认版本），我还碰到过pip未知命令的情况，这里要找到pip文件，然后将解释器改成自己Python的路径，如果报和下面那行相关错误的话就把下面那行删掉，然后更新pip

```
python pip install upgrade 
```



```
#! /usr/bin/python3
# EASY-INSTALL-ENTRY-SCRIPT: 'pip==20.3.4','console_scripts','pip'   //删掉这行
```

​			然后pip默认是国外源，需要换源，这里可以直接加上

```
-i  
```

​			然后我们安装依赖项(这里可以看后面补)

```
sudo apt update
sudo apt install pkg-config zip g++ zlib1g-dev unzip default-jdk autoconf automake libtool
sudo apt install python3-pip python3-numpy swig python3-dev
sudo pip3 install wheel
```

​			如果没有修改pip也没有修改软连接的话，他是会下载到3.9那个版本去的，Tensorflow会显示numpy未找到。

### 梯子

​			如果不能访问外面的话，tensorflow编译的时候有些东西下载不过来，这里直接去github找v2ray-core ，因为树莓派此时还不能访问github（但是很多东西可以clone？），所以通过电脑下载压缩包上传到树莓派解压安装，配置文件(用v2ray导出就行)用原先的。

---

### swap

```
//查看当前内存和swap大小
free -h
//创建swap交换分区文件
sudo mkdir /swap
sudo dd if =/dev/zero of=/swap/swapfile bs=1G count=8
//格式化该分区
sudo mkswap /swap/swapfile
//设置交换分区
sudo mkswao -f /swap/swapfile
//修改权限
sudo chmod 600 /swap/swapfile
//激活
sudo swapon /swap/swapfile
```

​		具体可以参考这里:[(17条消息) linux 增加swap 分区大小_linux增加swap分区大小_小百菜的博客-CSDN博客](https://blog.csdn.net/u014644574/article/details/127405490)

​		（ps:修改派上那个swap文件的SWAPSIZE 最多只能到2G，，，）

### Tensorflow

​		我从github上clone下来源码，然后checkout到r2.6，执行

```
sudo ./configure		
```

​		这里会出现很多选项，只需要仔细填最开始的Python路径，然后不用安装用不到的东西。

​		然后执行

```
bazel build \
    --local_ram_resources 2560 \
    --verbose_failures \
    --cxxopt="-D_GLIBCXX_USE_CXX11_ABI=0" \			
    tensorflow/tools/pip_package:build_pip_package
```

​		限制内存的使用，错误时显示调试信息，然后gcc版本过高需要加上第三条

​		编译过程中有几个时候使用内存突高，不用swap是真不行，我设置了8G的swap后面才过

​		如果运气好，编译几个小时之后，应该就过了(笑)

### 出现的错误

-  各种版本不对应（包括需要的JAVA和Python）：这里去官网选择对应的版本,如果版本不对应是不能成功编译的
- 树莓派swap太小 :主要在编译tensorflow时碰到，容易死机，我发现最多跑到了6点多G，最后设置成了8G

- 编译时需要联网且翻墙：bazel和tensorflow编译都是需要联网的，并且tensorflow编译时需要翻墙（显示xxx download failed），不然有些下载不下来，我用v2RayA连上自己的服务器，但是下载完后就不用了
- 编译时间过长且易卡顿（差不多算选择上的错误，为什么要用树莓派直接编译呢？？？）bazel编译倒是一个小时差不多完了，但是tensorflow 是真的又长又卡，适合晚上跑

### 总结

​		为了这玩意花了一两天，树莓派的性能还是不太顶，后面觉得自己直接在上面编译是真的有点那啥，