# 总结一下对vue组件通信的理解和使用

    在做组件分离的时候，很有可能会涉及到父子组件之间的通信，  
    那么父子组件间如何通信。父组件调用一个子组件，父组件的属性如何能够传递给子组件使用， 
    子组件里的数据如何能传递父组件？

    下面我们通过一个demo来解答这个问题

## 组件demo

    父组件

```vue
<template>
  <div class="parent">
    我是父组件  
    父组件监听子组件触发的say方法，调用自己的parentSay方法
    通过:msg将父组件的数组传递给子组件
    <children :msg="msg" @say="parentSay"></children>
  </div>
</template>
<script>
  import Children from '@/components/Children.vue'
  export default {
    data() {
      return {
        msg:'hello,children'
      }
    },
    methods: {
      //参数value就是子组件传递出来的数据
      parentSay(value){
        console.log(value) //hello,parent
      }
    },
    //应用子组件
    components:{
      children:Children
    }
  }
</script>
```

    子组件

```vue
<template>
  <div class="hello">
    <div class="children" @click="say">
      我是子组件
      <div>
        父组件对我说:{{msg}}
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      msg: {
        type:String,
        default:()=>{
          return ''
        }
      }
    },
    data() {
      return {
        childrenSay:'hello, parent'
      }
    },
    methods: {
      say(){
        let value =this.childrenSay
        this.$emit('say',value)
      }
    }
  }
</script>
```

    结果为：
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/5.png)

    总结
VUE的父子组件间通信可以总结为一句话
父组件通过prop给子组件下发数据，子组件通过$emit触发事件给父组件发送消息，即prop向下传递，事件向上传递

也可以使用.sync修饰符让子组件去修改更新父组件状态,例如：
在底部导航的右侧有一个删除所有已完成的按钮，点击删除所有已完成的 todo 项目。
使用todos例子中的“删除已完成”


    父组件
    父组件可以监听子组件的事件，并根据需要更新一个本地的新数据，

```vue
<template>
  <div class="todoapp">
    <the-footer
      :todos='todos'
      :todos.sync="todos"/>//更新data中todos的值
  </div>
</template>
<script>
  import TheFooter from '@/components/TheFooter'
  export default {
    data() {
      return {
        todos: [{
        title: '代办一',
        completed: true
      }, {
        title: '代办二',
        completed: false
      }]
      }
    },
    components:'the-footer': TheFooter
  }
</script>
```

    子组件的clearCompleted方法对其赋新值的意图
    this.$emit('updata:todos',todosArr)
    TheFooter子组件

```vue
 <template>
  <footer class="footer">
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

## 通信过程介绍

1. 父组件向子组件传值

  1.1 在父组件中引入需要通信的子组件

  ```vue
  import Children from './Children.vue'
  ```
  1.2  在父组件的components中注册该子组件

  ```vue
  components: {
    children:Children
  }
  ```

  1.3 在父组件的template中使用子组件

  ```vue
  <children></children>
  ```

  1.4 将需要传递给子组件的值通过v－bind(如果传递的是固定值，则不需要v－bind,
  直接属性名，属性值传递即可)

  ```vue
  <clidren :msg="msg" @say=parentSay></clidren>
  //此处的msg则是传递给子组件的值
  ```

  1.5 在对应的子组件中，通过props属性接收传递过来的值

```vue
props: {
  msg: {
      type:String,
      default:()=>{
        return ''
      }
  }
}
```

  1.6 在子组件中使用该值

```vue
<div>
  父组件对我说:{{msg}}
</div>
```

2. 子组件向父组件中传值
  2.1 在Children.vue中，通过触发子组件的方法(这里是自定义的say方法)

```vue
<div class="children" @click="say">我是子组件
  <div>父组件对我说:{{msg}}</div>
</div>
```
  2.2 在子组件中的methods的say中，通过this.$emit(),将事件和参数传递给父组件

```vue
  say(){
    let value =this.childrenSay
    this.$emit('say',value)
  }
  //say是传递给父组件的事件，父组件触发相应这个方法
  //value传递给父组件的参数，在父组件中，可以对这个参数进行详细操作
```
  2.3 在父组件中接受子组件传递的事件say和数据

```vue
  <clidren :msg="msg" @say=parentSay></clidren>
```
  2.4 父组件对接收到的事件和数据做出响应

```vue
parentSay(value){
  console.log(value) //hello,parent
}
```

3. 父组件调用子组件方法
  方法一：
  3.1 在使用子组件时，给子组件加一个ref引用

```vue
<clidren :msg="msg" @say="parentSay" ref="children"></clidren>
```
  3.2 父组件通过this.$refs即可找到该组件，也可以操作子组件的方法

```vue
  this.$refs.children.子组件方法
```
  方法二：
  3.3 通过$children,可以获取到所有子组件的集合

```vue
  this.$children[0].某个方法
```

4. 子组件调用父组件方法
  4.1  通过$parent可以找到子组件，进而调用其方法

```vue
  this.$parent.父组件方法
```

5. 平级组件通信
  同级组件不能直接传值，需要一个中间桥梁，可以先将数据传递给公共的父组件，然后父组件再将数据传递给需要的子组件。
  5.1 定义一个公共文件eventBus.js
  代码很简单(就2句)，只是创建一个空的vue实例

```vue
  import Vue from 'vue'
  export default new Vue()
```
  5.2 在需要通信的同级组件中分别引用eventBus.js文件

```vue
import bus from '@/eventBus.js'
```
  5.3 在children1.vue中，通过$emit降事件和参数传递给children2.vue

```vue
  say(){
    let value =this.childrenSay
    bus.$emit('say',value)
  }
```
  5.4 在children2中，通过$on接收参数和相应事件

```vue
  bus.$on('say',(value)=>{
    //dong something
    })
```

一般大型的项目，推荐使用Vuex来管理组件之间的通信




















