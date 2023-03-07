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

​			我们需要通过Bazel（跟Make类型差不多）编译Tensorflow源码，Bazel通过[bazelbuild/bazel: a fast, scalable, multi-language and extensible build system (github.com)](https://github.com/bazelbuild/bazel) 这里可以下载（注意一定要选择版本适合的，这里tensorflow官网 [从源代码构建  | TensorFlow](https://www.tensorflow.org/install/source?hl=zh-cn)有对应的版本,我之前没看浪费了好久时间> - <）Tensorflow的源代码可以从[tensorflow/tensorflow: An Open Source Machine Learning Framework for Everyone (github.com)](https://github.com/tensorflow/tensorflow) 这里下载（安装时记得切换到相应版本的分支）

​			在安装编译Bazel之前我们需要准备好所有的依赖项,然后编译安装,需要设置环境变量

​		----------------------------- 2023.3.7----------------------23：46 ------------------------------------------------------------



### 出现的错误

-  各种版本不对应（包括需要的JAVA和Python）：这里去官网选择对应的版本,如果版本不对应是不能成功编译的
- 树莓派swap太小 :主要在编译tensorflow时碰到，容易死机，我发现最多跑到了6点多G，最后设置成了8G

- 编译时需要联网且翻墙：bazel和tensorflow编译都是需要联网的，并且tensorflow编译时需要翻墙（显示xxx download failed），不然有些下载不下来，我用v2RayA连上自己的服务器，但是下载完后就不用了
- 编译时间过长且易卡顿（差不多算选择上的错误，为什么要用树莓派直接编译呢？？？）bazel编译倒是一个小时差不多完了，但是tensorflow 是真的又长又卡，适合晚上跑

### 总结

​		为了这玩意花了一两天，树莓派的性能还是不太顶，后面觉得自己直接在上面编译是真的有点那啥，