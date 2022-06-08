# 1. Vue核心

## 1.1 简介

​		动态构建用户界面的**渐进式**JavaScript框架

​		**渐进式：**

​		Vue可以自底向上逐层的应用

​		简单应用：只需一个轻量小巧的核心库

​		复杂应用：可以引入各式各样的Vue插件

​		**特点：**

​		1.采用**组件化**模式，提高代码复用率、且让代码更好维护。

​		2.**声明式**编码，让编码人员无需直接操作DOM，提高开发效率。

​		3.使用虚拟DOM+优秀的Diff算法，尽量复用DOM节点。

​		4.遵循MVVM模式

​		5.编码简洁，体积小，运行效率高，适合移动/PC端开发

​		6.它本身只关注UI，也可以引入其他第三方库开发项目

​		**与其他JS框架的关联：**

​		1.借鉴Angular的模板和数据绑定技术

​		2.借鉴React的组件化和虚拟DOM技术

​		**Vue周边库：**

​		1.Vue-cli：Vue脚手架

​		2.Vue-resource

​		3.axios

​		4.Vue-router：路由

​		5.Vuex：状态管理

​		6.element-UI：基于Vue的UI组件库（PC端）

​		**前置知识：**

​		ES6语法规范、模块化

​		包管理器

​		原型、原型链

​		数组常用方法

​		axios

​		promise



## 1.2 搭建Vue开发环境

![image-20220607144327622](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607144327622.png)

![image-20220607151143969](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220607151143969.png)

浏览器添加Vue Devtools

## 1.3 HelloWorld

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初识vue</title>
    <!--引入vue-->
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
        初识Vue：
        1.想让Vue工作，就必须创建一个Vue实例，且要传入一个配置对象
        2.root容器里的代码依然符合html规范，只不过混入了一些特殊的Vue语法
        3.root容器里的代码被称为【Vue模板】
        4.容器与Vue实例 ，一对一的关系
        5.真实开发中只有一个Vue实例，并且会配合者组件一起使用
        6.{{xxx}}中的xxx要写js表达式，且xxx可以自动读取到data中的所有属性
        7.一旦data中的数据发生改变，那么页面中用到该数据的地方也会自动更新

        注意区分：js表达式 和 js代码(语句)
            1，表达式： 一个表达式会产生一个值，可以放在任何一个需要值的地方
                (1). a
                (2). a+b
                (3). demo(1)
                (4). x === y ? 'a' : 'b'
            2.js代码(语句)
                (1). if(){}
                (2). for(){}
     -->
    <!-- 准备一个容器 -->
    <div id="root">
        <h1>Hello,{{name}}</h1>
    </div>
    <script type="text/javascript" >
        Vue.config.productionTip = false//阻止vue在启动时生成生产提示
        
        //创建Vue实例
        new Vue({//const x = new Vue，const x 可以省略
            el:'#root' ,//el用于指定当前Vue实例为哪个容器服务，值通常为CSS选择器字符串
                        //el:document.getElementById("root") 
            data: {     //data中用于存储数据，数据供el所指定的容器去使用，
                        //值我们暂时先写成一个对象
                name:'World'
            }
        })
    </script>
</body>
</html>
```

## 1.4 模板语法

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>模板语法</title>
    <!-- 引入Vue -->
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
        Vue模板语法有2大类：
        1.插值语法：
            功能：用于解析标签体内容。
            写法：{{xxx}}，xxx是js表达式，且可以直接读取到data中的所有属性。
        2.指令语法：
            功能：用于解析标签（包括：标签属性、标签体内容、绑定事件等）
            举例：v-bind:href="xxx" 或 简写为 :href="xxx", xxx同样要写js表达式。
                    且可以直接读取到data中的所有属性。
            备注：Vue中有很多的指令，且形式都是：v-???, 此处只是拿v-bind举个例子。
     -->

    <!-- 准备好一个容器 -->
    
    <div id="root">
        <h1>插值语法</h1>
        <h3>你好,{{name}}</h3>
        <hr/>
        <h1>指令语法</h1>
        <a v-bind:href="url">点我去百度</a>
        <a :href="url">点我去谷歌</a>   <!-- v-bind:可以简写为 : -->
    </div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示

    new Vue({
        el: '#root',
        data: {
            name : 'world',
            url : 'http://www.baidu.com'
        }
    })
</script>
</html>
```

## 1.5 数据绑定

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>数据绑定</title>
    <!-- 引入Vue -->
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
        Vue中有2种数据绑定的方式
            1.单向绑定（v-bind），数据只能从data流向页面。
            2.双向绑定（v-model），数据不仅能从data流向页面，还可以从页面流向data。
                备注：
                    1.双向绑定一般都应用在表单类元素上（如：input、select等）
                    2.v-model:value 可以简写为 v-model ,因为v-model默认收集的就是value值              
     -->
    <!-- 准备好一个容器 -->
    <div id="root">
        <!-- 普通写法 -->
        单向数据绑定： <input type="text" v-bind:value="name"><br/>
        双向数据绑定： <input type="text" v-model:value="name"><br/>

        <!-- 简单写法 -->
        单向数据绑定： <input type="text" :value="name"><br/>
        双向数据绑定： <input type="text" v-model="name"><br/>


        <!-- 如下代码是错误的，因为v-model只能应用在表单类元素（输入类元素） -->
        <h2 v-model:x="name">Hello</h2>
    </div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止Vue在启动时生成生产提示

    new Vue({
        el:'#root',
        data:{
            name:'张三'
        }
    })
</script>

</html>
```

## 1.6 el与data的两种写法

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>el与data的两种写法</title>
    <!-- 引入Vue -->
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
        data与el的2种写法
            1.el有2种写法
                (1).new Vue时候配置el属性
                (2).先创建Vue实例，稍后再通过vm.$mount('#root')指定el的值
            2.data有2种写法
                (1).对象式
                (2).函数式
                如何选择，目前哪种写法都可以，以后学习到组件时，data必须使用函数式，否则会报错
            3.一个重要的原则
                由Vue管理的函数，一定不要写箭头函数，一旦写了箭头函数，this就不再是Vue实例了
    -->

    <!-- 准备好一个容器 -->
    <div id="root">
        <h1>Hello,{{name}}</h1>
    </div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 Vue 在启动时生成生产提示
    //el的两种写法
    // const v = new Vue({
    //     // el:'#root',第一种写法
    //     data:{
    //         name:'world'
    //     }
    // })
    // console.log(v)

    // setTimeout(() => {   $mount更加灵活
    //      v.$mount('#root') //第二种写法
    // },1000);

    //data的两种写法
    new Vue({
        el:'#root',
        //data的第一种写法，对象式
        // data:{
        //     name:'World'
        // }

        //data的第二种写法，函数式
        data:function(){    //可以直接写成data()
            console.log('@@@',this) //此处的this是Vue实例对象
            return{
                name:'World'
            }
        }
    })
</script>

</html>
```

## 1.7 MVVM模型

​		1.M 模型(Model)：对应data中的数据

​		2.V 视图(View)：模板代码

​		3.VM 视图模型(ViewModel)：Vue实例对象

![image-20220608103018057](https://raw.githubusercontent.com/Jayfei-Wu/NotePic/master/pic/image-20220608103018057.png)

​			观察发现：

​			1.data中所有的属性，最后都出现在了vm身上

​			2.vm身上所有的属性，及Vue原型上所有属性，在vue模板中都可以直接使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>理解MVVM</title>
    <!-- 引入Vue -->
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 准备好一个容器 -->
    <!-- 这个div是 视图 View  -->
    <div id="root">
        <h1>学校名称：{{name}}</h1>
        <h1>学校地址：{{address}}</h1>
    </div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 Vue 在启动时生成生产提示
    
     new Vue({      //new Vue就是ViewModel
         el:'#root',
         data:{
            name:'world',   //data里面是model
            address:'北京'
         }
     })
</script>

</html>
```

## 1.8 数据代理









































# 2.

# 3.

vue基础

vue-cli

vue-router

vuex

element-ui

vue3

