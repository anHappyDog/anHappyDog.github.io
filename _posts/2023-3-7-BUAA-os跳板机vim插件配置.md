---
title: BUAA-os跳板机和树莓派vim插件YCM,NERDTree配置并尝试访问外网（失败）
author: lonelywatch
date: 2023-3-7 23:03 +0800
categories: [BUAA,os]
tags: [gdb,cli,os]
---

## 说明

​		（最后发现认证不了！！！！**没学python的悲痛**）学校的服务器是能够访问学校内部的所有网络的，因此也能够登录校园网认证，我们只需要利用python写的脚本(github上一位19级学长写的)就能够登录认证然后就能访问外网（一开始只想去github下vim的插件，但是下不了，想着离线安装，结果发现YouCompleteMe还要下载三方包巨麻烦），我们通过学校内的gitlab，创建一个分支，这样就可以上传下载到我们的跳板机上面，然后安装python，安装依赖，使用python登录认证，事实上，**如果能够认证的话**，到了这里，这个跳板机就跟普通的服务器没有啥区别了（悲）。 

---

​		因为树莓派能连接外网，所以先安装Vundle然后安装其他要方便很多，跳板机也要安装Vundle管理插件和设置，树莓派上是多用户的，不同用户的设置是不同的(都在各自的主目录下)，但是有总的设置文件？我懒得去找了。

### Vundle.vim

github网站：

```
https://github.com/VundleVim/Vundle.vim
```

然后前往vimrc，跳板机为.vimrc 但是设置的内容是相同的,按照安装文档

```
set nocompatible              " be iMproved, required
filetype off                  " required

set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'

call vundle#end()            " required
filetype plugin indent on    " required

" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-
```

​		树莓派直接按照指示添加github.com的后缀名就可以安装下载，部分需要编译安装，跳板机需要下载完安装再添加。

如

```
Plugin 'ycm-core/YouCompleteMe'
```



#### NERDtree

​		普通的vim是不能在编辑的同时显示文件树的（虽然可以打开多个文件逐一编译），NERDtree提供了文件树的显示，十分方便。这是仓库

```
https://github.com/preservim/nerdtree
```

​		然后按照指示去vimrc文件设置快捷键配置，在os上因为是网页所以不能用 Ctrl + w + w 切换界面，所以我这么设置

```
map<F3> : NERDTreeMirror<CR>
map<F3> : NERDTreeMirror<CR>
nnoremap<F4> : NERDTreeFocus<CR>

//
分别是 打开，关闭文件树 和 切换到 文件树页面（因为不能切换页面，也没去详细查快捷键设置了）
```

​		这里我没去查map和nnoremap的不同，NERDtree不同的宏也没去查了，后面要用到再去。

​		基本的使用就满足了，NERDTree还有很多操作，这里可以输入?查看各种操作，很方便。

### GRUVBOX

```
https://github.com/morhetz/gruvbox
```

​			一款好看的主题，下载安装，然后把GRUVBOX文件夹中的colors文件复制到vim或者.vim文件夹中（后面不再赘述）, 我是把autoload也复制过去了，然后在vimrc文件中设置

```
syntax on
syntax enable
set t_Co=256
colorscheme gruvbox
```

​		这里必须加 set t_Co=256 具体原因我也没去查，然后 colorscheme使用主题，上面两个syntax也要带上，这里设置完后进去就是亮主题，加上

```
set bg=dark
```

​		变成我喜欢的黑色系主题。

### YouCompleteMe

​		支持代码补全提示，跳转定义申明等等。

```
https://github.com/ycm-core/YouCompleteMe
```

​		非常非常非常好用（尤其是os课上，速率加倍），但是配置非常非常非常麻烦，主要是下载麻烦，还有很多依赖项。

​		首先它需要python3.8及以上的版本，并且需要共享编译才行（两步没有一步他都会提醒你）即 编译安装 configure那一步时加上--enable-shared 

```
sudo ./configure --enable-shared
```

​		python的配置又是一篇帖子。		

​		然后正常安装，然后按照ycm的安装文档进行操作，缺少依赖就去网上搜索安装下来，都可以找到（其实是全忘记了，，）， 然后指定安装补全的语言（默认支持python），这里我选了c家族，即 

```
python3 ./install.py --c-completer (指令大概长这样)
```

​		然后他会下载第三方库（痛苦开始了），最简单的办法是多试几次，就好了 即

```
git submodule --init upgrade --recursive  // 大概长这样
```

​		如果在不同架构的电脑上下载，最后vim会报E492 ^M not found 的错，这是因为windows 或者有些系统\r\n,而linux则是\n, 我们可以去每个配置文件设置文件格式,即

```
:set fileformat=unix
:w
```

​		但是文件太多，而且容易出错。

​		最后显示编译成功，我记得有个地方可以加 --verbose,然后显示详细编译信息，编译成功后，还可能会碰到 ycmd server shutdown， 然后这里会有err log，去/tmp/文件夹找，里面有详细报错，都可以在网上找到（全忘了）。

​		还有个错是，.ycmdXXCONF.py not found, 这是隐藏的配置文件，在example文件夹中找个能用的设置路径就好了。

​		最后就可以放心食用辣。

---

​		最后附上我的设置文件（忘了）

```
```



​		