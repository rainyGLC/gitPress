# 什么是MVC模式

      框架是用来软件设计进行分工的，设计模式是小巧的，对具体问题提出解决方案，以提高代码的复用滤，提高耦合性。

## 1、MVC的概念
MVC模式（Model–view–controller）是软件工程中的一种软件架构模式，把软件系统分为三个基本部分：模型（Model）、视图（View）和控制器（Controller）.

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

### 4、现有的MVC框架  

* Backbone（ MV ）
* Angular（ MVVM ）
* Vue （ MVVM ）
* React （ V ）


## 5、MVC使用事例
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