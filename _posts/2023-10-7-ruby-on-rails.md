---
title: ruby on rails
author: lonelywatch
date: 2023-10-7 23:03 +0800
categories: [WEB]
tags: [RUBY,WEB]
---

# ROR

rails是典型的MVC web框架。主要分为Controller，Model和View。其中View负责界面交互，Model负责数据的存储，而Controller则负责连接Model与View，进行相应的操作。

rails同时是一款敏捷开发的框架，其热更新机制和充满惯例的开发使得使用ROR开发出一款常见的应用十分简单迅速。rails通过`generate`指令，可以生成MVC以及脚手架scaffold其所有模板内容。

通过`rails new <name>`来创建一个基本的rails项目。

一个rails项目大致有下列结构：

```
- app 
- bin 
- config
- db
- lib
- public
- test
- tmp
- vendor
Gemfile Gemfile.lock .gitignore config.ru Rakefile
```

其中主要部分有app,config,db。其中app存放mvc，help（辅助方法），mails和assets等主要内容，config包含应用的配置文件（包含路由route.rb），其中db存放迁移以及相关文件。storage存放三个环境下的sqlite3文件（ror默认使用sqlite3，并且ror分为development，production，test 3个环境，支持test单元测试----虽然很多框架都支持）。

public存放编译后的资产文件，test存放测试文件，Gemfile存放gem包的依赖。

---

通过rails查看rails支持这么些指令：

```shell
 generate     Generate new code (short-cut alias: "g")
  console      Start the Rails console (short-cut alias: "c")
  server       Start the Rails server (short-cut alias: "s")
  test         Run tests except system tests (short-cut alias: "t")
  test:system  Run system tests
  dbconsole    Start a console for the database specified in config/database.yml
               (short-cut alias: "db")
  plugin new   Create a new Rails railtie or engine
```

`rails server`来启动项目。

`rails generate ..`来生成mvc等其他内容。

`rails console`来进入console命令行。

`rails test`进行测试。

`rails dbconsole`进入sqlite3命令行

`rails routes`查看项目路由

## 使用Bootstrap

- 修改Gemfile文件,增加下列内容

```ruby
gem "sassc-rails"
gem "bootstrap"
```

- 执行`bundle install`
- 修改application.css为application.scss，并且进行修改

```scss
//删除 所有的*=require 和*=require_tree
//引入bootstrap
@import "bootstrap";
```

## Model

使用`rails g <ModelName> <..attribute>`来创建模型。

### 修改模型与模型迁移

如果要对模型进行修改，是要创建新的数据库迁移：

```shell
rails g migration Add<Attribute>To<Model> <attribute>:<type>
```

这里创建新的属性。（事实上，这也是惯例，如果是添加属性，必须写成上面的形式）。

```shell
rails g migration Change<DataType>For<FieldName>
```

这里修改某一属性的种类。

```shell
rails g migration Remove<FieldName>From<Model> 
```

这里删除某一属性。

执行完上述指令后，会在db/migrate中生成迁徙文件。（也就是一个类，里面包含一些辅助方法来实现模型的改变）。

使用`rails db:migrate`来应用这些迁移。

如果



### 外键与级联

通过generate创建模型时可以指定外键，比如`rails g article:references `默认以articles的id作为外键。

也可以在模型文件中进行设置。

```ruby
//在模型中添加外键
class Article < ApplicationRecord
  has_many :comments, dependent: :destroy
end
```

其中has_many表示和Comment的关系为一对多,后面的dependent则指出删除该元素时，对于其参照元素的处理操作，这里指定为删除。

其他的关系还有:`belongs_to`，`has_one`,`has_many :through`(或者是`has_and_belongs_to_many`)

### 常见Model属性类型

创建时需要制定的属性类型

1. `:binary` - 用于存储二进制数据
2. `:boolean` - 用于存储布尔值，即true或false
3. `:date` - 用于存储日期
4. `:datetime` - 用于存储日期和时间
5. `:decimal` - 用于存储精确的小数，通常用于财务计算
6. `:float` - 用于存储浮点数
7. `:integer` - 用于存储整数
8. `:primary_key` - 用于存储模型的主键
9. `:string` - 用于存储较短的文本信息
10. `:text` - 用于存储较长的文本信息
11. `:time` - 用于存储时间
12. `:timestamp` - 用于存储时间戳



## View

rails使用嵌入式html来在html中嵌入ruby代码。（框架基本大差不差)

文件后缀名为:`html.erb`。

> 惯例就是，视图名与控制器中的方法名相同，rails会自动寻找控制器中和方法同名的视图文件，并进行渲染返回。

使用`<%  %>`和 `<%= %>`来嵌入代码，两者的不同就是后者会在视图中输出结果，而前者不会。

### render与Not Repeat Yourself

视图中，使用render来渲染partial视图文件，也就是代码复用。

rails中使用下划线+名字的方式来命名partial视图文件。

对于`<%= render article %>`就得有个`_article.html.erb`的partial视图文件。

除了render外还有一些别的特殊的方法，包括：`redirect_to`,`send_data`和`send_file`等方法。

用的多的有：`link_to`,`button_to`这些。这两个的区别在于，link_to使用GET方法，而button_to使用POST方法，并且分别生成a和button元素。

用起来像这样：

```erb
<%= linkto "New product",product_new_path %>
```

> 这里的惯例就是后面的target事实上是routes里头的route名字 + _path

### form表单的创建



## Controller

不同模型的controller文件位于app/controller中。

使用`rails g controller <controllername> <..action>`来创建controller，后面不指定生成的方法就默认为生成所有。并且这样会自动生成相应视图，在路由中设置。

> 惯例就是：controllername是<modelname>的复数形式,
>
> 如果模型名为Article，那么controller名为Articles. 

Controller中可以包含任何自定义方法，不过推荐使用rails自带的show这些。

使用`rails destroy controller <name>`来删除生成的控制器。



---

---

可以生成脚手架`rails g scaffold <name> <..arrtibute：type>`来生成对应的MVC以及测试文件。

## 数据库console

rails有`rails console`和 `rails dbconsole`两种console模式，分别代表着编程式的命令行和传统数据库的命令行。

在`rails  dbconsole`创建的console中，使用sqlite的语法对数据库进行操作，在`rails console`中编写代码来对数据库进行操作。

## test

ROR包含测试，在项目根目录使用`rails test`进行测试。

如果出现数据库未migrate的错误，需要`rails db:migrate RAILS_ENV=test`来将数据迁移到test的数据库种。

test会对MVC进行单元测试。

## 表单

在view中使用form_with来构建表单。

类似于：

```erb
<%= form_with(model: [@article,Comment.new]) do |f|  %>
  <p>
    <%= f.label :content, "New Comment" %><br>
    <%= f.text_area :content, :rows => 5 %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>
```

其中`model`参数，指定了使用的资源，`[]`内表示model的嵌套关系，rails会自动根据这些来选择url，比如这里就会先择articles/<id\>/comments/new。

通过form_with来创建表单构建器f, 使用f来生成一些元素，比如f.label 以及f.text_area,总共可以生成：

`text_field`：生成一个文本输入框。

`password_field`：生成一个密码输入框。

`text_area`：生成一个文本区域。

`check_box`：生成一个复选框。

`radio_button`：生成一个单选按钮。

`select`：生成一个下拉选择框。

`file_field`：生成一个文件上传框。

`hidden_field`：生成一个隐藏字段，用户看不到这个字段，但它会随着表单提交。

`label`：生成一个标签。

`submit`：生成一个提交按钮。

### rails中的ajax

## 路由

所有的路由规则都在`config/route.rb`中，使用rails定义的DSL语言进行申明。rails将Http请求映射到controller的action上，常见的操作有：

```ruby
get 'welcome', to: 'welcome#index'
```

（简单路由）将get请求，url后缀为welcome映射到welcome控制器的index方法。

```ruby
resources :articles
```

（资源路由）会自动生成RESTful路由，包含CRUD操作。

```ruby
resources :articles do
	resources :comments
end
```

(生成嵌套路由)，也就是说后者的路由依附于前者。对于上述来说就是`/articles/<a_id>/comments/<c_id>`

```ruby
get 'login', to: "sessions#new",as 'login'
```

(命名路由)，使用`as`关键字进行命名.



### devise与用户验证

devise插件被rails用来进行用户验证机制。

在Gemfile中增加对devise的依赖，bundle install进行安装。

使用`rails g devise:install`用于安装和设置devise（？）。

它会弹出一些可能需要手工设置的地方。

然后使用`rails g devise <name>`，来创建一个Devise模型，使用`rails db:migrate`来进行迁移。

使用`rails g devise:view`来将devise使用的视图copy到项目中（app/views/devise），这样就可以对其进行自定义修改。

## 使用自定义样式和脚本

这两个都在`/app/assets`中，在对应的文件夹创建对应的文件，