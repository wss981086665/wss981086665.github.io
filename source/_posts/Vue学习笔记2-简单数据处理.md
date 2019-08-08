---
layout: post
title: Vue学习笔记2-简单数据处理
date: 2019-08-07 21:12:46
categories:
- Vue.js
tags:
- Vue.js
- 前端
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过vue并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 6.事件处理
- 事件处理：简单来说就是监听**鼠标点击**与**键盘**事件并对其做出相应处理，通过触发预先实现好的函数来处理事件。
- 话不多说，上图上代码

**前端页面展示:**
![事件处理](https://upload-images.jianshu.io/upload_images/13687958-991c3898b18a58a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**前端代码:**
``` html
    <div id="example">
        <h2>1.绑定监听</h2>
        <button @click="test1">test1</button>
        <button @click="test2('谢清照')">test2</button>
        <button @click="test3">test3</button>
        <button @click="test4('信鸽', $event)">test4</button>

        <h2>2.事件修饰符</h2>
        <div style="width:200px; height:200px;background:red" @click="test5">
            <div style="width:100px; height:100px;background:skyblue" @click.stop="test6"></div>
            <!--@click.stop的功能：阻止事件冒泡-->
        </div>

        <a href="http://www.baidu.com" @click.prevent="test7">去百度</a>
        <!--@click:prevent"设置默认事件，阻止原有事件-->

        <h2>3.按键修饰符</h2>
        <input type="text" @keyup.13="test8">
        <!--键盘处理事件，处理的键跟在keyup后面-->
        <input type="text" @keyup.enter="test8">

    </div>
```
**js代码:**
``` html
    <script>
        new Vue({
            el: '#example',
            data: {
                test1() {
                    alert("test1")
                },
                test2(msg) {
                    alert(msg)
                },
                test3(event) {
                    alert(event.target.innerHTML)
                },
                test4(number, event) {
                    alert(number + '---' + event.target.innerHTML)
                },

                test5() {
                    alert("out")
                },
                test6() {
                    alert("inner")
                },

                test7() {
                    alert("去百度")
                },

                test8(event) {
                    // if (event.keyCode === 13) { //13:回车键
                    alert(event.target.value + ' ' + event.keyCode)
                    // }       //原始实现方法
                }
            }
        })
    </script>
```
## 7.表单收集
- 表单收集在项目中重要的地位不用多说吧。直接开撸。

**前端页面展示:**
![表单收集](https://upload-images.jianshu.io/upload_images/13687958-337be55762868ac9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**前端代码**
``` html
  <div id="demo">
    <form action="/XXX" @submit.prevent="handleSubmit">
      <!--文本框-->
      <span>用户名:</span>
      <input type="text" v-model="username"><br>

      <!--密码框-->
      <span>密码:</span>
      <input type="password" v-model="password"><br>

      <!--性别-->
      <span>性别:</span>
      <input type="radio" id="female" value="女" v-model="sex">
      <label for="female">女</label>
      <input type="radio" id="male" value="男" v-model="sex">
      <label for="male">男</label><br>

      <!--爱好-->
      <span>爱好:</span>
      <input type="checkbox" id="basket" value="basket" v-model="likes">
      <label for="basket">篮球</label>
      <input type="checkbox" id="foot" value="foot" v-model="likes">
      <label for="foot">足球</label>
      <input type="checkbox" id="pingpang" value="pingpang" v-model="likes">
      <label for="pingpang">乒乓</label><br>

      <!--城市-->
      <span>城市：</span>
      <select v-model="cityId">
        <option value="">未选择</option>
        <option :value="city.id" v-for="(city, index) in allCitys" :key="index">{{city.name}}</option>
      </select><br>
      <span>介绍：</span>
      <textarea rows="10"></textarea>

      <!--提交-->
      <button type="submit">注册</button>
    </form>
  </div>
```
**js代码:**
``` html
<script>
    const vm = new Vue({
        el: "#demo",
        data: {
            username: '',
            password: '',
            sex: '女', //radio初始值
            likes: ['foot'], //foot为初始值
            allCitys: [{
                id: 1,
                name: 'BJ'
            }, {
                id: 2,
                name: 'SH'
            }, {
                id: 3,
                name: 'GD'
            }],
            cityId: '3' //3为初始值
        },
        methods: {
            handleSubmit() {
                console.log(this.username, this.password)
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
