# vue父子组件之间的通信
1. 父组件传递数据给子组件

父组件如何传递给子组件呢？可以通过props属性来实现

父组件：
这里必须用－代替驼峰

    data() {
      return {
        msg:[1,2,3]
      }
    }

    方式一
    子组件通过props来传递参数
    props:[childMsg]

    方式二
    props: { childMsg: Array //这样可以指定传入的类型，如果类型不对，会警告 }

    方式三
    props: { childMsg: { type: Array, default: [0,0,0] //这样可以指定默认的值 } }
    这样呢，就实现了父组件向子组件传递数据.

[案例演示－－使用todos例子]


父组件的内容
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/WechatIMG3.jpeg)


子组件theSection的内容
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/WechatIMG4.jpeg)


注意：

2. 子组件与父组件通信
那么，如果子组件想要改变数据呢？这在vue中是不允许的，因为vue只允许单向数据传递，这时候我们可以通过触发事件来通知父组件改变数据，从而达到改变子组件数据的目的.

例如：

    子组件:
    <button class="destroy" @click="destroy(index)"></button>
  

    给子组件绑定一个事件
    然后，我们可以通过‘destroy’这个点击事件，来去调用this.$emit()方法，这个方法可以传递两个参数
    第一个参数就是绑定在这个子组件上的一个自定义属性，第二个参数就是子组件传递过来的值。

    methods:
    destroy(index) {
      this.$emit('destroy-del',index)
    }

      之后在子组件标签里面把destroy-del事件绑定到父组件destroy(自定义)方法即可
     父组件 
      <the-section
      v-on:destroy-del="destroy"
      />

     methods:
      destroy (index) {
      this.todos.splice(index, 1)
    },

也可以使用.sync修饰符让子组件去修改更新父组件状态

    在脚步导航的右侧有一个删除所有已完成的按钮，点击删除所有已完成的 todo 项目。
    使用todos例子中的“删除已完成”

      TheFooter子组件
      <templete>
       <button class="clear-completed" @click="clearCompleted">删除已完成</button>
      </templete>
      子组件props接收从父组件传递过来的todos，
      <template>

```vue
<template>
  <footer class="footer" v-show="todos.length">
    <span class="todo-count">
      <strong>{{todos.length}}</strong> 总数
    </span>
    <ul class="filters">
      <li v-for="(item,key) in filters" :key="item">
        <!-- @click="changeFilter(key,$event)" :key="item" -->
        <!-- <a href="javascript:;" :class="[filter === key && 'selected']">{{item}}</a> -->
        <router-link :class="[filter === key && 'selected']" :to="'/' + key">{{item}}</router-link>
      </li>
      
    </ul>
    <button class="clear-completed" @click="clearCompleted">删除已完成</button>
  </footer>
</template>

<script>
export default {
  props:{
    todos: {
      type:Array,
      default:function() {
        return []
      }
    },
    filters:{
      type:Object,
      default:function() {
        return {}
      }
    },
    filter:{
      type:String,
      default:'all'
    }
  },
  data() {
    return {
    }
  },
  methods: {
    clearCompleted() {
      let todosArr = this.todos.filter(data => data.completed === false)
      this.$emit('update:todos',todosArr)
    }
  }
}
</script>
```
子组件的clearCompleted方法对其赋新值的意图
    this.$emit('updata:todos',todosArr)

然后在父组件可以监听这个事件，并根据需要更新一个本地的新数据，

```vue
<template>
  <div class="todoapp">
      <the-header :todos.sync="todos" />
    <the-section 
      :todos="todos"
      :filter="filter"
      v-on:toggle-all="toggleAll"
      v-on:destroy-del="destroy"
    />
    <the-footer
      :todos='todos'
      :filters="filters"
      :filter="filter"
      :todos.sync="todos"
    />
  </div>
</template>
```

如果出现了这个问题：

```
vue.runtime.esm.js?2b0e:619 [Vue warn]: Avoid mutating a prop directly since the value will be overwritten whenever the parent component re-renders. Instead, use a data or computed property based on the prop's value. Prop being mutated: "todos"
```
是因为子组件对父组件传递过来的props进行了赋值，使用.sync修改只有更新父组件的功能，不能改变父组件的值




    





