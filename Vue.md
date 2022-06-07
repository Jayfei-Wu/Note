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























































# 2.

# 3.

vue基础

vue-cli

vue-router

vuex

element-ui

vue3

