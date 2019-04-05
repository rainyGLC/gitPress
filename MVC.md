# 通过express实现一个简单MVC框架

    Model是定义数据结构和方法,并且和数据库进行交互。
    View是用数据渲染用户看到的视图。
    Controller是处理用户请求,从Model中拿到数据给到view视图。

## 全局安装express-generator，快速创建应用程序框架

    ```
    npm install -g express-generator
    ```

##  初始化项目
  进入桌面使用expressappName，这时候桌面就创建expressApp文件夹，里面包含基本的项目文件
    ```
    cd ~/Desktop && express expressApp && cd expressApp
    ```
## 下载相关依赖，
    ```
    npm install 
    ```
## 启动项目

    ```
    npm start
    ```
    浏览器打开http://localhost:3000/，可以看到 Welcome to Express 

    我们打开 expressApp/package.json 会发现包含有以下依赖，可以点击查看依赖包的作用及使用方法：

    * cookie-parser 用于管理 cookie
    * debug 用于打印调试
    * express Nodejs Web 框架
    * http-errors 网络错误管理
    * jade 视图模版
    * morgan 日志组件

##  目录结构：express-generator 帮助我们创建及配置好项目文件，主要有以下：
      app.js 主文件
      bin/www 启动入口文件
      package.json 依赖包管理文件
      public 静态资源目录
      routes 路由目录
      views 模版目录
![Image text]()

app.js是应用程序的开启点,以下是app.js

```JavaScript
// 各个依赖包
var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var logger = require('morgan');

// 路由文件引用
var indexRouter = require('./routes/index');
var usersRouter = require('./routes/users');

// Express 引用实例化
var app = express();

// 视图模版设置
// 设置视图模版目录，设置视图模版后缀为 jade 文件
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');

// 使用 morgan 日志打印
app.use(logger('dev'));
// 使用对 Post 来的数据 json 格式化
app.use(express.json());
// 使用对 表单提交的数据 进行格式化
app.use(express.urlencoded({ extended: false }));
// 使用 cookie
app.use(cookieParser());
// 设置静态文件地址路径为 public
app.use(express.static(path.join(__dirname, 'public')));

// 使用配置好的路由
app.use('/', indexRouter);
app.use('/users', usersRouter);

// 捕捉404错误
app.use(function(req, res, next) {
  next(createError(404));
});

// 监听异常如果有，立刻返回异常
app.use(function(err, req, res, next) {
  // 设置错误信息
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // 渲染到模版
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;
```
##  使用nunjucks模版代替jade模版

接下来我们使用 nunjucks 并修改模版：

1. 安装 nunjucks 依赖
```
npm install --save nunjucks
```
2. 在app.js中引用nunjucks
```
/ 引入 nunjucks
var nunjucks = require('nunjucks');

...

// 视图模版设置
// 1. 设置视图模版后缀修改为 tpl 文件
// 2. 添加 nunjucks 在 express 中的自动配置
// 3. 注释 设置 views 代码，在 nunjucks 自动配置中有设置
// app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'tpl');
nunjucks.configure('views', {
  autoescape: true,
  express: app,
  watch: true
});
```
3. 在 views 目录中添加 layout.tpl、index.tpl、error.tpl
layout.tpl
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>{{title}}</title>
    {% block css %}
    {% endblock %}
</head>
<body>
    {% block content %}
    {% endblock %}

    {% block js %}
    {% endblock %}
</body>
</html>
```
index.tpl
```html
{% extends './layout.tpl' %}

{% block css %}
<link rel="stylesheet" href="/stylesheets/style.css">
{% endblock %}

{% block content %}
<h1>{{title}}</h1>
<p>Welcome to {{title}} with nunjucks!</p>
{% endblock %}
```
error.tpl
```
{% extends './layout.tpl' %}

{% block css %}
<link rel="stylesheet" href="/stylesheets/style.css">
{% endblock %}

{% block content %}
<h1>{{message}}</h1>
<h2>{{error.status}}</h2>
<pre>{{error.stack}}</pre>
{% endblock %}
```
4. 启动
```
npm start
```

## 配置 favicon.ico 图标
1. 下载serve-favicon 依赖
```
npm install -save serve-favicon
```
2. 把你的favicon.ico文件放在pulic目录下
3. 配置app.js
```JavaScript
// 引入 serve-favicon 依赖包
var favicon = require('serve-favicon');

...

// 设置 favicon.ico 地址
app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
```

## axios
1. 下载 axios
```
npm install -save axios
```
2. 修改路由文件，把 routes/user.js 重命名为 routers/api.js

3. 修改路由配置，因为我们是个接口地址，所以前缀应该为 /api ,而不是 /users/
app.js
```JavaScript
// var usersRouter = require('./routes/users'); 修改为以下
var apiRouter = require('./routes/api');

// app.use('/users', usersRouter); 修改为以下
app.use('/api', apiRouter);
```

## knex
使用 knex 使用 mysql 链接操作数据库，使用前请确保安装以下工具：
* XAMPP 集成环境，用于启动数据库
* navicat - 数据库管理工具 - windows 用户安装
* Sequel Pro - 数据库管理工具 - macOS 用户安装

XMAPP 默认用户名为：root , 密码为空 , host 为 127.0.0.1 。请确保 XAMPP 启动，然后使用数据库管理工具连接成功，接着进行以下步骤：

注意：在 Mac 的新版本XAMPP中，host 未必为 127.0.0.1，可能是 192.168.64.2 ，需要按提示修改文件获取权限，同时设置新的用户和密码。


1. 使用数据库管理工具新建数据库 database 名称为: expressapp 。

2. 进入 expressApp 库，新建用户信息表为 users ，默认有 id 字段，需要再添加以下必要字段：
Field:id
Field:name Type:VARCHAR LENGTH:255 Comment:姓名
Field:email Type:VARCHAR LENGTH:255 Comment:邮箱
Field:password Type:VARCHAR LENGTH:255 Comment:密码

3. 手动设置几个默认值，例如：
name:anan email:1234567890@qq.com password:123456
name:bob email:23456789@qq.com password:123456
name:coco email:34455667@qq.com password:123456

4. 下载项目相关依赖
```
npm install -save knex mysql
```
5.新建配置信息 config.js
config.js
```JavaScript
const configs = {
  mysql: {
    host: '127.0.0.1',
    port: '3306',
    user: 'root',
    password: '',
    database: 'expressapp'
  }
}

module.exports = configs
```
6. 在项目根目录下，新建 .gitignore 避免上传 config.js 及 node_modules 等不需要被上传到 Github 的文件
```JavaScript
.DS_Store
.idea
npm-debug.log
yarn-error.log
node_modules
config.js
```
7. 新建 models/knex.js 数据库配置,初始化配置 knex
```JavaScript
// 引用配置文件
const configs = require('../config');
// 把配置文件中的信息，设置在初始化配置中
module.exports = require('knex')({
  client: 'mysql',
  connection: {
    host: configs.mysql.host,
    port: configs.mysql.port,
    user: configs.mysql.user,
    password: configs.mysql.password,
    database: configs.mysql.database
  }
})
```

8. 以请求路由为／user为例,在index中匹配到/user的路由，从而调用下面的逻辑
```JavaScript
var express = require('express');
var router = express.Router();
router.get(/user,function(req,res,next){
  render(view/user)
    }),
module.exports = router;

```
在调用下面的逻辑之前，在router/index.js中渲染出页面。


9.  新建路由，修改 routes/index.js
```JavaScript
var express = require('express');
var router = express.Router();
var userController = require('./../controllers/user.js');


router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/user', userController.show);

module.exports = router;
```

10. 新建 models/user.js 用户模型,并设置获取所有用户的方法,此为model层
```JavaScript
// 引用 knex
const knex = require('./../models/knex');
// 定义数据库表信息
const TABLE = 'users';

const User = {
  // 获取所有用户的方法
  all: function(){
    // 返回 Promise
    return new Promise((reslove,reject)=>{
      // knex select 操作相关方法
      knex.select().table(TABLE).then( res => {
        reslove(res)
      }).catch( err => {
        reject(err)
      })
    })
  }
}

module.exports = User
```
model层定义数据结构和方法,并且把方法暴露出去,方便调用,比较简单

9. 新建 controllers/user.js 用户控制器，并设置 show 方法,此为controller层
```JavaScript
// 引用用户模版数据
const User = require('./../models/user.js');

const userController = {
  // show 获取用户数据并返回到页面
  show: async function(req,res,next){
    try{
      // 从模型中获取所有用户数据
      const users = await User.all();
      // 把用户数据设置到 res.locals 中
      res.locals.users = users;
      // 渲染到 views 视图目录的 user/show.tpl 路径中。
      res.render('user/show.tpl',res.locals)
    }catch(e){
      res.locals.error = e;
      res.render('error',res.locals)
    }
  },
}

module.exports = userController;
```
controller层获取数据后,调用res.render('user/show.tpl',res.locals);进行渲染数据。
返回给客户端，完成整个请求。


10. 新建视图文件 views/user/show.tpl ，用于显示用户控制的页面，视图中循环语法，请参考 nunjucs 文档。此为 View是视图层
views/user/show.tpl
```html
{% extends './../layout.tpl' %}

{% block css %}
<link rel="stylesheet" href="/stylesheets/style.css">
{% endblock %}

{% block content %}

<div class="page">
  <h1>用户管理</h1>
  <div class="new-user">
    <h2>新建用户</h2>
    <input type="text" name="name" id="new-name" placeholder="请输入用户名">
    <input type="email" name="email" id="new-email" placeholder="请输入邮箱账号">
    <input type="password" name="password" id="new-password" placeholder="请输入密码">
    <button id="new-submit">新建用户</button>
  </div>
  <div class="user-list">
    <h2>用户列表</h2>
    <ul>
      {% for val in users  %}
      <li>
        <span>id: {{val.id}}</span>
        <span>email: {{val.email}}</span>
        <input class="user-name" type="text" name="name" placeholder="姓名" value="{{val.name}}">
        <button class="update-user" data-id="{{val.id}}">修改姓名</button>
        <button class="delete-user" data-id="{{val.id}}">删除用户</button>
      </li>
      {% endfor %}
    </ul>
  </div>
</div>
{% endblock %}
```

12. 重启服务，打开http://localhost:3000/user 你将看到所有用户的信息


## 什么是MVC模式

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