---
title: jetson nano编译最新llama-cpp与使用
author: lonelywatch
date: 2025-1-7 1:28 +0800
categories: [ML]
tags: [AIGC,ML]
usemath: latex
---

# 声明

> 虽然后续能够安装CUDA11.0版本，安装时由于覆盖了10.2版本的部分库，jetson nano的GPU就抛锚了，llama.cpp也不会利用jetson nano其自带的GPU，所以要么将CUDA11的驱动直接移植到Jetson nano里面（估计难度很大，而且有可能不行），要么只能使用古早版本。jetson nano使用的Maxwell GPU嵌入在soc里面，`nvidia-detector`包括`nvidia-smi`都是无法检测的。

# 说明

nvidia给jetson nano提供的jetson pack只有4.6.1,里面Ubuntu的版本为18.04，所以如果需要编译当前最新的llama.cpp(hash:44d1e796d08641e7083fcbf37b33c79842a2f01e),需要安装一些额外的工具:

- cmake(版本要求3.18以上，可以安装最新版本:[下载地址](https://cmake.org/download/))

- cuda-toolkit(版本要求11.0以上，安装可以参考[这里](https://blog.csdn.net/weixin_38076609/article/details/128131332))

- gcc(要求10及以上，需要注意cuda-11.0要求所使用的gcc后端大版本不能超过9，但是你仍可以使用更新的gcc以及g++版本来编译其他部分,最新版本的gcc可以编译安装或是添加test仓库,gcc-8可以通过包管理器直接安装，如果最新版本的gcc安装时不使用`update-alternative`而是使用软链接，若是使用编译安装请不要忘记将所对应版本的库`<paath>/lib64`添加入`LD_LIBRARY_PATH`中，否则编译时会出现libstdc++fs的错误;`LD_LIBRARY_PATH`也会影响系统支持GLIBC的版本)


安装完以上工具后，可以开始编译llama-cpp:

```shell
cmake -B build -DGGML_CUDA=ON -DCMAKE_CUDA_STANDARD=11 -DCMAKE_C_COMPILER=/usr/local/gcc-12.3/bin/gcc -DCMAKE_CXX_COMPILER=/usr/local/gcc-12.3/bin/g++ -DCMAKE_CUDA_COMPILER=/usr/local/cuda-11.0/bin/nvcc -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-11.0 -DCMAKE_INSTALL_PREFIX=/usr/local/llama.cpp

cmake --build build --config Release -j10

sudo make install
```

如果遇到关于cuda模板参数非常量的错误，直接删去该模板参数，将其设置为普通参数并删除其中的`static_assert`。

如果遇到关于`__builtin_assume`未定义的错误，直接删除。


编译完成后直接将`/usr/local/llama.cpp/bin`文件夹添加入`PATH`并将`/usr/local/llama.cpp/lib`添加入`LD_LIBRARY_PATH`即可。

## GCC编译安装

```shell
sudo apt update
sudo apt install build-essential manpages-dev
sudo apt install libgmp-dev libmpfr-dev libmpc-dev flex bison

wget https://ftp.gnu.org/gnu/gcc/gcc-12.3.0/gcc-12.3.0.tar.gz
tar -xvf gcc-12.3.0.tar.gz
cd gcc-12.3.0

mkdir build && cd build
../configure --enable-languages=c,c++ --disable-multilib --prefix=/usr/local/gcc-12.3
make -j$(nproc)
sudo make install
```

## GCC包管理器安装

```shell
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt update
sudo apt install gcc-10 g++-10 gcc-9 g++-9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 60
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 60

```

## CMake编译安装

前往[这里](https://github.com/Kitware/CMake/releases)下载所需版本的sh脚本,然后执行该脚本。将安装后得到的文件夹迁入`/usr/local/cmake-<version>`（个人习惯）,然后编辑`~/.bashrc`修改PATH环境变量，添加`/usr/local/cmake-<version>/bin`。

最重要的是添加`CMAKE_ROOT`环境变量为`/usr/local/cmake-<version>/share/cmake-<version>`。

最后执行`cmake --version`查看是否安装成功。

## llama.cpp使用

使用`llama-cli`来使用模型并进行推理,`-h`查看使用。

例如:

```shell
llama-cli -m ../Gpt2-163M-F16.gguf  -p "Hello?" -cnv -co  -n 128

# -cnv 开启对话模式, -co显示不同颜色, -n 128限制最大token数为128 -p定义prompt -m指定模型路径
```

使用`llama-quantize-stats`评估模型不同量化的性能。

例如

```shell
llama-quantize-stats -m ./Gpt2-163M-F16.gguf
```


# 模型获取与转换

可以直接通过python的`huggingface_hub`包进行下载hf上的模型。

```python
from huggingface_hub import snapshot_download

model_addr = "openai-community/gpt2-xl"

model_name = model_addr.split('/')[1]

snapshot_download(
    repo_id=f"{model_addr}",
    ignore_patterns=["*.h5","*.ot","*.msgpack","*.tflite"],
    local_dir=f"./{model_name}"

```

之后使用llama.cpp带有的`convert_hf_to_gguf.py`进行转换，例如`python3 ./convert_hf_to_gguf.py -m gpt2-xl`，转换的gguf模型文件存放在该文件夹中。顺带一提，gguf格式基于ggml格式，可以使用相应脚本进行转换。
