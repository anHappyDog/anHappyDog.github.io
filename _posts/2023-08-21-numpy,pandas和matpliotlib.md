---
title: numpy,pandas和matpilotlib
author: lonelywatch
date: 2023-08-21 10:05
categories: [数据]
tags: [PYTHON,数据处理]
---

# 

头痛。

## numpy

### ndarray

numpy的效率很高，ndarray是numpy的一个数据容器，支持向量化操作。

```python
# np.
#接收一切可序列化对象，可指定数据类型
array(arr，dtype) 
#和array类似，但是asarray如果输入是数组，
#则不会创建新数组，而array会。
asarray(arr,dtype)
#接收维度tuple来生成全0数组
zeros(size)
#和zeros类似
ones(size)
#创建没有初始值的数组
empty(size)
#和python的arange相同
arange(start,end,steps) 
```

ndarray本身带有一些方法和属性：

```python
# somearr.
#返回当前数组的类型,维度，维度数
dtype,shape,ndim
# 转化原本的类型，生成副本
astype(type)
# 重写了很多方法，使得数组能像标量一样使用运算符。
# 该运算符会运用到数组中对应的每个元素，并且返回结果。

###
# 可以指定轴进行排序
sort()
```

#### 切片与索引

##### 基本

ndarray除了支持python原本的切片和索引操作以外，还支持用逗号分隔索引值。例如：

```python
a1 = np.arange(1,126).reshape(5,5,5)
a1[1,2,3]
a1[1][2][3]
# 切片与索引还可以混合
a1[1,2,:3]
# 或者
a1[:,2,1:]
```

##### 布尔索引

可以将布尔数组作为索引值进行索引（布尔数组的长度必须和数组轴索引长度一直）。

##### 神奇索引

使用整数数组作为索引值（选取出原数组中以轴索引为单位的子元素按照整数数组进行排列）。

#### 数组与文件

通过`np.save(filename,arr)`将arr保存为npy后缀的文件，通过`np.load(filename)`将文件提取为数组并返回。

如果想同时保存多个数组，可以使用类似的`savez`函数保存为npz文件，提取时返回一个字典，键值分别为savez时的参数名和存储的数组。

`savez_compressed`会将若干数组存入已经存在的npz文件中。



#### 其他的操作

```python
#np.
# 用于矩阵内积 和@相同
dot(a,b) 
# 下面是一些ufunc(通用函数)，进行主元素操作
# 求平方差和平方
sqrt(a) square(a)
# 求绝对值
abs(a) fabs(a)
# 进行e指数运算,以数组元素作为指数运算
exp(a) power(a,b)
# 比较产生最大值和最小值数组
maximum(a,b) minumum(a,b)
# 去重并排序
unique(a)
# 检测一个数组是否包含另一个数组中的某个值
#结果为一个和原数组等大小的布尔数组
in1d(a,b)
# 对于一个数组，有
# someArr.
# 提供矩阵的转置，和方法transpose的基本功能类似
T 
# 根据传入参数进行维度的交换，产生副本
transpose()
# 求逆矩阵
inv()
```



### random

numpy中一个很重要的模块就是random，用于随机生成数据。

```python
#np.random.
# 生成(d1,..dn) dimension的[0,1)之间的随机浮点数
rand(d1,...,dn) 
#生成区间内的指定数量和类型的随机数组 
randint(low[,high,size,dtype]) 
#生成(d1,...,dn)标准正态分布的随机浮点数数组
randn(d1,...,dn) 
#生成一组正态分布的随机浮点数组
normal(loc,scale[,size]) 
#生成给定范围内的均匀分布随机浮点数
#组，size为维度tuple
uniform(low[,high,size,dtype]) 
#生成[0,1)的size数量的随机浮点数组
random(size) 
#生成二项分布随机数组
binomial(n,p,size)


###
# np.random.
#用于打乱一个数组，不会生成副本
shuffle(arr)
#打乱数组的副本，不修改原数组。
permutation(arr) 
#决定随机种子
seed()
```

## pandas

pandas中两个很重要的容器就是Series和DataFrame。

### Series

Series就像是python字典的加强版。包含索引标签（默认为range）。

可以使用数据和索引，以及字典进行创建。实例`index`的属性返回。

Series有自动对齐属性，类似于np的向量化操作，对Series的操作会自动适用到其中的每个对应元素，如果不存在对应元素则返回NA(元素缺失)。

Series也支持np中的切片和索引操作。

一些操作：

```python
# someSeries.
# 返回索引列表的视图
index
# 返回其数据列表的视图
values
#可以修改Series的名称以及索引列的名称
name index.name

# pd.
#返回Series是否缺失元素的布尔Series
#这也是Series实例的方法
isnull(someSeries)
notnull(someSeries)


```

### DataFrame

DataFrame可以看作为具有共同索引的Series的字典。

可以通过包含等长数组或字典（外字典键作为列，内字典键作为行）的字典来构建DataFrame。构建时可以指定column来固定列的排序。

如果要获取DataFrame中的某一列，可以使用`.`运算符获取符合python变量名的列视图，也可以通过`[]`操作符获取列数据视图。

可以通过`loc`元素获取行数据视图。

对于获取的视图，可以直接赋予标量值或者数组修改数据，并且DataFrame也支持numpy的切片和索引操作。

对于Series和DataFrame的一些操作：

```python
## pd. someSeries. someDataFrame.
#返回其前5个元素 和最后5个元素
head() tail()
# 在副本上改变行列索引
reindex(index=,column=)
# 在副本上删除行数据，如果设置inplace=True，则会在原本数据上删除
#在Series中根据传入索引值列表或单个索引值
#在DataFrame中根据传入索引值列表或单个索引值
#也可以传入axis=1删除若干列数据
drop()
# 用于选取部分数据
# 传入切片或索引值
# iloc使用整形索引
loc[] iloc[]

# 对于DataFrame之间的算数操作
# 使用具体的函数可以指定缺值时填充的默认值
# 对于DataFrame和Series之间的操作
# Series会自动广播到适用于DataFrame的大小

#用于排序，可指定axis,ascending决定依靠行还是列进行升降序
# 对于DataFrame若要根据若干列为关键字，使用by属性
# 赋予列名数组来进行排序
sort_index()

#索引可以是重复的
#判断索引是否重复
is_unique()

#求和，可指定axis决定求和的位置
#一般会跳过所有na，指定skipna是否跳过缺失值
sum()

#对数据进行汇总
describe()
```

### 与文件的交互

pandas支持多种类型的文件导入和导出。

对于csv文件，可以使用`read_csv`和`read_table`进行导入，使用`to_csv`进行导出。

对于其他文件也是类似。

```python
# names可指定默认header值，
# index_col指定若干列为索引
# na_values指定缺失值的标识符
# nrows指定读取行数
# 对于大文件可指定chunksize分块读取
pd.read_csv()
#sep指定数据的分隔符，可使用正则表达式
pd.read_table()

#将数据写入csv文件
# sep指定分隔符
# na_rep指定na的标识符
# index和columns指定行列写入顺序
pd.to_scv()

```

特殊的，对于HDF文件（分层存储文件，用于存储大量数据），除了python中的其他库以外，还可以使用pandas中的HDFStore类来进行处理。使用`to_hdf`和`read_hdf`来进行hdf文件的导入导出。

对于Excel文件，可以使用`to_excel`和`read_excel`进行导入导出。

### 对于NA的处理

对于na要么是使用`dropna`过滤na值，要么是使用`fillna`将na替换为某些值。

```python
# 可指定axis，默认删除所有带有na的行
# 指定how=‘all’来删除只有na的行
dropna()
#填充na，使用字典为不同列设置不同的填充值
#使用inplace=True方法来在原数据上修改
fillna()
```



## matpilotlib

