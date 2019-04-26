# Koa2开发快速入门

## 什么是koa

    Koa 是一个新的 web 框架，由 Express 幕后的原班人马打造， 致力于成为 web 应用和 API开发领域中的一个更小、更富有表现力、更健壮的基石。 通过利用 async 函数，Koa帮你丢弃回调函数，并有力地增强错误处理。 Koa 并没有捆绑任何中间件， 而是提供了一套优雅的方法，帮助您快速而愉快地编写服务端应用程序。
    正因为 Koa 没有捆绑任何中间件，所以我们在开发应用的时候一定要注意自己开发，或者找 Koa 对应的中间件，并且还要注意对应的版本。

### 中间件的执行顺序

koa是中间件是由generator组成，这决定了中间件的执行顺序
Express的中间件是顺序执行，从第一个中间件执行到最后一个中间件，发出响应。
![images](15_2.png)
 
koa是从第一个中间件开始执行，遇到next进入下一个中间件，一直执行到最后一个中间件，在逆序，执行上一个中间件next之后的代码，一直到第一个中间件执行结束才发出响应。
![images](16.png)

### Koa 快速开发例子
Koa 主要的优势是 ES6 + 轻量定制化，以下我们就用 Koa 快速开发一个查询接口。
1. 初始化项目

```

cd ~/Desktop && mkdir koaApp && cd koaApp
```

```
npm init
```

2. 下载相关依赖

＊ koa
＊ koa-router
＊ axios

```
npm install koa koa-router axios --save
```

3. 创建入口主文件 app.js

```
touch app.js
```
app.js

```
// 引入 koa 框架
const Koa = require('koa');
// 引入 路由
const router = require('./routes');
// 实例化应用
const app = new Koa();

// 使用路由，监听3000 端口
app
  .use(router.routes())
  .use(router.allowedMethods())
  .listen(3000)
```

4. 新建路由

routes/index.js
```
const router = require('koa-router')({
  prefix: '/'
})

const indexController = require('../controllers/index.js')

router.get('/', indexController.indexRender)

module.exports = router
```

5. 创建首页控制器 controllers/index.js
```
const indexController = {
  indexRender: async (ctx, next) => {
    ctx.body = 'Hello Koa!'
  } 
}

module.exports = indexController;
```

6. 启动代码,打开http://localhost:3000/，你就可以看到，Hello Koa!

```
nodemon app.js
```



