# vue中使用vue-quill-editor富文本编辑器，自定义toolbar修改工具栏options
    基于webpack和vue

## 1.npm 安装 vue-quill-editor
    npm install --vue-quill-editor

## 2.在main.js中引入，也可以在组建中单独引入
```vue
import  VueQuillEditor from 'vue-quill-editor'
// require styles 引入样式
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
Vue.use(VueQuillEditor)
```
### 3.在模块中引用
```vue
<template>
     <quill-editor 
      v-model="content" 
      ref="myQuillEditor" 
      :options="editorOption" 
      @blur="onEditorBlur($event)" @focus="onEditorFocus($event)"
      @change="onEditorChange($event)">
    </quill-editor>
</template> 
<script>
    import { quillEditor } from 'vue-quill-editor'
    export default{
        data(){
            return {
                content:null,
                editorOption:{}
            }
        },
        methods:{
            onEditorBlur(){//失去焦点事件
            },
            onEditorFocus(){//获得焦点事件
            },
            onEditorChange(){//内容改变事件
            }
        }
    }
</script>   
```
这样引用后就会得到一个编辑器

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/layout.png)


