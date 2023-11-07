---
title: ruby on rails
author: lonelywatch
date: 2023-10-7 23:03 +0800
categories: [WEB]
tags: [RUBY,WEB]
---





## 数据库console

rails有`rails console`和 `rails dbconsole`两种console模式，分别代表着编程式的命令行和传统数据库的命令行。

在`rails  dbconsole`创建的console中，使用sqlite的语法对数据库进行操作，在`rails console`中编写代码来对数据库进行操作。

## test

ROR包含测试，在项目根目录使用`rails test`进行测试。

如果出现数据库未migrate的错误，需要`rails db:migrate RAILS_ENV=test`来将数据迁移到test的数据库种。

test会对MVC进行单元测试。