---
title: javascript
author: lonelywatch
date: 2023-7-10 10:14 +0800
categories: [WEB]
tags: [WEB,javascript]
---

## 语法

注释采用C语言分格。语句使用分号隔开。使用`"use strict"`开启严格模式。

### 变量

使用`var`,`let`和`const`来定义变量。

#### var

使用var定义的变量为当前函数的局部变量（在函数中如果省略var则是定义一个全局变量）。并且var的作用域为整个函数作用域，在同一作用域var变量被定义前使用也是可以的（声明提升）。

#### let

let与var不同的是，let的作用域为块作用域，而var为函数作用域。let也不会有变量提升。同时，let在全局作用域中也不会称为window对象的属性。

很神奇的是在for循环中var申明的迭代变量会渗透到循环外，而let不会，所以for使用let。

#### const

类似于定义常量，但是因为不能指定类型，所以在声明时必须初始化变量。并且类似于`const char* t`这种，其限制只适用于它指向的变量的引用，修改其内部属性并不违反限制。

#### 数据类型

简单数据有：`Undefined`,`Null`,`Boolean`,`Number`,`String`和`Symbol`，复杂类型有`Object`。Javascript不能定义自己的数据类型。使用（操作符而非函数）`typeof`来确定变量的数据类型。

一些特殊的函数如`Number`,`parseInt`,`parseFloat`可以将一些类型转化为数字类型。`toString`转化为字符串类型，`isNaN`判断是否不是一个数字类型。 javascript不支持表达任意大小的数字。

字符串插值使用`${ 内容 }`也可以使用加号拼接。

### 语句

C语言的控制流语句在javascript中都有，javascript还包括`for-in`和`for-of`语句。for-in语句负责美剧对象中的非符号键属性，并且是无序的。for-of用于遍历可迭代对象的元素。

```javascript
for (property in expression) statement
for (property of expression) statement
```

#### 标签语句

使用`label`来实现汇编中标签的功能，使用break和continue回到相应的位置（同时执行相应的作用）。

### 函数

使用`function`关键字来声明。

```javascript
function functionName(arg0,....,argn) {
    statements
}
```

### 值与引用

Javascript也分值和引用。

## Document

document是当前html的对象，js使用document来与html进行交互。

常见的方法：

```javascript
document.querySelector("selector");
//根据选择器查找第一个合适的元素，返回其对象，否则返回null

```

