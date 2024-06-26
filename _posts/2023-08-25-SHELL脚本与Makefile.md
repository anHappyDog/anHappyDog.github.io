---
title: SHELL脚本与Makefile
author: lonelywatch
date: 2023-08-25 10:05
categories: [SHELL]
tags: [SHELL,LINUX,Makefile]
---

## shell脚本

shell脚本为由shell命令和一些语句组成的文件。shell有不同版本，下面所讨论的基本为bash shell。在文件第一行使用`#!`来指定使用的shell版本。

### 变量

变量分为环境变量（可通过set指令查看所有环境变量）和用户自定义变量。

变量名由任意字母，数字下划线组成，且不超过20个字符，并且区分大小写。

自定义变量时，变量名，等号以及值之间不能留有其他空白字符。变量在shell脚本中以字符串的格式存储，并且由使用的命令决定格式。在使用变量时，通过`$`引用，有时也使用`${}`来界定使用的变量。

可以将命令的输出赋予变量（命令替换），有两种方式：

- 使用反引号将命令括起来
- 使用`$()`将命令括起来

在shell中可以使用`expr`命令进行数学运算（对于一些特殊的运算符需要使用反斜线）。也可以使用`$[]`来执行数学运算并返回。bash shell 是不支持浮点数运算的。

shell 的退出使用`exit <exitcode>`，结束时会返回该退出状态码（一个uchar大小的整数），在linux中使用`$?`来保存该退出码。如果不使用exit，则返回最后被执行的命令的返回码。

### 结构化命令

我把他们当作是控制流，大概有条件判断以及循环等类型。

#### if-else

其基本形式为：

```shell
if command1
then
	command2
elif comand3
then 
	command4
...
else
	commandn
fi
```

if条件为命令的执行语句被执行，当且仅当该命令的返回码为0。

可以使用`test <condition>`指令和`[ <condition> ]`（[]与条件间必须保留空格）来判断除命令返回码之外的条件。

对于条件判断有：数值比较，字符串比较，文件比较。

比较可以使用比较符号（但是在使用一些在shell中有其他用途的符号时需要转义），也可以使用`-<cpr>`,cpr基本类似形式：eq，ne，gt，lt，le，ge(对于字符串比较和文件比较，还有其他的单字母比较符)。

常用的有：使用`-n`和`-z`检测变量是否为空，`-d`检测文件是否为目录,`-e`检测文件是否存在。

对于复合条件，使用`[ ]&&[]`和`[  ]||[  ]`的形式。

bash还提供了一些条件判断的高级特性：使用单括号使用子shell，使用双方括号来对字符串进行额外的处理，

事实上，if-else可以简化为`[[ else-condition ]] || command;`如果else-condition不满足才会执行后面的command。

#### for

类似于for-in，在shell中的for基本为：

```shell
for var in list
do 
	command
done
# 选自linux命令行与shell脚本大全
```

list可以是列表字面量，也可以是变量。可以通过指定`IFS`变量的值来指定分隔符。

bash shell中也提供了c语言分隔的for，基本为：

```shell
for ((variable assignment;condition;iteration process))
do 
	commands
done
# 选自linux命令行与shell脚本大全
```

在for括号中，等号两旁可以有空格，变量不需要`$`引用。

#### case

作用类似于switch语句，基本形式为：

```shell
case variable in
pattern1 | pattern2) command;;
pattern3) command2;;
*) commands;;
esac
# 选自linux命令行与shell脚本大全
# *为通配符
```

#### while和until

```shell
while/until [ condition ]
do
	command
done
```

while的条件为命令时，当命令返回码为0时会执行一次循环，until与while相反。

---

对于有do，并且前面有指令的情况，可以将do和该指令放在一行，用`;`隔开。

也可以使用`break`和`continue`来控制语句。

在这些语句后都可以使用重定向符。

### 输入

可以使用命令行参数向shell脚本传递数据，在shell脚本中`$i`为输入的第i个参数（从1开始，$0为脚本名）特殊的，`$#`为命令行参数的个数，`$*`和`$@`包含所有命令行参数。

`shift`指令用来移位参数，将参数整体向前推一个，最前面的参数会被抛弃，可以和case结合用来处理脚本的选项参数。在linux中，可以使用`--`将选项与其他参数分离开来。

## Makefile

​	make是用来更新文件的工具（基本用于编译）。make存在多个变种及版本，其中在linux上使用最多的是GNU make，下面所讨论的基本都为GNU make。make需要makefile描述文件（通常名为Makefile，makefile或GNUMakefile）。make指令会根据指定的目标（不指定则为默认目标）判断前置条件是否满足，然后执行相应的指令。

make中存在着`时间戳`的概念，对于生成一个目标，如果前置要求的时间戳晚于生成目标，或者生成目标不存在，就会调用目标对应的指令，否则不执行，以此来提高效率。

与Shell脚本相同，采用`#`作为注释标记。采用`\`作为一行的延续。



### 规则

Makefile文件中的主体为一组组规则，基本形式为：

```makefile
target1 [target2 targetN]: [prerequisite prerequisite2 prerequisiteN]
	command1
	command2
	commandN
```

target为目标名（标识名，可以为文件名，也可以为其他），prequisite为前置要求（可以为其他文件，也可以是其他目标），后面紧跟着一群带有**tab**的shell指令（是否一定要由tab标出也可以修改Makefile文件的配置）。

当make指定target目标时，会检测前置要求是否被满足，如果不满足，则查找是否存在对应该前置要求的规则，如果存在，则先调用该规则，满足该前置要求，否则报错。如果所有的前置要求都被满足，就会执行目标对应的shell指令（*调用时会输出该指令，在指令前添加上`@`来避免输出指令，直接输出指令的输出*）。

在Makefile文件中，第一个目标为默认目标，如果make不指定生成的目标时，就会执行生成默认目标。

在Makefile中通常采取“从上到下”的结构，首先编写最主要的目标规则，随后编写对应主目标的前置要求的规则以及其他规则。执行时使用"从下到上"的方式。

#### 通配符

makefile中提供了通配符来匹配文件，比如*匹配所有字符，?匹配单个字符，[...]匹配一些字符[^...\]不匹配一些字符。这里特殊的是，如果通配符在目标和前置要求中使用，则会被make立刻扩展，如果在指令中，则会被子shell扩展。

#### 假想工作目标

事实上，所有目标名不为生成文件名的目标都为假想工作目标，因为他们不会生成该目标名的文件，所以他们总是会被make执行。最典型的就是`clean `，用来清除一些文件。在GNU Make中，使用`.PHONY`来标注假想目标文件，这样即使存在同名的其他目标，也不会产生错误，基本形式类似于：

```makefile
.PHONY: clean
clean:
	rm *.o
```

#### 具体规则

具体规则就是指 目标和前置要求都为具体文件名的规则。

#### 模式规则

make中存在着一些内置规则，其中对于c语言的编译，有下面这些：

- 使用.l文件生成.c文件
- 使用.c文件生成.o文件
- 使用.o文件生成无后缀文件

事实上，利用make中的内置规则，具体规则中的大部分命令（例如gcc编译指令等）都可以省略。这几条内置规则的实现使用了通配符`%`（类似于unix中的`*`，能够匹配不定量任意字符）

#### 后缀规则（过时）

后缀规则完全可以通过模式规则来实现。

### 变量

make中也存在变量，变量名最好是大小写数字加下划线（对于内部变量，最好全小写，对于常量以及用户定义变量，则使用全大写和下划线）。变量的定义基本为：

```makefile
variable assignSymbol variableValue
```

其中assignSymbol可以为

- 简单赋值(:=，赋值右侧会全部扩展直至原始值)
- 递归赋值(=，将右边的值赋给变量，其中的变量会在使用时才会被扩展)
- 条件赋值(?=，如果变量名之前不曾定义过才会被赋值)
- 附加赋值(+=，将赋值符号右侧的值正确地附加在原先的变量后)

对于variableValue，会删除其与赋值符之间的空格，但是会保留variableValue之后的空格。

#### 自动变量

在某一正确的规则中，可以使用自动变量来优化Makefile的编写：

- $@ : 目标名
- $< : 第一个前置要求名
- $? : 所有的时间戳在目标后的前置要求名，并以空格隔开
- $^ : 所有的不重复的前置要求名，并以空格隔开
- $+ : 所有的前置要求名，并以空格隔开

#### 变量的使用

对于变量名为单个字母的变量，可以直接使用`$`进行引用（这是不好的习惯），对于其他变量，则需要使用`$()`（这与shell脚本中的命令替换是相同的符号，，）也可以使用`${}`。

#### 变量的来源

变量可以来自Makefile文件，

可以来自用户的输入（用户的输入会覆盖文件中的变量以及环境变量，环境变量的优先级很

```makefile
make CC=cc
```

低），也可以来自引用的mk(makefile fragment)文件(makefile通过`include`来引用)。

mk文件可以包含Makefile文件中的一个片段，不单单是变量，也可以是一组规则和指令。

```makefile
include include.mk
```

上层的Makefile变量不加限制也会传递得下层的Makefile。

#### 条件编译

Makefile中也存在条件编译，使用`ifxxx`（ifeq,ifneq , ifdef ,ifndef），`else`以及`endif`等关键字。基本形式为：

```makefile
ifdef xxx
assign
	commands
else
assign
	commands
endif
```

这里需要注意的是，指令前仍然需要带有tab格。

### Make的运行

make的运行可分为两阶段：

第一阶段需要读取所有的变量，针对不同类型决定是否立即扩展，读取所有的规则，建立起依存图，在第二阶段，则根据该依存图，根据make命令寻找需要更新的目标，并执行其指令。

### 函数

make中函数可以分为：字符串操作，文件名操作。流程控制，用户自定义函数以及其他函数。

函数的定义可以通过**宏**来定义（事实上宏也可以用来定义变量，又或者说变量也可以用来存储指令）；

函数的使用形式为：

```makefile
$(func-name [arg1,arg2,...argN])
```

调用自定义函数时需要在函数名前加上call并隔开。

#### 字符串操作

- `$(filter pattern text)`会返回text中满足pattern（可使用%）的元素。

- `$(filter-out pattern text)` 会返回text中不满足pattern（可使用%）的元素。

- `$(subst search_text,replace_text,text)`将text中的元素所有search_text都替换成replace_text(不支持%，常用来替换文件后缀名)

- `$(patsubst search_pattern,replace_pattern,text)`支持使用一个%但是匹配的是整个元素，功能与subst类似。

- `$(words text)`返回text中的单词数（被空格隔开）

- `$(firstword text)`返回text中的第一个单词，否则为空
#### 自定义函数

类似于定义变量，只是存储的是指令序列，使用参数和shell相同($N)。


#### 其他函数

- `$(sort list)`去重并排序。

- `$(shell command)`在子shell中执行command，并返回结果。

- `$(strip text)` 去除前后所有空格，将单词之前的空白换成一个空格。
#### 文件名函数

- `$(wildcard pattern...)` 对列表的每个模式进行扩展。
- `$(dir list...)`返回list中每个单词的目录部分。
- `$(suffix name...)`返回每个单词的后缀。
- `$(basename name...)`返回每个单词不带后缀的部分。
- `$(addsuffix suffix,name...)`为每个单词添加后缀。
- `$(addprefix prefix,name...)`为每个单词添加前缀。


###  递归Make

对于含有多个目录的Make项目，可以使用递归Make，基本形式为：

```makefile
$(MAKE) --directory=
```

MAKE变量始终指向make的实际位置，该指令会并行跳转到directory指定的目录，执行make。

一个有趣的地方是，该方法也可以通过子shell执行`cd`与make来实现（甚至在最初的0.11linux中的make也是通过cd实现make子目录的，只不过目标带有路径）。

### 非递归Make

通过引用子目录的mk文件来实现make子目录。
