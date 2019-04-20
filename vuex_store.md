# 使用 Vuex 来完成我们的数据全局交互。(通俗版教程)


## Vuex是什么
官方文档：Vuex 是一个专为Vue.js 应用程序开发的状态管理模式。 它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

## 为什么使用Vuex

即：Vuex 是为了保存组件之间共享数据而诞生的，如果组件之间有要共享的数据，可以直接挂载到 Vuex 中，而不必通过父子组件之间传值了，如果组件之间的数据不需要，这些不需要共享的私有数据，没有必要放到 Vuex 中
注意：

1. 只有共享的数据，才有权利放到 Vuex 中；
2. 组件内部私有的数据，只要放到组件的 data 中即可；
3. props 、 data 和 vuex 的区别：props 是父组件向子组件传值；
data 是组件内部私有数据；vuex 相当于一个全局的共享数据存储区域，就是一个公共数据仓库

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/7.png)



## 最简单的Vuex事例

下载vuex,
npm install --save vuex
在更目录中创建 store.js 然后在 main.js 中引用
为 vuex 创建 store，并配置 state、getters、mutations

```js
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

store.js
```js
import Vue from 'vue'
import Vuex from 'vuex'//注册 vuex 到 vue 中

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
     count: 0
     // 大家可以把 state 想象成组件中的 data ，专门用来存储数据的
     // 如果在组件中，想要访问 store 中的数据，只能通过
      // this.$store.state.*** 来访问

  },
  mutations: {
    // 注意：如果要操作 store 中的 state 值，只能通过调用 mutation
    // 提供的方法，才能操作对应的数据，不推荐直接操作 state 中的数据，
    // 因为万一导致了数据的紊乱，不能快速定位到错误的原因，因为每个组
    // 件都可能有操作数据的方法；
    increment(state) {
      state.count++;
    }
    // 注意：如果组件想要调用 mutations 中的方法，
    // 只能使用 this.$store.commit('方法名')
    subtract(state, obj) {
      // 注意：mutations 的函数参数列表中，最多支持两个参数，其中，
      // 参数1：是 state 状态；参数2：通过 commit 提交过来的参数：
      // 提交过来参数若是多个，可以以数组对象的形式
      state.count -= (obj.c + obj.d);
    }
  },
  getters: {
    // 注意：这里的 getters ，只负责对外提供数据，不负责
    // 修改数据，如果想要修改 state 中的数据，请去找 mutations
    optCount: function (state) {
      return '当前最新的 count 值是：' + state.count;
    }
    // getters 中的方法，和组件中的过滤器比较类似，因为过滤器和
    // getters 都没有修改原数据，都是把原数据做了一层包装，提供给了调用者；
    // 其次，getters 也和 computed 比较像，只要 state 中的数据发生了变化，
    // 如果 getters 正好也引用了这个数据，那么就会立即触发 getters 的重新求值；
  },
  actions: {

  }
})
```

两个组件：
amount.vue
```js
<template>
  <div>
      <!--<h3>{{ '当前数量为：'+$store.state.count }}</h3>-->
      <h3>{{ $store.getters.optCount }}</h3>

  </div>
</template>
```


counter.vue
```js
<template>
  <div>
      <input type="button" value="减少" @click="remove">
      <input type="button" value="增加" @click="add">
      <br>
      <input type="text" v-model="$store.state.count">
  </div>
</template>
<script>
export default {
  data() {
      return {
          // count: 0
      };
  },
  methods: {
    add() {
      // 虽然可以实现，但不要这么用，后期维护困难
      // 不符合 vuex 的设计理念
      // this.$store.state.count++;
      this.$store.commit('increment');
    },
    remove() {
      this.$store.commit('subtract', {c: 3, d: 2});
    }
  }
};
</script>
```

总结：

1. state 中的数据，不能直接修改，如果想要修改，必须通过 mutations
2. 如果组件想要直接从 state 上获取数据：需要 this.$store.state.***
3. 如果组件，想要修改数据，必须使用 mutations 提供的方法，需要通过 this.$store.commit(‘方法的名称’，唯一的一个参数)
4. 如果 store 中 state 上的数据，在对外提供的时候，需要做一层包装，那么，推荐使用 getters，如果需要使用 getters，则用 this.$store.getters.***


附加：
1.应用场景：商城购物车的商品个数添加
2.映射：

getter
```js
computed: {
// 使用对象展开运算符将 getter 混入 computed 对象中
  ...mapGetters([
    'doneTodosCount',
    'anotherGetter',
    // ...
  ])
}
```

mutation
```js
 methods: {
  ...mapMutations([
    'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
    // `mapMutations` 也支持载荷：
    'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
  ]),
  ...mapMutations({
    add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
  })
 }
```

// action
```js
  methods: {
  ...mapActions([
    'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`
    // `mapActions` 也支持载荷：
    'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
  ]),
  ...mapActions({
    add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
  })
}
```
## 在Vue组件中实现使用Vuex的关于Todo的demo

    https://github.com/rainyGLC/combat-todo


























