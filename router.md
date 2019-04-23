# vue-router如何传递参数

## vue-router传递参数有三种方法
1. 使用name传递
之前一直在配置路由的时候，出现一个name，但是不知道具体有什么用，在路由中它可以用来 
传递参数。在router.js中将路由都写好
接收参数：
在我们需要接收它的页面里添加
```
<p>我是router-name:{{$route.name}}</p>
```
![image](9.png)

比如在APP.vue中接收的，我希望切换每个页面都能看见每个页面各自的参数。
如图：
![image](10.png)
![image](11.png)
但这种方法不太常用，因为我们觉得它不太规整。


2. to来传递

利用router-link 中的to来传参，看语法：
```
<router-link v-bind:to="{name:'xxx',params:{key:value}}"></router-link>
```
* 首先：to需要绑定；
* 传参使用类似与对象的形式；
* name就是我们在配置路由时候取的名字；
* 参数也是采用对象的形式

实际操作一下：

1. 在APP.vue中将to里面的路径改成上面那样

```
<router-link v-bind:to="{name:'about',params:{username:'rainy'}}">关于我</router-link>
```
这里我们注意to的写法，前面加了冒号，因为那是绑定的，传递一个username过去，值为rainy

2. 在about.vue中接收参数
```
<p>传递的名字是：{{$route.params.username}}</p>
```
如图:![image](12.png)

3. 采用url传参
* 修改router.js里的path，这里我们修改about.vue组件

* 在App.vue组件里传递参数
```js
<router-link to="/about/1/about">About</router-link> 
```

* 在about.vue组件里显示我们要展示的内容（接收参数）
```
<a>id是:{{$route.params.id}}</a>
<a>title是:{{$route.params.title}}</a>
```
结果如图：
![iamge](14.png)

## query和params传参(接收参数)$router $route的区别
1. query方式传参和接收参数

```js
//传参: 
this.$router.push({
  path:'/xxx',
  query:{
    id:id
  }
})
  
//接收参数:
this.$route.query.id
```

    注意:传参是this.$router,接收参数是this.$route,这里千万要看清了！！！


## this.$router 和this.$route有何区别？

在控制台打印两者可以很明显的看出两者的一些区别：
![images](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/8.png)

*  $router为VueRouter实例，想要导航到不同URL，则使用$router.push方法

*  $route为当前router跳转对象，里面可以获取name、path、query、params等

2. params方式传参和接收参数

```js
传参: 
this.$router.push({
  name:'xxx',
  params:{
    id:id
  }
})

接收参数:
this.$route.params.id
```

注意:params传参，push里面只能是 name:'xxxx',不能是path:'/xxx',因为params只能用name来引入路由，如果这里写成了path，接收参数页面会是undefined！！！

另外，二者还有点区别，直白的来说query相当于get请求，页面跳转的时候，可以在地址栏看到请求参数，而params相当于post请求，参数不会再地址栏中显示

选择哪个更好
看场景需求，一半

