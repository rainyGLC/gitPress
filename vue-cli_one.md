# 05/14 遇到的问题

1.

```
Access to XMLHttpRequest at 'http://localhost:3000/api/classify' from origin 'http://localhost:8080' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

百度翻译为

```
从'http：// localhost：8080'访问'http：// localhost：3000 / api / classify'的XMLHttpRequest已被CORS策略阻止：没有'Access-Control-Allow-Origin'标题出现在 请求的资源。
```
遇到这个问题需要配置cors实现跨域资源共享
新建cors中间件
middlewares/cors.js
```
const cors = {
  allowAll:function(req,res,next){
    res.header("Access-Control-Allow-Headers", "*");
    res.header("Access-Control-Allow-Origin", req.headers.origin);
    res.header('Access-Control-Allow-Methods', 'PUT,POST,GET,DELETE,OPTIONS');
    res.header('Access-Control-Allow-Credentials', true);
    next();
  }
}

module.exports = cors;
```
之后在app.js中引用cors.js
```
var cors = require('./middlewares/cors')

app.use('/api',cors.allowAll, apiRouter);
```

2. 当在控制台上执行 
```
npm run lint
```
可以自动修复控制台上的报错
