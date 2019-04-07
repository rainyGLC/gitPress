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

[案例演示－－todos]

父组件的内容

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/express1.png)

子组件theSection的内容
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/express1.png)


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

     父组件 
      <the-section
      v-on:destroy-del="destroy"
      />
      
     methods:
      destroy (index) {
      this.todos.splice(index, 1)
    },










