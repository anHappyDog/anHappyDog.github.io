---
title: React Native
date: 2023-10-19 22:41 +0800
categories: [Android]
tags: [RN,Android]
author: lonelywatch
---

这次Android作业用React-Native做前端，就不用原生Android开发了。Rn使用Js或者是Ts，采用声明式编程，可以像vue那样使用组件式开发。

### Vscode与Android配置

### style

通过`StyleSheet.create({<styles>});`来创建style对象，通过该对象或者其子对象来设置元素的style属性。

也可以通过内联样式来设置。

比如说：

```typescript
const comp1 = function1() {
  	return (
    	<>
        	<Text style={styleTest.test1}>This is really a good day!</Text>
        </>
    );
};


const styleTest = StyleSheet.create({
   test1 :{
       color:"black",
        margin: 10    
	}
});
```

上面style的使用也可以写成`style = {{ color : "black",margin:10 }}`,或者是`style = {[styleSheet.test1,background:"black"]}`,分别代表**内联**，**对象引用**，**混合使用**。

从上面可以看到，元素的很多属性的定义类似于`name={object}`。

### 图片与视频

在Rn中使用`Image`元素来插入图片（使用`source`属性来决定图片的来源，比如说`source={{'uri':"addr"}}`）。Rn并没有内置的Video元素，但是有第三方`react-native-video`库，通过该第三方库引入`Video`元素来实现在rn中展示视频。（需要注意的是，Video使用本地资源时，必须使用`require`来使用资源，否则会无法播放；在使用远程URI时，必须是HTTPS，并且后缀为MP4）

### 列表

可选的元素有：`ScrollerView`,`FlatList`和`SectionList`

### 网络请求

类似于WEB，React Native可以使用其提供的`fetch`来进行网络通信。

使用的话，类似于：

```typescript
fetch("addr",{
    method: "POST",
    headers : {
        ...
    },
       body: JOSN.stringfy({
           ...
       })
});
```

第一个参数addr 为请求地址，第二个参数是可选项，表示请求的设置。

fetch也会返回Promise，使用`async/await`来简化代码。

```typescript
async function fetchSomething() {
    try {
        const response = await fetch("someAddr",
                         	        {
            method : "",
            headers : {},
            body : JSON.stringify({})
        });
        const responseJson = await response.json();
        return responseJson;
    } catch(err) {
        console.log(err);
    }
}
```

其中headers携带请求头，method指定请求方法，body存放数据（这里使用JSON，请求头也应该带有`Content-type : application/json`）。

老一些的方法使用`then-catch`，类似于：

```typescript
return fetch("asd")
	.then(response=>{})
	.catch(err=>{});
```

### 导航







# 大作业设计

一个人的各方面都是有限的，对于这种偏向于应用的作业，应该尽可能地利用前人的成果，坚决拒绝自己造轮子（事实上也确实应该这样，，）。这里可选的主题有：NativeBase，Ui Kitten,React Native Elements，最后我选择了NB，因为它是高度可定制化的(。

 ## 界面设计

采用常见的底部切换界面布局，总共分为四个界面，分别为 活动记录，团体组局，攻略分享，个人中心（需要实现选作任务中的卡路里检测，健身计划，City walk以及活动推荐）。

主题：为了尽可能少而快地优质完成，可以使用别人的主题。



前面三个界面包含了三个必做任务，后面个人中心包含了4个选做任务。

其中运动记录，分别列举出不同运动，然后点击进入运动界面。从开始运动到结束运动。存储其运动时间，运动名。

对于团体组局，可以做一个纵向列表，表示总的大厅，然后可以根据不同的运动名，人数限制，以及时间进行筛查。点击局进入，开局的人具有管理组员的能力。（这里需要包括其他活动）

攻略分享，使用瀑布式布局，图片加少量描述，点击查看详情。敏感词检测使用其他JS插件。



---

活动推荐：根据历史记录，以及天气情况来进行推荐，这里使用别人的算法，以及调用天气api来进行分析。

> 1. 创建一个数据库或者其他形式的数据存储，用来保存学生的活动历史记录和活动爱好。你可以使用云数据库服务，如Firebase、AWS DynamoDB等，也可以使用本地数据库，如SQLite、Realm等。
> 2. 使用一个天气API，如[OpenWeatherMap API](https://openweathermap.org/api)、[WeatherAPI](https://www.weatherapi.com/)等，获取学生所在地的天气情况。
> 3. 创建一个算法，根据学生的活动历史记录、活动爱好和天气情况，为学生推荐适合的团体活动。例如，你可以根据学生过去参加的活动和他们对这些活动的评价，预测他们可能喜欢的活动类型；然后根据天气情况，过滤掉不适合的活动；最后，从剩下的活动中选择一些推荐给学生。
> 4. 在app的界面上显示推荐的活动。你可以创建一个活动列表界面，让学生可以浏览和选择活动。

CITYWALK，这里选择列举出固定的景点，让用户选择若干，最后生成若干路线。这里使用地图api来进行展示。

> 1. 首先，你需要在你的app中创建一个列表，列出所有支持的walk地点。这个列表可以是一个简单的数组，也可以是一个从服务器获取的动态列表。
> 2. 创建一个界面，让用户可以在这个列表中选择他们感兴趣的walk地点。
> 3. 使用一个地图服务API，如[Google Maps API](https://developers.google.com/maps/documentation)或者[百度地图API](http://lbsyun.baidu.com/)等，获取每个地点的经纬度信息。
> 4. 使用地图服务API的路径规划功能，为用户生成一条或者多条包括这些walk地点的路线。你可以根据用户的需求（如路线的总长度、路线的难易程度等）来调整路线的生成策略。
> 5. 在app的界面上显示生成的路线。你可以使用地图服务API提供的地图组件来显示地图，也可以自己创建一个地图界面。
>
> 以下是一个简单的React Native示例，使用了[Google Maps API](https://developers.google.com/maps/documentation)：
>
> 首先，你需要注册一个Google Maps API的账号，并获取API密钥。
>
> 然后，安装`react-native-maps`库来显示地图：

卡路里检测，根据学生输入的食物来识别相关卡路里，提供相关建议。调用别人的插件来进行分析。

> 1. 使用一个图像识别API，如Google Cloud Vision API或者Amazon Rekognition，来识别食物的图片。这些API可以识别图片中的对象，并返回对象的名称。
> 2. 创建一个数据库或者其他形式的数据存储，保存各种食物的卡路里信息。你可以从公开的营养数据库，如USDA National Nutrient Database，获取这些信息。
> 3. 当用户上传食物的图片时，首先使用图像识别API识别图片中的食物，然后在数据库中查找对应的卡路里信息。
> 4. 根据用户的健康信息（如年龄、性别、体重、身高、活动水平等）和食物的卡路里信息，给出相关的健康建议。例如，如果食物的卡路里过高，可以建议用户减少食用量；如果食物含有丰富的蛋白质和纤维，可以鼓励用户多吃这种食物。
> 5. 在app的界面上显示食物的卡路里信息和健康建议。

健身计划，暂时没想好。



## 设计

事实上包含的运动有：篮球，足球，跑步，TD线，排球，羽毛球，乒乓球，台球，游泳，网球，足球，飞盘，健身，其他运动。

记录方式：记录地点，活动时间，

事实上，通过axios访问后端，会自动将携带的json返回，而使用fetch则需要使用json显式地转化为json。

django使用内置的用户表登录验证时，会自动设置session，而在非内置表时，则需要自己对session进行赋值，此时session才会被创建，这样session_key会被携带到cookie中。

可以根据携带的值，以及sessionId来进行用户机制的验证。

TD线

事实上，用户有这么些功能。

头像，查看信息，修改信息，推出登录，我的文章，我的锻炼记录，提交反馈。

这里地图相关功能使用百度地图api。

### 富文本

react-native上很多这方面的插件，只是很多都很老旧没更新了。这里遇到了一个问题就是WebView在已经安装了~-webview后仍然报错元素undefined的情况，这里rn并没有安装后自动添加依赖，所以需要手动安装依赖：

```shell
yarn add react-native-webview
```

然后

```shell
npm install
```

然后就可以使用WebView和富文本了。

#### 图片的选择和插入

这里可以选择`react-native-image-picker`和`react-native-crop-image-picker`两种图片处理。还需要`react-native-fs`进行文件的读取和转码（因为rich-editor只支持base64格式的图片数据，需要使用RNFS readfile转化为base64）。

在文章中插入的图片在提交时被上传到服务端，并且替换掉文章中的图片网址。

### 图片处理

django中有固定处理图片的ImageField，并且能够给出对应的url。建立一个相应的模型就可以了。不过需要注意在django的设置中指定文件的存放文职，`MEDIA_ROOT`和`MEDIA_URL`。

服务器请求转发我使用的是nginx，它好像可以自己处理静态资源，并且最好交由nginx来进行处理。并且django和nginx都对文件的上传大小进行了限制。（超出nginx返回413，请求不会到达django后端；超出django会直接返回413）

在nginx中只需要对配置文件中的location块进行配置：

```z
client_max_body_size 5m; /*5M*/
```

在django中对设置进行配置：

```
DATA_UPLOAD_MAX_MEMORY_SIZE = 10485760  # 10MB
```

### django静态资源收集

如果将django部署到远程服务端，会出现静态资源无法访问的情况，在django设置中指定 `STATIC_URL`和`STATIC_ROOT`并且执行`collectstatic`。

### 模型的外键处理

django的模型外键并非具体的值，而是引用的实例（除了外键并非被参照表的情况下，貌似可以使用具体值？）

处理时间就用dateField，dateTimeField和durationField。

处理图片用ImageField（通过<instance\>.image.url获取url（不包含网址前缀）），处理文件用FileField。

### 模型的操作

也就是增删查改,模型通过其`objects`属性进行操作，比如创建`objects.create`（也可以创建实例并显式调用save，create会自动调用save），<instance\>.delete()表示删除，对instance的属性进行操作来进行查询和修改。

对模型类的objects.filter和objects.get来进行查询。通过其方法`values()`获取其对象的字典表示。

### 前后端通信

前端使用axios连接后端，通过json进行数据交流。

### 服务端https认证

通过Let‘s Encrypt获取各个域名的https证书，会自动配置443端口的nginx设置，其他端口可能需要手动配置证书和http自动跳转https。

### RN钩子函数

用的多的也就`useState`,`createRef`(react),`useCallback`,`useFocusEffect`,`useEffect`这些。

其中useState类似于vue的ref，表示状态变量，而createRef则是对组件的引用，useCallback表示回调函数，常用于useFocusEffect中。useEffect在每次组件重新渲染时都会被调用，而状态变量改变时就会触发重新渲染，可以通过设置来指定触发uesEffect的状态变量。

### django的request

request包含这么些dong