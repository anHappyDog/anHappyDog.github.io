---
title: XML
author: lonelywatch
date: 2023-09-9 11:03
categories: [XML]
tags: [XML,ANDROID]
---

# XML与Android

XML是一门可拓展的标记语言，被设计用来传输和存储数据。Android中的布局文件采用XML格式。

其实XML与HTML类似，只是在XML中需要自定义标签，而HTML使用内置标签，并且XML只能用来结构化存储数据，并不能用来展示数据。

- 首行`<?xml version="1.0" encoding="UTF-8"?>`制定了xml的版本和编码方式。

- XML采用树结构，必须要有根节点（标签），通过节点嵌套来组成一颗XML树。
- 关于标签的使用与HTML类似。

- 可以使用 **命名空间**来避免标签名冲突，秩序使用 命名空间:标签名 这样的方式即可避免重复标签名冲突。在使用命名空间时必须通过`xmlns`定义，例如`xmlns:命名空间="URI"`

当然后面大头还是Android

## Android

安卓系统主要由 Linux内核，系统运行时，应用框架，应用程序四部分组成。

安卓程序的四个部分分别为：Activity(活动)，Service(服务),Broadcast  Receiver(广播接收器),Content Provider(内容提供器)

### 项目组成文件

我觉得大概可以分为代码文件，资源文件和控制管理文件这么三部分。

#### AndroidManifest.xml

最主要的控制管理文件，位于项目的根目录下，描述了应用程序的基本信息，并且所有的组件和权限都需要在里面注册声明。

#### 资源文件

随着Android版本的提高，对资源的管理粒度越来越细，对绘画资源，布局，文字这些都有着相应的管理。

#### 代码文件

实现4大组件功能的部分。

### 调试

使用`Debug`进行调试，其中的子函数v,d,i,w,e分别对应着verbose,debug,info,warning,error等级。

### (Activity)活动

活动是一种包含用户界面的组件，用于与用户进行交互。对于一个应用程序，可以包含若干活动。

活动必须要在`AndroidManifest.xml`中注册才能生效。

#### 活动的运行状态

安卓使用**任务**（Task）来管理活动，一个任务就是被存放在栈里的活动的集合，这个栈也被称为**返回栈**。

一个活动总共有四种运行状态：

- 运行状态：活动处于栈顶。
- 暂停状态：活动处于栈的内部，但是活动仍然可见。
- 停止状态：活动处于栈的内部，但是活动不可见。
- 销毁状态：活动从栈中移除。

活动的销毁优先度从上到下递增。

#### 活动的生命周期

Activity类中有7个回调方法，分别为：

- OnCreate() ：创建活动
- OnStart(): 活动由不可见变为可见
- OnResume(): 活动在运行并且和用户交互时调用
- OnPause(): 暂停该活动从而去启动或者恢复其他活动
- OnStop()：在活动完全不可动的时候被调用
- OnDestroy(): 摧毁活动
- OnRestart():重启活动

其中分出3个生存周期：

- 完整声明周期：OnCreate与OnDestroy
- 可见生命周期：OnStart与OnStop
- 前台生存周期：OnResume与OnPause

一个活动是可能在任何阶段被回收的（在内存被耗尽的时候才会回收优先度最低的活动）。在回收后在返回会通过OnCreate创建一个新的活动，原先的临时数据会通过传入OnCreate中的Bundle进行恢复。

#### 启动模式

启动模式可在列表文件中的activity标签中指定`android:launchMode="mode"`，mode为上述四种。

启动模式一共有4中，分别为：`standard`,`singleTop`,`singleTask`,`singleInstance`.

- standard

这是活动的默认启动模式，在该模式下，每次启动都会创建一个新的实例（不在乎栈中是否已经有该活动）

- singleTop

如果创建的活动已经在栈顶，则使用该活动，而非创建新活动。

- singleTask

如果创建的活动已在栈中，则使用该活动，而非创建新活动

- singleInstance

singleInstance会启用一个新的返回栈。如果有多个程序共享一个活动，那么singleInstance会创建一个新的单独的返回栈来管理该活动，这个栈被这些程序所共用。

#### 组件

活动由若干组件(View)构成。View为Android中的组件抽象类。通过实现可以构建自定义组件。

可以通过`getViewById(id)`来获取对应id的组件，对于组件，常用的方法有：

1. `setOnClickListener(OnClickListener listener)`：设置点击事件监听器，当用户点击该视图时触发相应的动作。
2. `setVisibility(int visibility)`：设置视图的可见性，参数可以是`View.VISIBLE`（可见）、`View.INVISIBLE`（不可见但仍占据空间）或`View.GONE`（不可见且不占据空间）。
3. `setEnabled(boolean enabled)`：设置视图是否可用，控制视图是否响应用户的操作。
4. `setBackgroundColor(int color)`：设置视图的背景颜色。
5. `setText(CharSequence text)`：设置视图显示的文本内容。
6. `setTextColor(int color)`：设置视图文本的颜色。
7. `setImageResource(int resId)`：设置ImageView视图显示的图像资源。
8. `setLayoutParams(ViewGroup.LayoutParams params)`：设置视图的布局参数，用于控制视图在父容器中的位置和大小。
9. `requestLayout()`：请求重新进行布局，使得视图的尺寸和位置等属性生效。
10. `invalidate()`：标记视图无效，使其需要重绘。

#### Toast

小的弹出提示，可设置消失时间。

#### 意图

活动之间采用（**意图**）**Intent**进行通信,被**Intent-filter**过滤并处理。

意图分为显式和隐式。意图的一个构造重载为`Intent(Context,class<?>)`，分别表示启动活动的上下文，需要启动的目标活动。

创建后启动后，可以通过Activity类中的`startActivity(Intent)`进行启动。

通过该构造函数构造意图需要显式指明前后活动，所以是显式的，不通过过滤器来启动。

一个另外的意图构造为`Intent(action)`将需要启动的活动中的**action**作为参数，将**category**填入其中（使用addCategory，默认为DEFAULT），使用该意图的话，会根据过滤器启动满足该action和category的活动（有意思的点是如果有多个活动可供启动，会让用户自己选择启动哪一个），这就是隐式的。

隐式意图还可以启动其他程序的活动，意图也可以携带数据在活动间传送往返（在activity的意图过滤器中指定data来指定可响应哪些数据）。

关于意图数据的传送，类似于Bundle，主要通过`putExtra(String name,<type> value)`来进行携带，通过get<Type\>Extra(String name,<Type\> defaultValue )来进行获取。其中Type为Serializable 和 Parcelable用于序列化自定义对象并传递。

对于需要回传（返回给上一个活动的数据），需要通过`startActivityForResult(intent,code)`实现，code为唯一码。

---

#### 活动的回收与状态保存

切换活动需要保存前活动的上下文，恢复后活动的上下文。活动被回收后可以通过`onSaveInstanceState`保存状态，通过传递给onCreate的Bundle参数来恢复。

Bundle变量也可以传递和存储数据，通过`put<Type>(String name,data)`来进行存储，通过`get<Type>(name)`来进行获取。

### UI开发

Android的基本UI使用xml文件作为载体，其中组件View为一个个xml元素。

常见的组件有Button，TextView,EditText，ImageView，ProgressBar，AlertDialog以及ProgressDialog这些。

其中一些常用的属性有：

```xml
android:layout_width=""
//
android:layout_height=""
//
android:text=""
//
android:layout_gravity=""
//
android:layout_weight=""
//
android:id=""
//
```



自定义组件通过实现View来得到。

#### ListView与RecyclerView

ListView就不用了，直接用RecyclerView就行，这两个都是列表组件。昨夜里大概率用到线性和瀑布列表。

#### 布局

总共有LinearLayout（线性布局，通过orientation属性进行行列的选择），RelativeLayout(相对布局),FrameLayout(帧布局),PercentFrameLayout&PercentRelativeLayout(百分比布局，提供组件比例选择)4种。

### 碎片

适用于适配不同的设备。



---

---

### 广播与广播接收器

广播分为**标准广播**和**有序广播**：

- 标准广播：异步无序且传递不可截断。（类似于消息）
- 有序广播：同步且受处理优先级有序被接收，并且可以被阶段。

每个活动可以注册一些广播接收器来决定只接受这些广播，通过实现**广播接收器**来实现。

#### 注册广播

注册广播接收器分为**静态注册**（在AndroidManifest.xml中注册）和**动态注册**(在代码中注册)。

动态注册需要使用`registerReceiver`和`unregisterReceiver`来进行注册和注销接收器。

静态注册在启动后就会被使用（动态注册只能在注册后被使用），但是在后续不再使用。

#### 标准广播

广播通过Intent来进行发送和接收。标准广播通过`sendBroadcast(intent)`(属于Context类中)。

#### 有序广播

通过`sendOrderedBroadcast(intent,permission)`来发送有序广播。通过`abortBroadcast()`来截断广播。

#### 广播接收器

重写BroadcastReceiver类，并且重写onReceive方法。

#### 本地广播

本地广播只能在应用程序内部传播，并且也只能接收本地的广播。

本地广播通过LocalBroadcastManager对广播进行管理。

通过`LocalBroadcastManager.getInstance(this);`来获取实例，通过`localBroadcastManager.sendBrocast(intent);`来发送本地广播。

通过创建`LocalReceiver`来进行接收，通过`LocalBroadcastManager.registerReceiver(localReceiver,intentFilter)`来注册接收器，同样通过`~.unregisterReceiver()`注销接收器。

### 内容提供器

~是用于应用程序间实现数据共享。

Android具有运行时权限，使用相应操作来请求权限。

#### 内容提供器



### 服务

服务就是让一些内容在程序后台运行，并且不依赖任何用户界面。服务依附于创建服务的进程，并且也不会去创建子线程。	

#### 线程

使用线程可以继承Thread类(`new MyThread().start()`)和实现Runnable类（`new Thread(new myThread()).start()`）。在Android中不允许在子线程中更新UI。Android有一套异步处理机制（MessageQueue,Message,Handler,Looper）。在每个线程中有一个MessageQueue，其中Looper中的loop方法启动后会一直尝试从MessageQueue中获取Message，然后交给Handler进行处理。所以子线程所要处理的内容交给了主线程的Handler去做。

也可以通过使用封装好了的AsyncTask进行异步操作。需要实现AsyncTask抽象类。

实现`onPreProgress()`,`onPostExecute()`,`onProgressUpdate()`等函数。启动任务只需要创建该对象并执行execute方法。

#### 服务

需要继承Service类，并且需要在AndroidManifest.xml中注册。

同时需要实现一些回调函数，比如：

`onCreate()`,`onDestroy()`,`onStartCommand(Intent intent, int flags, int startId)`,`onBind(Intent intent)`,`onUnBind(Intent intent)`。

一种使用方法是使用`startService(Intent)`和`stopService(Intent)`开启和关闭服务。此时，只会在最开始调用一次onCreate，onStartCommand在每次启动服务时，都会调用。

这种方法，调用者与服务间没有数据交流，适用于不需要结果的情景。

还有一种使用方法就是使用绑定和解绑服务，这样可以实现双方的数据交流。

总的来说，就是在服务这边使用Binder的实现提供服务的相关操作，在活动这边使用ServiceConnection的实现类进行连接和断开（其中有`onServiceConnected(ComponentName name, IBinder service)`，和`void onServiceDisconnected(ComponentName name)`两个回调函数，通过传入的IBinder 类型service转化为实现类，调用其中定义的相关的服务操作，至于说如何获得的service，这是通过回调函数onBind返回的）。



#### 前台服务与后台服务

前台服务的最大不同就是，前台服务会在状态栏中显示，而后台服务不会，并且后台服务更加容易被回收。

### 数据持久化

可以通过文件，sharedPreferences以及SQLite进行数据的持久化存储。

在Idea中通过模拟器查看文件更新时需要使用**Synthronize**进行同步才能显示结果！

#### 文件

使用`openFileOutput(filename,mode)`写入文件(不允许路径，默认在设备的包的file文件夹下)，其中模式支持`MODE_APPEND`和`MODE_PRIVATE`,分别为追加写入和覆盖写入。

使用`try-with-resources`来操作文件：

```java
        try (FileOutputStream out= openFileOutput("data",Context.MODE_PRIVATE);
             BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(out))
        ){
            EditText editText = findViewById(R.id.edit_text);
            writer.write(editText.getText().toString());
        } catch (IOException e) {
            e.printStackTrace();
        }

```

读取文件类似于：

```java
        try (FileInputStream in = openFileInput("data");
             BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(in))
        ){
            String s;
            while ((s = bufferedReader.readLine()) != null) {
                stringBuilder.append(s);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
```

这里openFileInput返回一个FileInputStream，FileInputStream为IO类，通过将其转化为InputStreamReader输入流，在转化为缓冲流来提升效率。

#### SharedPreferences

保存若干键值对。

可以通过下面这些方法获得SharedPreferences对象：

- Context类中的getSharedPreferences

- Activity类中的getPreferences
- PreferenceManager的getDefaultSharedPreferences()

#### 写SharedPreferences

需要通过SharedPreferences的editor对象，通过对editor进行put<Type\>方法来放入数据，最后通过`apply`（异步，不返回结果）和 `commit`（同步执行，返回Boolean结果）

#### 读SharedPreferences

对SharedPreferences对象使用get<Type\>方法获取数据(参数为key)。

#### SQLite数据库

通过SQLiteOpenHelper来对SQLite进行操作，需要对实现该抽象类才能使用。

其中有两个抽象方法，分别为`onCreate()`和`onUpgrade()` ，用于初始时创建数据库和升级数据库（升级数据库时只需要创建databaseHelper的时候version大于oldversion就行）。

其中有两个实例方法，分别为`getReadableDatabase()`和`getWritableDatabase()`来创建或者打开一个数据库。

其构造方法为`SQLiteOpenHelper(Content,name,cursor,version)`。分别为上下文，数据库名，查询用cursor，以及用于更新的版本。

分别通过**insert()**,**update()**,**delete()**对数据库进行操作，也可以直接通过`excuteSQL` 执行原生SQL语句。

#### 开源库与LitePal

（事实上，对于造轮子这种事情看自己意愿。）

随着Android的发展，有了很多开源的框架。

LitePal是一款开源Android数据库框架。采用ORM（对象关系映射）模式(采用面对对象式的模式来描述关系，很多框架都有)，并且对数据库操作进行封装。

对于开源库的安装与使用，只需要在build.gradle中的depenencies中加入对litePal的引入。

### build.gradle

Gradle是一种现代化的自动化构建工具，用于构建、测试和部署软件项目。

build.gradle是Android项目中的Gradle构建脚本文件，用于构建和打包应用程序，其中包含了应用的很多配置。

### adb（Android Debug Bridge）

adb是Android SDK中的一个调试工具，存放于sdk的platform-tools目录下。

通过输入`adb shell`进入adb的shell界面。

需要注意的只有`su`获取权限，其余大多并未和linux有太大不同。

同时使用`sqlite <database_name>`来打开数据库文件。进入sqlite shell中通过`.table`获取所有表名， `.schema <table_name>`查看表明详细信息，其余和SQL并未有太大不同。





