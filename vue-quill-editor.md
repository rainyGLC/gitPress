# vue中使用vue-quill-editor富文本编辑器，自定义toolbar修改工具栏options
    基于webpack和vue
官方文档：https://quilljs.com/docs/themes/

## 1.npm 安装 vue-quill-editor
    npm install --vue-quill-editor

## 2.在main.js中引入，也可以在组建中单独引入
```JavaScript
import  VueQuillEditor from 'vue-quill-editor'
// require styles 引入样式
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
Vue.use(VueQuillEditor)
```
### 3.在模块中引用
```JavaScript
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

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/vueQuill.png)

如果不需要那么多的工具栏功能要怎么办，通过options来修改
把上面的script部分修改如下：
```JavaScript
<script>
    import { quillEditor } from 'vue-quill-editor'
    export default{
        data(){
            return {
                content:null,
                editorOption:{
                    modules:{
                        toolbar:[
                          ['bold', 'italic', 'underline', 'strike'],        // toggled buttons
                           ['blockquote', 'code-block']
                        ]
                    }
                }
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
这时就会只显示写在toolbar里面的模块
如图所示：
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/vueQuill2.png)

    toolbar工具栏对应功能的模块名大致上分为这几类：
    1. 只需要填写功能名的
      加粗 - bold；
      斜体 - italic
      下划线 - underline
      删除线 - strike
      引用- blockquote
      代码块 - code-block
      公式 - formula
      图片 - image
      视频 - video
      清除字体样式- clean
      这一类的引用 直接['name','name']这种格式就好了

    2.需要有默认值的
      标题 - header  
      [{ 'header': 1 }, { 'header': 2 }] 值字体大小
      列表 - list 
      [{ 'list': 'ordered'}, { 'list': 'bullet' }], 值ordered，bullet
      上标/下标 - script 
       [{ 'script': 'sub'}, { 'script': 'super' }],  值sub，super
      缩进 - indent
      [{ 'indent': '-1'}, { 'indent': '+1' }], 值-1，+1等
      文本方向 - direction
      [{ 'direction': 'rtl' }],    值rtl

这部分如图所示会填写的内容对应提供的值展示功能的图标 如果多个值就显示多个图标

如图所示：
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/vueQuill3.png)

      3.有多个值 以下拉列表形式显示
        大小 - size
        [{ 'size': ['small', false, 'large', 'huge'] }],  
        标题 - header
        [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
这部分显示如图所示 以下拉列形式显示
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/vue-quill4.png)

      4.有系统默认值的功能只需填写一个空数组 就会有出现默认的选项
        颜色 - color
        背景颜色 - background
        字体 - font
        文本对齐 - align
        他们的格式都是
        [{ 'color': [] }, { 'background': [] }], 
        [{ 'font': [] }],
        [{ 'align': [] }]
        这种格式

效果如下 会有默认值出现
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/vueQuil5.png)