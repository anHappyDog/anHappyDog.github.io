---
title: 在树莓派Raspberry4B下编译Tensorflow
author: lonelywatch
date: 2023-03-7 20:13 +0800
categories: [深度学习,tensorflow]
tags: [深度学习,tensorflow,树莓派]
---

### 故事背景

​			科研课堂选的是TinyML，在小型设备上的深度学习应用。看到第六章布置最简单的程序到开发板上的时候，书上用的库都需要编译(其实官网都有现成的下载，，)，然后就去试着弄弄，结果一弄弄了好久，总结经验教训就是一定要按照官网的做法，用别人用过的方法（惨痛的教训）-_-

---

### 开始

​			我们需要通过Bazel（跟Make类型差不多）编译Tensorflow源码，Bazel通过[bazelbuild/bazel: a fast, scalable, multi-language and extensible build system (github.com)](https://github.com/bazelbuild/bazel) 这里可以下载（注意一定要选择版本适合的，这里tensorflow官网 [从源代码构建  | TensorFlow](https://www.tensorflow.org/install/source?hl=zh-cn)有对应的版本,我之前没看浪费了好久时间> - <）

---

---

​			Tensorflow的源代码可以从[tensorflow/tensorflow: An Open Source Machine Learning Framework for Everyone (github.com)](https://github.com/tensorflow/tensorflow) 这里下载（安装时记得切换到相应版本的分支）

​			

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