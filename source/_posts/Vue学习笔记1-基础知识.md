---
layout: post
title: Vue学习笔记1-基础知识
date: 2019-08-07 21:12:21
categories:
- Vue.js
tags:
- Vue.js
- 前端
---
- **<script src="https://cdn.jsdelivr.net/npm/vue@2.5.17/dist/vue.js"></script>**
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过vue并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

##  1.Vue特点及其与js的关联
### 1.1.Vue的特点
- 遵循MVVM模式
- 编码简洁，体积小，运行效率高，适合移动/PC端
- 它本身只关注UI，可以轻松引入Vue插件或其它第三方库开发。

### 1.2.与其他前端JS框架的关联
- 借鉴angular的**模板**和**数据绑定**技术。
- 借鉴react的**组件化**和**虚拟DOM**技术。

### 1.3.Vue扩展插件
- vue-cli: vue脚手架
- vue-resource(axios): ajax请求
- vue-router: 路由
- vuex: 状态管理
- vue-lazyload: 图片懒加载
- vue-scroller: 页面滑动相关
- mint-ui: 基于vue的UI组件库（移动端）
- element-ui: 基于vue的UI组件库PC端（）

## 2.基础实例(动态显示)
### 2.1.使用Vue
- 引入Vue.js
- 创建Vue对象
el: 指定根element（选择器）
data: 初始化数据（页面可以访问）
- 双向数据绑定: v-model
- 显示数据: {{xxx}}
- 理解Vue的mvvm实现

**实例展示效果:**
![简单实例](https://upload-images.jianshu.io/upload_images/13687958-9a47600250b9f82f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**示例代码:**
``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <script type="text/javascript" src="js/vue.js"></script>
    <div id="app">
        <h1>1.双大括号表达式</h1>
        <input type="text" v-model="username"><br>
        当前时间:<div>{{date}}</div>
        <p>hello {{username}}</p>
        <p v-text="msg"></p>
        <p v-html="msg"></p>

        <!-- <h2>2.指令一:强制数据绑定</h2> -->
        <!-- <img v-bind:src="imgUrl"> -->
        <!--强制数据绑定语法-->
        <!-- <img :src="imgUrl"> -->
        <!--更简洁的强制数据绑定语法-->

        <h2>3.指令二:绑定事件监听</h2>
        <!--传输参数非必需,需要时传入-->
        <button v-on:click="test">test1</button>
        <!--绑定事件监听语法-->
        <button @click="test2(msg)">test2</button>
        <!--更简洁的绑定事件监听语法-->

    </div>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                username: 'wsswss',
                msg: '<a href="http://www.xztywss.top">逍遥子</a>',
                imgUrl: 'https://cn.vuejs.org/images/logo.png',
                date: new Date()
            },
            methods: {
                //所有事件函数都写在这个配置里面
                test() {
                    alert("信鸽")
                },
                test2(content) {
                    alert(content)
                }
            }
        })
    </script>
</body>

</html>
```
- id=app的<div>标签内前两行是用于动态显示的代码，其余为vue其他的一些知识点的练习，读者可先不必了解。
- **注意:** 这里为了展示了Vue的基本使用方法，这是最简单的一种使用方法: 直接在html文件中引入vue.js。后面会介绍其他使用方法。并且，在后面给出的代码中，博主会选择性的将<html>、<head>等标签省略掉。

**vue基本结构**
``` html
<script type="text/javascript">
// Vue是一个vue.js库提供的构建函数
      const app = new Vue({  //配置对象（属性名称必须是指定的一些名称）
          el: '#test',  //element: 选择器字符串   表明将表面哪个元素交给vue管理
          data: {
              name: 'xztywss',
          }
      })
</script>
```
### Js错误注意点
- 一个可能遇到的错误

![一个可能遇到的错误解决方案](https://upload-images.jianshu.io/upload_images/13687958-04c9da4a1bf7eb6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3.计算属性和监视
### 3.1.计算属性
**前端页面预览:**
![前端页面预览](https://upload-images.jianshu.io/upload_images/13687958-5e0e592b61eb82e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 在**姓**和**名**输入框内输入信息，下方的三个输入框会通过**姓**和**名**两个输入框内的内容改变自己的值，但其中它们有的输入框可以通过改变自己的值去影响**姓**和**名**输入框内信息。
- **单向**: 即只能通过改变一端去改变另外一端。
- **双向**: 即改变任何一方，另一方都会跟着改变。

**前端页面代码:**
``` html
<div id="demo">
        姓: <input type="text" placeholder="First Name" v-model='firstName'><br>
        名: <input type="text" placeholder="Last Name" v-model='lastName'><br>
        姓名1(单向): <input type="text" placeholder="Fullt Name1" v-model='fullName1'><br>
        姓名2(单向): <input type="text" placeholder="Fullt Name2" v-model='fullName2'><br>
        姓名3(双向): <input type="text" placeholder="Fullt Name3" v-model='fullName3'><br>
</div>
```

**输入框：姓名1（单向）计算代码**
``` js
computed: {
    // 什么时候执行：初始化显示/相关的data属性数据发生改变
    fullName1() {  //计算属性中的一个方法，方法的返回值作为属性值
        console.log("fullName1()")
        return this.firstName + ' ' + this.lastName
    },
}
```
- **计算属性:** 必须放在**`computed`**块内才会起作用。就像方法函数必须放在**`methods`**那样。

![计算属性](https://upload-images.jianshu.io/upload_images/13687958-73c69d91c733a1f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**输入框：姓名3（双向）计算代码**
``` js
fullName3: {
    // 1.你定义的   2.你没有调用   3.但最终它执行了
    // 2.什么时候调用？  2.用来作什么
    // 回调函数  但需要读取当前属性值时回调， 根据相关的数据计算并返回当前属性的值
    get() {
        return this.firstName + ' ' + this.lastName
    },
    // 回调函数，监视当前属性值的变化，当属性值发生改变时回调，更新相关的属性数据
    set(value) {  // value就是fullName3的最新属性值
        const names = value.split(' ')
        this.firstName = names[0]
        this.lastName = names[1]
    }
}
```
### 3.2.监视
**使用方法1:**
``` js
watch: {  // 配置监视
    firstName: function (value) {  // firstName发生了变化
        console.log(this)  // 就是vm对象
        this.fullName2 = value + ' ' + this.lastName
    },
}
```
**使用方法2**
``` js
vm.$watch('lastName', function (value) {
    this.fullName2 = this.firstName + ' ' + value
})
```

## 4.class与style绑定
- **理解**
在应用中，某个（些）元素的样式是变化的
class/style绑定就是专门用来实现动态样式效果的技术
- **class绑定**：class='xxx'
xxx可以是字符串
xxx可以是对象
xxx可以是数组
- **style绑定**
**:style="{color: activeColor, fontSize: fontSize + 'px'}"**
其中**activeColor/fontSize**是data属性

**页面展示:**
![页面展示](https://upload-images.jianshu.io/upload_images/13687958-38e8a0883caa3cee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**代码示例:**
``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .aClass { color: red;  }
        .bClass { color: blue; }
        .cClass { font-size: 30px; }
    </style>
</head>

<body>
    <div id="demo">
        <h1>1.class绑定: :class='xxx'</h1>
        <p class="cClass" :class="a">xxx是字符串</p>
        <p :class="{aClass: isA, bClass: isB}">xxx是对象</p>
        <p :class="['aClass','cClass']">xxx是数组</p>
        <!--用的少-->

        <h1>2.style绑定</h1>
        <p :style="{color: activeColor, fontSize: fontSize + 'px'}">style测试</p>

        <button @click="update">更新</button>
    </div>
    <script src="js/vue.js"></script>
    <script>
        new Vue({
            el: '#demo',
            data: {
                a: 'aClass',
                isA: true,
                isB: false,
                activeColor: 'red',
                fontSize: 20
            },
            methods: {
                update() {
                    this.a = 'bClass'
                    this.isA = false
                    this.isB = true
                    this.activeColor = 'green'
                    this.fontSize = 30
                }
            }
        })
    </script>
</body>

</html>
```
- 这是一个可以直接使用的实例，想看明白这段代码并不难，你只需要一点时间来琢磨就可以。

## 5.渲染
### 5.1.条件渲染
- **条件渲染指令**
**v-if**
**v-else**
**v-show**
- **比较v-if与v-show**
在需要频繁切换的场景下 v-show较好

**前端代码:**
``` html
<div id="demo">
      <p v-if="ok">成功了</p>
      <p v-else>失败了</p>

      <p v-show="ok">表白成功</p>
      <p v-show="!ok">表白失败</p>

      <button @click="ok=!ok">切换</button>
</div>
```

**js代码:**
``` html
<script>
      new Vue({
          el: '#demo',
          data: {
              ok: false
          }
      })
</script>
```
### 5.2.列表渲染
#### 5.2.1.列表更改、更新与遍历
**前端页面展示:**
![前端页面展示](https://upload-images.jianshu.io/upload_images/13687958-7a7eb9877f910cf2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**前端页面代码:**
``` html
<div id="demo">
    <h2>测试: v-for 遍历数组</h2>
    <ul>
        <li v-for="(p, index) in persons" :key="index">
            {{index}}---{{p.name}}---{{p.age}}
            ---<button @click="deletep(index)">删除</button>
            ---<button @click="updatep(index,{name: 'cat',age: 20})">更新</button>
        </li>
    </ul>

    <h2>测试: v-for 遍历对象</h2>
    <ul>
        <li v-for="(value,key) in persons[1]">
            {{value}}---{{key}}
        </li>
    </ul>

</div>
```
**js代码:**
``` html
    <script>
        //Vue本身只是监视了persons的改变，没有监视数组的改变
        //Vue重写了数组中的一系列改变数组内部数据的方法(先调用原生，更新界面)-->数组内部改变,界面自动变化
        new Vue({
            el: '#demo',
            data: {
                persons: [
                    {name: 'Tom',age: 18},
                    {name: 'Wss',age: 17},
                    {name: 'Bob',age: 16},
                    {name: 'Ros',age: 15},
                ]
            },
            methods: {
                deletep(index) {
                    //删除persons中指定index的p
                    this.persons.splice(index,1)
                },
                updatep(index, newP){
                    //并没有改变persons本身，而是改变了内部的数据,但并没有调用编译方法,vue不会更新页面
                    // this.persons[index] = newP 
                    this.persons.splice(index,1,newP)  //替换
                    //splice可以实现增删改,为 0 时增加
                }
            }
        })
    </script>
```
#### 5.2.2.列表排序与查找
**前端页面展示:**
![列表排序与查找](https://upload-images.jianshu.io/upload_images/13687958-e7089bc009d5a267.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**前端代码:**
``` html
    <div id="test">
        <input type="text" v-model="searchName">
        <ul>
            <li v-for="(p,index) in filterPersons" :key="index">
                {{index}}---{{p.name}}---{{p.age}}
            </li>
        </ul>

        <button @click="setOrderType(1)">年龄升序</button>
        <button @click="setOrderType(2)">年龄降序</button>
        <button @click="setOrderType(0)">原本顺序</button>
    </div>
```
**js代码:**
``` html
    <script>
        new Vue({
            el: '#test',
            data: {
                searchName: '',
                orderType: 0, //0代表原本顺序,1代表升序,2代表降序
                persons: [{
                        name: 'Tom',
                        age: 18
                    },
                    {
                        name: 'Wss',
                        age: 20
                    },
                    {
                        name: 'Bob',
                        age: 17
                    },
                    {
                        name: 'Ros',
                        age: 19
                    },
                ]
            },
            computed: {
                filterPersons() {
                    //取出相关数据
                    const {
                        searchName,
                        persons,
                        orderType
                    } = this
                    //过滤后最终需要显示的数组
                    let fPersons; //let为定义块级作用域，对外部无影响

                    //对persons进行过滤
                    fPersons = persons.filter(p => p.name.indexOf(searchName) != -1)

                    if (orderType != 0) {
                        fPersons.sort(function (p1, p2) { //如果返回负数,p1在前,返回正数,p2在前
                            //1代表升序,2代表降序
                            if (orderType === 2) {
                                return p2.age - p1.age
                            } else {
                                return p1.age - p2.age
                            }
                        })
                    }
                    return fPersons
                }
            },
            methods: {
                setOrderType(orderType) {
                    this.orderType = orderType
                }
            }
        })
    </script>
```

**文集推荐:**
> [Java基础方法集1](https://www.jianshu.com/nb/35411761)
> [Python基础知识完整版](https://www.jianshu.com/nb/35412583)
> [Spring Boot学习笔记](https://www.jianshu.com/nb/35490047)
> [Linux指令进阶](https://www.jianshu.com/nb/35411158)
> [Java高并发编程](https://www.jianshu.com/nb/35701647)
> [SpringMVC基础知识进阶](https://www.jianshu.com/nb/36348245)
> [Mysql基础知识完整版](https://www.jianshu.com/nb/36768953)
> [健康管理系统学习花絮(学习记录)](https://www.jianshu.com/nb/36626677)
> [Node.js基础知识(随手笔记)](https://www.jianshu.com/nb/36852271)
> [MongoDB基础知识](https://www.jianshu.com/nb/36850994)
> [Dubbo学习笔记](https://www.jianshu.com/nb/36474207)
> [Vue学习笔记(随手笔记)](https://www.jianshu.com/nb/35411638)

> 声明：发表此文是出于传递更多信息之目的。若有来源标注错误或侵犯了您的合法权益，请作者持权属证明与本我们(QQ:981086665；邮箱:981086665@qq.com)联系联系，我们将及时更正、删除，谢谢。