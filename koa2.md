# koa2的ctx.state ctx.body有什么区别啊？

state是用来给中间件保存数据的，而body是最终的输出

body和原来一样没变过，只说说state。
为什么会有state，因为我们会有到很多中间件用于存储某些昨天，比如登陆或者权限验证，在此之前，我们会报错到ctx的一个自定义属性上比如ctx.locals.isLogin，但是我们总是要写类似这样的代码

app.use(async ctx => {
    ctx.locals = ctx.locals || {};
});
现在官方提供了ctx.state用于报错中间件的状态数据。


