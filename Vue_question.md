# 关于Vue的一些问题纪录

## 环境：vue的安装方式使用vue-cli命令行自动生成一个项目

已知知识：

    vue单文件将组件写在了一个.vue后缀的文件中，有三部分<template> <script> css样式，在该文件中使用ES6模块的export导出这个组件的选项，提供其他组件复用，最后使用webpack打包成一个正常的html+js的文件。

问题：
* 在.vue文件中通过<template>标签直接写组件模板，在当前的.vue文件中存在一个导出模块的对象：export default {需要返回的组件选项}, 在父组件中导入这个选项并在new Vue实例中局部注册的使用上面导入的子组件组件选项，那么这个子组件的template选项去哪里了，webpack会自动把.vue中的<template>加上吗，这点和直接写在html中的官方例子有很大的出入


* 在构建的项目中存在一个入口js，main.js里面有段如下的代码，代码中提供了一个el标签选项表示挂载到存在的dom元素中那么这个存在的dom元素在哪里，在App.vue中我找到这个<div id='app'>,也在webpack提供的index.html模板中找到这个<div id='app'>那么这个el是指App.vue上的dom还是由webpack的提供的index.html模板中的id='app'的？

```
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```
这里el指的是webpack提供的index.html模板的dom id


* 还是第二点上面的代码问题，因为在vue实例中存在一个template,它会代替当前vue实例中el选项所挂载的dom元素，但是vue又不得不去写上这个el并要求存在这个id=app的元素，这两个相同的id但是不同的元素会冲突吗？

webpack会将在配置文件配置的主入口的main.js和其他一些css文件打包成一个
[name].js并生成一个index.html的文件，这个index.html文件会引用生成的
[name].js，这时候index.html的<div id='app'>和[name].js中vue实例表示的组件<div> id='app'>分开成两个文件，不会出现编译错误，之前写在同一个html中所以会引发编译错误

* 在.vue中导入公共样式的方法

1. 在入口js文件main.js中通过import静态引入需要的公共样式
2. 在webpack提供的模板index.html中引入(和传统的引用方式一致）
3. 在app.vue中引入<style>@import 'global.css'; /引入公共样式/</style>


* vue中引入自定义的js文件

vue中不支持module.default = {} 和import的混用，所以在自定义的js文件中采用exprot或者export default这种ES6的模块定义。比如我在我的Util的js文件中定义了一个格式化时间的方法，然后需要导出这个方法：function formatDate (date, fmt) export {formatDate} 导出有默认导出和不默认两种，这里不默认导出在vue文件中引入需要注意一点，必须要用{}花括号解构导出的内容，如import {formatDate} from '../util/Util.js' export中有几个key那么在import后面的花括号里面就可选几个key，
调用通过formatDate（）来直接引用其中导出的方法.function formatDate (date, fmt) export default {formatDateOther} 这里是默认导出，那么在import中引用就不需要包括花括号，import defaultOption, {formatDate} from '../util/Util.js' 这里defaultOption代表的是整个默认导出的对象 {formatDateOther}调用通过defaultOption.formatDateOther()进行调用













