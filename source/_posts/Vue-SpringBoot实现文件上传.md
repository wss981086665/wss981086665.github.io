---
layout: post
title: Vue+SpringBoot实现文件上传
date: 2019-08-07 21:13:08
categories:
- Vue.js
tags:
- Vue.js
- 前端
- Spring Boot
---
### Vue+SpringBoot实现文件上传
- 最近由于业务需求，需要实现一个文件上传的上传的功能，可以是图片，也可以是文档，总之文件类型是随意的。下面我将会演示是一个简化版的文件上传示例。由于这里只需要简单的上传一个文档，因此没有实现限制文件大小的代码。
- 由于本人也是菜鸟一个，写此文章也为记录一下实现方法，当然也希望能给一些小伙伴带来帮助。代码写的不是很好，感谢读者指出不足之处。

**前端页面:**
![前端页面](https://upload-images.jianshu.io/upload_images/13687958-6237563da39b2073.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 可以看到，该页面包含**选择文件**以及**输入姓名**两个表单。所以我并没有选择用**form**表单提交数据，而是直接使用Vue的**双向数据绑定**获取的文件信息与输入信息。
- 在提交时，你可能会遇到各种各样的问题，其中包括**`跨域问题`**，跨域问题的简单解决方案会在后端代码中给出。

**前端代码:**
``` html
<template>
    <div>
      <div class="con">
        <div class="tip">选择文件：</div>
        <input class="file" type="file" title="请选择文件" value="请选择文件">
        <div class="tip">输入姓名：</div>
        <input class="inputS" type="text" v-model="name" placeholder="请在此输入姓名">
        <button class="submit" @click="submit">提交</button>
      </div>
    </div>
</template>

<script>
    import axios from 'axios'

    var formData = new FormData() // 声明一个FormData对象
    var formData = new window.FormData() // vue 中使用 window.FormData(),否则会报 'FormData isn't definded'
    export default {
      data() {
        return {
          name: '',
          // file: ''
        }
      },
      methods: {
        submit: function() {
          formData.append('file', document.querySelector('input[type=file]').files[0]) // 'file' 这个名字要和后台获取文件的名字一样;
          formData.append('user',this.name)
          //'userfile'是formData这个对象的键名
          axios({
            url: 'https://****:8081/ensure/uploadwork',   //****: 你的ip地址
            data: formData,
            method: 'post',
            headers: {
              'Content-Type': 'multipart/form-data',
              // 'Access-Control-Allow-Origin': 'http://127.0.0.1:8080'
              //这里是为了解决跨域问题，但是博主并没有用这种方式解决。后面会给出解决方案
            }
          }).then((res) => {
            console.log(res.data);
          }) // 发送请求
        },
      }
    }
</script>

<style scoped>
     // css属性由读者自行实现
</style>
```

**后端代码:**
``` java
@CrossOrigin
@RestController
@RequestMapping("/ensure")
public class MyController {
    //上传文件
    @PostMapping("/uploadwork")
    public String uploadWork(HttpServletRequest request,@RequestParam(value = "file", required = false) MultipartFile file) throws IOException {

        request.setCharacterEncoding("UTF-8");
        String user = request.getParameter("user");

        if(!file.isEmpty()) {
            String fileName = file.getOriginalFilename();
            String path = null;
            String type = fileName.indexOf(".") != -1 ? fileName.substring(fileName.lastIndexOf(".") + 1, fileName.length()) : null;
            if (type != null) {
                if ("DOCX".equals(type.toUpperCase())||"DOC".equals(type.toUpperCase())) {
                    // 项目在容器中实际发布运行的根路径
                    String realPath = request.getSession().getServletContext().getRealPath("/");
                    // 自定义的文件名称
                    String trueFileName = user + "_" + fileName;

                    // 设置存放图片文件的路径
                    path = "/workplace/classwork/" + trueFileName;
                    File dest = new File(path);
                    //判断文件父目录是否存在
                    if (!dest.getParentFile().exists()) {
                        dest.getParentFile().mkdir();
                    }

                    file.transferTo(dest);

                    return trueFileName;
                }else {
                    return "error";
                }
            }else {
                return "error";
            }
        }else {
            return "error";
        }
    }
}
```
- 这段代码看起来可能会有一点难理解，但相信你只需要稍加思索就可以理解啦。
- **`@CrossOrigin`: **为Spring Boot的实现类加上此注解即可轻松解决跨域问题，这是不是要比上面在前端解决跨域问题简单得多呢。

**提交成功:**
![提交成功](https://upload-images.jianshu.io/upload_images/13687958-3d34827482d4a9c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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