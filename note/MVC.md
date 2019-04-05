# 什么是MVC模式

      框架是用来软件设计进行分工的，设计模式是小巧的，对具体问题提出解决方案，以提高代码的复用滤，提高耦合性。


## 1、MVC的概念

MVC模式（Model–view–controller）是软件工程中的一种软件架构模式，把软件系统分为三个基本部分：模型（Model）、视图（View）和控制器（Controller）

    什么是MVC呢？M-Model 模型层，V：视图层，C-控制层。下面简述这3者的关系，由于ASP.NET引入了路由机制，我们是通过路由产生动态页面，我们首先由路由表里面的{controller}找到控制器里对应的控制层，路由的第二个参数是{Action}(就是一个Controller内置的public类型的方法，能够接收并处理用户的请求),第三个是可选参数，可以是形式参数，比如id=2
    首先来说一下模型层，模型层是直接和数据互通的，数据库里的数据可以填充到模型层里面。
    控制层：控制层是写逻辑代码的地方，其实控制层和模型层并没有特别区分的地方，因为模型层是控制层里逻辑代码的一部分，我们通过一些方式可以让模型层里面填充数据，然后在控制层里面进行一系列的计算操作，然后控制层再把计算到的结果返回给视图层。
    视图层：把控制层返回到的模型结果，填充到视图里面去，通过一系列的RAZOR语法(Razor 是一种允许您向网页中嵌入基于服务器的代码的标记语法），进行解析生成静态的HTML页面，说白了，就是一个简单的呈现结果。

## 2、MVC的组成

模型（Model） - 程序员编写程序应有的功能（实现算法等等）、数据库专家进行数据管理和数据库设计(可以实现具体的功能)。

控制器（Controller）- 负责转发请求，对请求进行处理。

视图（View） - 界面设计人员进行图形界面设计。


## 3、组件的互动
将应用程序划分为三种组件，模型 - 视图 - 控制器（MVC）设计定义它们之间的相互作用

    模型（Model）用于封装与应用程序的业务逻辑相关的数据以及对数据的处理方法。
    “ Model ”有对数据直接访问的权力，例如对数据库的访问。即封装数据

    视图（View）:即页面模版，起到渲染模版的作用。

    控制器（Controller）起到不同层面间的组织作用，用于控制应用程序的流程。它处理事件并作出响应。“事件”包括用户的行为和数据 Model 上的改变。即操作数据

## 4、现有的MVC框架  

* Backbone（ MV ）
* Angular（ MVVM ）
* Vue （ MVVM ）
* React （ V ）

## 5、 MVC的工作原理

首先通过路由确定控制层和所对应的Action以及Action对应的视图，通过控制层里面的逻辑代码，让模型层里填充数据，再确定视图层所呈现的模型，把ActionResult返回给视图层。然后填充了数据的视图层就会以最终的结果呈现给我们。 一次MVC的生命周期就走完了。


## 6、MVC使用事例
```
// 依次导入 MVC 的三个模块
import { TodoModel } from './model'
import { TodoController } from './controller'
import { view } from './view'

// 实力化各个模块，并连接在一起
const model = new TodoModel();
const controller = new TodoController(model, view)

// 通过手动调用 render 方法，初始化页面
controller.render()
```
学习原文地址：http://www.cnblogs.com/powertoolsteam/p/MVC_knowledge.html