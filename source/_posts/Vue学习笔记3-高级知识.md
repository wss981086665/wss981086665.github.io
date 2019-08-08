---
layout: post
title: Vue学习笔记3-高级知识
date: 2019-08-07 21:12:56
categories:
- Vue.js
tags:
- Vue.js
- 前端
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过vue并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 8.生命周期
- 每个Vue实例创建时，都会经历一系列的初始化过程，同时也会调用相应的生命周期钩子，我们可以利用这些钩子，在合适的时机执行我们的业务逻辑。
- 常用的钩子:
**create:** 实例创建完成后调用，此阶段完成了数据的观测等，但尚未挂载，$el还不可用，需要初始化处理一些数据时会比较有用。
**mounted:** el挂载到实例上后调用，一般我们的第一个业务逻辑会在这里开始。
**beforeDestory:** 实例销毁之前。主要解绑一些使用**addEventListener**监听的事件

**示例代码:**
``` html
  <div id="test">
    <button @click="killVM">killvm</button>  <!--click的名称不能含有destory？-->
    <p v-show="isShow">王顺顺</p>
  </div>

  <script type="text/javascript">
    new Vue({
      el: '#test',
      data: {
        isShow: true
      },

      mounted() {                  //初始化之后立即调用(1次)
        this.intervalId = setInterval(() => { //如果用function的话,this就是指window
          this.isShow = !this.isShow
            console.log("---------");
        }, 1000)
      },

      beforeDestroy() {            //死亡时前调用一次
          //清除定时器
          clearInterval(this.intervalId)
      },

      methods: {
          killVM() {
              this.$destroy()       //但是停止之后会有内存泄漏，定时器仍然在执行，需要清除定时器
          }
      }
    })
  </script>
```
## 9.过滤器
- 过滤器主要用于数据的格式化，例如格式化日期。

**前端页面展示:**
![日期格式化](https://upload-images.jianshu.io/upload_images/13687958-7b4a695d017a22c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**前端代码:**
``` html
<div id="test">

    <h2>显示格式化的日期时间</h2>
    <p>{{date}}</p>
    <p>完整版:{{date | dateString}}</p>
    <p>日期:{{date | dateString('YYYY-MM-DD')}}</p>
    <p>时间:{{date | dateString('HH:mm:ss')}}</p>
    <!--此语句会将date的值传输给dateString过滤器处理-->

</div>
```
**js代码:**
``` html
<script>
    //自定义过滤器
    Vue.filter('dateString', function (value, format) {
      return moment(value).format(format || 'YYYY-MM-DD HH:mm:ss')
    })
    // Vue.filter('dateString', function (value, format='YYYY-MM-DD HH:mm:ss') {
    //   return moment(value).format(format)
    // })

    new Vue({
      el: '#test',
      data: {
        date: new Date()
      }
    })
</script>
```
## 10.指令
### 10.1.基础使用
``` html
<!--css样式-->
<style>
    [v-cloak] {
      display: none;
      /*配合实现 v-cloak*/
    }
</style>


<!--前端页面代码-->
<div id="example">
    <p ref="content">xztywss.top</p>
    <button @click="hint">提示</button>

    <!-- <p>{{msg}}</p> -->
    <p v-cloak>{{msg}}</p> //v-cloak 防止闪现 需要配合css实现
</div>


<!--js代码-->
<script>
    new Vue({
        el: '#example',
        data: {
            msg: "wss"
        },
        methods: {
            hint() {
                alert(this.$refs.content.textContent) //textContent = innerhtml
            }
        }
    })
</script>
```
### 10.2.注册指令
- 没有太多需要解释的，这里仅仅给出一段示例代码，想了解更多请百度搜索吧！

**示例代码:**
``` html
<body>

    <!-- 1) 注册全局指令 
        Vue.directive('my-directive',function(el,binding){ 
        el.innerHTML=binding.value.toupperCase() 
    }) 
    2) 注册局部指令 
    directives:{ 
        'my-directive':{
             bind(el,binding){
                 el.innerHTML=binding.value.toupperCase() 
             } 
         } 
    } 
    3) 使用指令 v-my-directive='xxx' -->


    <div id="test1">
        <p v-upper-text="msg1"></p>
        <p v-lower-text="msg1"></p>
    </div>

    <div id="test2">
        <p v-upper-text="msg2"></p>
        <p v-lower-text="msg2"></p>
    </div>

    <script>
        //定义全局指令
        //el 指令属性所在的标签对象
        //binding 包含指令相关信息数据的对象
        Vue.directive('upper-text', function (el, binding) {
            console.log(el, binding)
            el.textContent = binding.value.toUpperCase()
        })

        new Vue({
            el: '#test1',
            data: {
                msg1: 'NBA I Love This Game'
            },

            //局部指令
            directives: {
                // 'lower-text': function (el, binding) {
                //     el.textContent = binding.value.toLowerCase()
                // }
                'lower-text'(el, binding) {
                     el.textContent = binding.value.toLowerCase()
                }
            }
        })

        new Vue({
            el: '#test2',
            data: {
                msg2: 'Just Do It！'
            }
        })
  </script>

</body>
```
## 11.插件
- 合理地使用插件能使我们做项目的过程中达到事半功倍的效果，此处给出一个简单示例

**简单实例:**
``` html
<body>

    <div id="test">
        <p v-my-directive="msg"></p>
    </div>

    <script type="text/javascript">
        //声明使用插件
        Vue.use(MyPlugin) //内部会执行MyPlugin.install(Vue)

        Vue.myGlobalMethod()


        const vm = new Vue({
            el: '#test',
            data: {
                msg: 'I Love You!'
            }
        })
        vm.$myMethod();
    </script>

</body>
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