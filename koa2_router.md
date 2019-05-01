# koa2使用路由中间件(抽离出路由)

## koa2使用路由中间件(在入口文件中抽离出路由进行封装)

通过安装koa-router中间件，来控制一下路由；

本篇的版本：注意版本
![images](21.png);

目录结构：

![images](22.png);

安装koa-router中间件
```
npm install --save koa-router
```
新建routes文件夹放置index.js 和 api.js(分别为两个路由);

在index.js中引用koa-router

```js
//index.js
const router = require('koa-router')({
  prefix:'/'
})

const indexController = require('../controllers/index.js')

router.get('/', indexController.indexRender);

module.exports = router
```

在api.js中引用koa-router

```js
//api.js
const apirouter = require('koa-router')({
  prefix:'/api'
})
const apiController = require('../controllers/index.js');

apirouter.get('/',apiController.apiRender)

module.exports = apirouter

```


在app.js中引用routes路由文件

```js
const Koa = require('koa');  //引用koa框架
const router = require('./routes');
const apirouter = require('./routes/api');
const app = new Koa(); //实力化应用
const response = require('./middleware/response');


// 使用路由，监听3000端口
app
  .use(response)
  .use(router.routes())//加载路由中间件
  .use(apirouter.routes())
  .use(router.allowedMethods()) 
  //处理的业务是当所有路由中间件执行完成之后，若ctx.status为空，或者404的时候，丰富response对象的hender头
  .listen(3000)
```

新建controller文件夹，创建index.js文件来放置路由请求

```js
const indexController = {
  indexRender:async(ctx,next) => {
    ctx.body = 'hello koa!'
  },
  apiRender:async(ctx,next)=>{
    ctx.body = 'hello koa and hello world and nodejs'
    ctx.body = {code:200,data:{message:'hello'}}
  }
}
module.exports = indexController;
```
访问localhost:3000,如图：

![images](23.png);


访问localthost:3000/api,如图：
![images](24.png);


## koa2使用路由中间件(在入口文件中使用路由)

1. 编辑app.js

```
const Koa = require('koa')
const Router =  require('koa-router')
const app = new Koa()


// 子路由1
const home = new Router()

home.get('/', async (ctx) => {
    ctx.body = "home pages"
})


// 子路由2
const page = new Router()

page.get('/404', async (ctx) => {
    ctx.body = '404 pages'
})


const login = new Router()

login.get('/', async (ctx) => {
    ctx.body = 'login pages'
})

// 装载所有子路由
let router = new Router()
router.use('/', home.routes(), home.allowedMethods())
router.use('/page', page.routes(), page.allowedMethods())
router.use('/login', login.routes(), login.allowedMethods())

// 加载路由中间件
app.use(router.routes()).use(router.allowedMethods())



app.listen(3000, () => {
    console.log('localhost:3000')
})
```

2. 启动服务，打开浏览器

```
nodemon app.js
```

分别访问：localhost:3000, localhost:3000/login , localhost:3000/page/404,如图：

![images](24.png);


















