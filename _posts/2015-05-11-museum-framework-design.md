---
title: 网上博物馆底层整体框架设计
layout: post
tags: 
- 架构
- 模式
- WEB
excerpt: 无论是从系统的稳定性，还是安全性来讲，将北京语言文化博物馆网站放到一个成熟框架中来开发是很切合实际的。北京语言文化网上博物馆使用的是Laravel框架，Laravel框架是目前最受欢迎的PHP框架......
---



###一、PHP框架
无论是从系统的稳定性，还是安全性来讲，将北京语言文化博物馆网站放到一个成熟框架中来开发是很切合实际的。
 
北京语言文化网上博物馆使用的是Laravel框架，Laravel框架是目前最受欢迎的PHP框架。
 > Laravel是2014与2015年美国[最受欢迎的PHP框架](http://www.sitepoint.com/best-php-frameworks-2014/ "title")，根据最受欢迎的PHP框架调查显示。
 
###二、 设计模式
MVC是一种组织结构设计模式，也是包括Laravel在内的所有主流框架使用的架构。要想先讲清网站的框架，需要先清楚MVC设计模式。
  
MVC的理念是将应用开发依据逻辑分隔为几个部分，然后各司其职，互有联系。它把应用分为了业务逻辑（即数据部分），用户界面（即视图部分），以及用于管理逻辑，用户输出，并在模型和视图中间起协调作用的控制部分（即控制器）。
>这个模式最初由Trygve Reenskaug在使用Smalltalk-80语言时设计出，一开始它的名字叫做”模型-视图-控制器-编辑器”模式。《设计模式：可重用的面向对象软件元素》一书对MVC设计模式有详细的描述。
  
MVC设计模式中其实也包含一些很经典的设计模式，即观察者模式，当模型发生了变化，视图都会进行相应，更新视图和模型变化联系紧密。
  
  其实MVC最初的设计并不是用来解决WEB中的问题的，那么MVC目前在WEB中是如何运用的呢。
  如下图所示：
  
  ![MVC](http://htmljs.b0.upaiyun.com/uploads/1409725591244-webmvcflow_bacic.png "MVC图示")
  
  
####MVC与WEB开发
先看看在web开发中，MVC的具体含义。
  
* ####Model
	模型代表的是一个应用的的数据，一般一个模型都对应着一张数据表，通过模型来操作数据表，与数据库进行交互。
* ####View
	视图代表用户界面，WEB应用都是以HTML的形式呈现，他们用来展示需要呈现给用户的数据。
* ####Controller
	控制器是用来连接模型和视图的中间层。处理来自用户的请求，然后从模型中取出数据传递给视图。



在网上博物馆的设计上会扩展原有的Model, 对Model需要增加数据验证功能，给具体的Model添加验证器。另外也需要扩展Model的功能，在口传文化项目中一个模型对应三个数据库，分别叫卖视频，叫卖人，叫卖物品，三个数据表中是有联系的，所以也需要在Model中间建立表的联系。Laravel数据库操作使用的是Eloquent ORM，由于ORM内部的完善的设计，会使数据库操作更加方便和安全，不会有SQL注入等安全漏洞的出现。

现在已经讲了MVC的具体含义了。但是有一个特别重要的概念还没有讲到。对于一个大型的应用，会有很多控制器来处理不同的请求。那么控制器是如何判断自己应该处理哪个请求呢。这里就用到了图示中的 *Front Controller* 。

Front Controller实际上是一个路由控制器，用户发来一个请求，请求以路由的形式呈现，类似http://example/post/3这样的形式。然后Front Controller会依据不同的路由请求，反馈给用户不同的控制器。所以首要的问题就是如何构造一个完整的*Routing*（路由）表。

###三、 路由设计

网上博物馆的路由设计采取*REST*（表征性状态传输）设计风格。

>REST是由Roy Fielding博士在2000年他的博士论文中提出来的一种软件架构风格。HTTP连接是无状态的，而REST传输会包含应用的所有状态信息，因此可以大幅降低对HTTP连接的重复请求资源消耗。

REST有以下几个特点：

* 资源由URI来指定。
* 对资源的操作包括获取、创建、修改和删除资源，这些操作正好与HTTP协议提供的GET,POST,DELETE方法对应。
* 通过操作资源的表现形式来操作资源。

下面的表格列出了实现RESTful API时HTTP请求方法的应用：

| 资源 | GET | POST | DELETE |
| ----|----|----|----
| 一组资源的URI，如http://example/post | 列出资源组中每个资源的信息，即列表展示 | 在本组中新增或者修改资源 | 删除整组资源 |
| 单个资源的URI，如http://example/post/3 | 获取指定资源的详细信息 | 在当前组中修改，新增一条资源 | 删除单个资源 |

Laravel对RESTful资源控制器进行了细化，下表列出Laravel中由资源控制器处理的行为：

| 动词 | 路径 | 行为 | 路由名称 |
| ---|---|---|--- |
| GET| /photo | 索引 | photo.index |
| GET| /photo/create | 创建 | photo.create |
| POST| /photo | 保存 | photo.save| 
| GET | /photo/{photo} | 显示 | photo.show |
| GET | /photo/{photo}/edit | 编辑 | photo.edit |
| PUT/PATCH | photo/{photo} |更新 | photo.update |
| DELETE | photo/{photo} |删除 | photo.destroy |


网上博物馆的路由设计完全依据REST的设计模式，将URI与资源联系起来。整体主要分为两个部分：

1. 项目内容展示页的路由设计，采用“baseurl/项目名”与“baseurl/项目名/{id}”这两种路由分别展示列表页和详情页。然后再控制器Controller中再具体写index，show这两个方法。
2.  项目管理页的路由设计，采用“baseurl/admin/项目名”这种路由，将用户请求导引到具体的项目管理Controller，在控制器中添加index, create,store,show,edit,update, destroy方法。 就可以完成，项目管理页面中的增删改了。然后也可以把管理页面的路由作为API给各个项目的研究小组增加和修改数据条目。

###四、视图模板设计
网上博物馆的WEB前端的页面设计，使用的是Blade模板引擎。

网站的视图页面全部放在resources的views目录下。首页就放置到根目录下，然后将header和footer等固定不变的布局全部放到layouts文件夹中，然后admin文件夹放置所有的项目的管理页面，project文件夹放置所有项目的展示页面。 另外auth文件夹放置用户的注册、登录页面。 通过Blade模板引擎，可以轻松的继承布局，扩充项目。 页面内容和样式的更改也变得很简单。

###五、网站发布 
为了防止网站在不同的服务器环境显示有差异，网上博物馆使用Vagrant和Homestead构建一个虚拟机，搭建一个无论在Windows，Linux，Mac等任何系统上都能完美运行网站的环境。 这样一来大大增加了网站的可迁移性。





 













 











