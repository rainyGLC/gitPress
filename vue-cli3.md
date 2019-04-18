# vue-cli2与vue-cli3区别

      CLI 2和CLI 3第一个区别是npm包的包名，CLI 3并没有沿用CLI 2的vue-cli，而是另起为@vue/cli。创建项目方面也发生了变化，CLI 2 可以选择根据模板初始化项目，而CLI 3并未重新开发模板，
      如果开发者想要像CLI 2一样使用模板初始化项目，可全局安装一个桥接工具@vue/cli-init。  

      CLI 2和CLI 3第二个区别是: 生成的vue项目目录的命名改变，同时生成的目录结构，
      CLI 3也隐藏了webpack配置文件

## 创建一个项目
接下来让我们通过安装CLI、创建项目到整个项目介绍，来大致了解两者的区别吧    
1、CLI 2 全局安装并创建项目
```git
  npm install -g vue-cli
  # 或者
  npm install -g vue-cli@x.x.x // 指定安装某一版本

  vue init <template-name> <project-name>
  # 例如：vue init webpack vue-test
```
注意：这里的CLI 2是2.9.6。

    <template-name>：表示模板名称，可以通过vue list查看可用的模板，在这里官方提供了6种模板，分别为:  
    * browserify——一个全面的Browserify + vueify 的模板，运行起来带有热重载，保存时 lint 校验，单元测试。
    * browserify-simple——一个简单Browserify + vueify的模板，不包含其他功能，让你快速的搭建vue的开发环境。
    * pwa——一个基于webpack模板的渐进式的网页应用程序模板。
    * simple——一个最简单的单页应用模板
    * webpack——一个全面的webpack + vue-loader的模板，运行起来带有热重载，保存时 lint 校验和CSS扩展。
    * webpack-simple——一个简单webpack + vue-loader的模板，能让你快速搭建一个vue的开发环境。


2、CLI 3全局安装并创建一个项目
```git
  npm install -g @vue/cli
  # 或者
  yarn global add @vue/cli
  # 创建项目
  vue create <project-name>
  # 例如：vue create vue-3.0-demo
  # 或
  # 使用init 初始化项目
  npm install -g @vue/cli-init
  # `vue init` 的运行效果将会跟 `vue-cli@2.x` 相同
  vue init webpack vue-3.0-demo
```

    当我们在git bash用CLI 3的方式创建项目，输入vue create vue-3.0-demo命令后,
    首先你得选取一个 preset。选择默认的设置可以快速创建一个新项目的原型，而手动设置
    则提供了更多的选择。你是选择默认配置，还是手动选择特性呢？”


![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/1.png)

  创建时建议选择手动去勾选配置，带*建议勾选
  1. (*)Babel （转换es6）
  2. (*)router （路由）
  3. (*)vuex
  4. (*)less css 预处理器
  5. linter / formatter （一个语法规则和代码风格的检查工具，可以检测出你代码中潜在的问题，可以保证写出语法正确和风格统一的代码），lint有两种检查时机，一是用户保存文件的时候，二是用户提交文件到git的时候。选择Lint on save。

  ![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/2.png)

    * ESLint with error prevention only——只检测错误
    * ESLint + Airbnb config——独角兽公司的Airbnb，有人评价说“这是一份最合理的JavaScript编码规范”，它几乎涵盖了JavaScript的各个方面。
    * ESLint + Standard config——standardJs一份强大的JavaScript编码规范，自带linter和自动代码纠正。没有配置。自动格式化代码。可以在编码早期发现规范问题和低级错误。
    * ESLint + Prettier—— Prettier 作为代码格式化工具，能够统一整个团队的代码风格。

  6. 把Babel、ESLint等配置信息全放在package.json文件里呢，还是单独文件管理？ => 单独管理。
      (根据文件名就知道这是谁的配置，方便维护)

## CLI 2 的项目结构

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/3.png)

  对于CLI 2这个项目结构，主要的也是最重要的在于bulid和config者两个目录。  
  bulid是项目构建的相关代码，config是项目开发环境配置。

    接下来就先从webpack.base.conf.js开始依次介绍build和config两个目录下的相关功能。

###  webpack.base.conf.js

webpack.base.conf.js是webpack的基础配置，是dev和prod的公共配置文件。
  ```js
  const path = require('path')
  const utils = require('./utils')
  const config = require('../config')
  const vueLoaderConfig = require('./vue-loader.conf')
  ```
  * path——该模块提供了一些用于处理文件路径的小工具
  * utils——给整个CLI提供方法
  * config——开发环境的配置
  * vueLoaderConfig——分析是否是生产环境，然后将根据不同的环境来加载配置功能

  在这个文件里一共实现了两个方法。一是合并path路径的，另一个是创建Eslint的Rules。而剩余部分就是webpack的基础配置，这里简化了webpack结构，简化的结果其实就是webpack的一个骨架，如果在配置上遇到问题，可去webpack查证。

  ```js
    ...
  module.exports = {
    entry: {}, // 编译入口文件
    output: {}, // 编译输出路径
    resolve: {}, // 一些解决方案配置
    module: {
      // 不同类型文件加载器配置
      rules: []
    },
    ...
    // 这些选项用于配置polyfill或mock某些node.js全局变量和模块。
    // 这可以使最初为nodejs编写的代码可以在浏览器端运行
    node: {...}
  }
  ```
  关于path有兴趣的可前往node学习，接下来重点介绍下utils.js，config和vue-loader.conf

### utils.js
  tils.js文件中总共实现了4个方法：assetsPath、cssLoaders、styleLoaders、createNotifierCallback。
  * assetsPath——返回不同环境下的static目录位置
  * cssLoaders——为不同的css预处理器提供一个统一的生成方式
  * styleLoaders——为那些独立的style文件创建加载器配置
  * createNotifierCallback——以类似浏览器的通知的形式展示信息

### config
config关键文件是index.js。这个文件是开发环境和生产环境的基本配置。在这个文件里开发者可在dev设置开发环境的静态路径、本地服务器配置项、Eslint、SourceMaps和代理，也可在build设置生产环境是否开启gzip压缩，以及压缩后缀名的设置等。
  ```js
    ...
  module.exports = {
    dev: {...},
    build: {...}
  }
  ```

### vue-loader.conf
  这个文件的内容相对比较少。首先，vue文件中的css loader将在生产环境下把css文件抽取到一个独立的文件中；其次是根据不同的环境，引入不同的source map配置文件；最后设置是否开启缓存破坏。
```js
  'use strict'
  const utils = require('./utils')
  const config = require('../config')
  const isProduction = process.env.NODE_ENV === 'production'
  const sourceMapEnabled = isProduction
    ? config.build.productionSourceMap
    : config.dev.cssSourceMap

  module.exports = {
    loaders: utils.cssLoaders({
      sourceMap: sourceMapEnabled,
      extract: isProduction
    }),
    cssSourceMap: sourceMapEnabled,
    cacheBusting: config.dev.cacheBusting,
    transformToRequire: {
      video: ['src', 'poster'],
      source: 'src',
      img: 'src',
      image: 'xlink:href'
    }
  }
```
  关于webpack公共配置讲完了，接下来我们就一起学习下在dev和prod环境各自的配置吧。

### webpack.dev.conf.js
  这个文件引入了webpack-merge，意在将公共配置文件和dev配置合并。从代码里我们可以发现，dev环境又新增了一些配置项。
  * 给独立的style文件添加了sourceMap功能，有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码。
  * 引入devtool。
  * 配置devServer，包括热部署、代理、启动程序的时候自动在浏览器打开主页面等。
  * 新增一些插件，包括热替换、webpack.NamedModulesPlugin在热加载时直接返回更新文件名、html-webpack-plugin生成html文件等。
  最后一个函数是为了确保启动程序时，如果端口被占用时，会通过portfinder来发布新的端口。
  ```js
    ...
  const devWebpackConfig = merge(baseWebpackConfig, {
    module: { ... },
    devtool: config.dev.devtool,
    devServer: { ... },
    plugins: [ ... ]
  })

  module.exports = new Promise((resolve, reject) => { ... })

```  

### webpack.prod.conf.js

    相比 webpack.dev.conf.js，这个文件多引入了几个依赖，主要是为了压缩CSS和JS。
    在文件配置上多了一个output，将js文件打包成多个chuck，用hash值命名，来解决缓存策略。
    到这里CLI 2的整个配置也就接近尾声了。剩下的还有check-version.js和bulid.js两个文件。

### check-version.js

    这个文件主要是用来检测当前环境中的node和npm版本和我们需要的是否一致的。  

### bulid.js

    这个文件刚开始通过check-versions判断当前的node和npm版本号，
    如果现有的npm或者node的版本比定义的版本低，则生成一段警告。
    接下来，先删除打包目标目录下的文件，再进行打包，直至打包完成。  

    学习了CLI 2的配置,大家都累了吧，接下来我们学习CLI3的配置  

## CLI 3的项目结构

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/4.png)  

    从CLI 3的整个项目结构我们可以发现，这个结构很简单，没有相关的配置文件或复杂的目录结构。CLI 3仅生成构建应用程序所需的文件，让使用者不用关心这些工具的具体配置，从而降低了工具的使用难度。

    其实通过阅读CLI 3的官方文档，你可能已经知道，官方内置了一个CLI服务（@vue/cli-service），作为一个开发环境的依赖，局部安装在@vue/cli创建的项目中。如果你真想修改webpack的相关配置，可在项目的根目录下（和package.json同级）创建一个vue.config.js配置文件，这个文件一旦存在就会被@vue/cli-service自动加载。也可直接使用package.json中的vue字段。

## 启动项目  

1、2.0 启动项目 npm run dev

  即CLI 2启动方式是webpack-dev-server --inline --progress --config build/webpack.dev.conf.js 这里用webpack-dev-server搭一个服务。

  * --inline：启动inline模式来自动刷新页面
  * --progress：显示打包的进度
  * --config build/webpack.dev.conf.js：指定要用的是哪个配置文件

2、3.0 启动项目 npm run serve

  即CLI 3启动方式是vue-cli-service serve
  vue-cli-service就是CLI服务，你可全局搜索一下，位于node_modules\@vue\cli-service\bin  

### vue-cli-service.js

```js
  #!/usr/bin/env node

  const semver = require('semver')
  const { error } = require('@vue/cli-shared-utils')
  const requiredVersion = require('../package.json').engines.node

  ...
  const Service = require('../lib/Service')
  const service = new Service(process.env.VUE_CLI_CONTEXT || process.cwd())

  const rawArgv = process.argv.slice(2)
  const args = require('minimist')(rawArgv)
  const command = args._[0]

  service.run(command, args, rawArgv).catch(err => {
    error(err)
    process.exit(1)
  })
```
  这个文件首先是判断了当前node的版本和vue-cli-service要求的版本是否一致，如果版本太低就得升级node版本。
  紧接着就起了个服务，这个服务是位于lib/Service。  


### Service.js

```js
...
loadUserOptions () {
    // vue.config.js
    let fileConfig, pkgConfig, resolved, resovledFrom
    const configPath = (
      process.env.VUE_CLI_SERVICE_CONFIG_PATH ||
      path.resolve(this.context, 'vue.config.js')
    )
    ...

    // package.vue
    pkgConfig = this.pkg.vue
    ...

    if (fileConfig) {
      if (pkgConfig) {
        ...
      }
      resolved = fileConfig
      resovledFrom = 'vue.config.js'
    } else if (pkgConfig) {
      resolved = pkgConfig
      resovledFrom = '"vue" field in package.json'
    } else {
      resolved = this.inlineOptions || {}
      resovledFrom = 'inline options'
    }
    ...
    return resolved
  }
}
...
```

  在loadUserOptions这个函数中，你可以看到官方提到的vue.config.js。
  这个函数主要是加载用户的配置。如果vue.config.js和package.json的vue字段同时存在，
  会忽略package.json的vue字段配置，而选取vue.config.js的配置。

  这里粗略介绍了何处加载了 vue.confg.js 文件，有兴趣可以根据继续深究。经过安装CLI、创建项目到整个项目结构介绍，我们可以大致了解了两者的区别。









