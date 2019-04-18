# 使用 Vuex 来完成我们的数据全局交互。

    下载vuex,
    npm install --save vuex
    在更目录中创建 store.js 然后在 main.js 中引用
    为 vuex 创建 store，并配置 state、getters、mutations

store.js
```vue
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {

  },
  mutations: {

  },
  actions: {

  }
})
```
state:驱动应用的数据源，即存放data数据
view

```vue
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App),
}).$mount('#app')
//通过在根实例中注册 store 选项，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 this.$store 访问到。
``` 


