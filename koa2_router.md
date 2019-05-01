# koa2使用路由中间件(抽离出路由)

通过安装koa-router中间件，来控制一下路由；

本篇的版本：注意版本
![images](21.png);

目录结构：

![images](22.png);


安装koa-router中间件
```
npm install --save koa-router
```
新建routes文件夹放置index.js or  api.js(分别为两个路由);

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


在app.js中引用routes路由文件

```
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

```
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










