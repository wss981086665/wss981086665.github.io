---
title: Python基础知识全网最全4(函数与文件)
date: 2019-05-07 14:11:56
categories:
- Python
tags:
- Python
---
## 四、函数

- 1.**函数的定义**:  
例1: 
``` python
def MyFirstFunction():
     print('Hello World!')
```
例2:
``` python
def add(num1,num2):
     #函数定义过程中,num1,num2是形参’      #注释1 #文档
     #因为它只是一个形式       //注释2
     return (num1 + num2)  
```
例3:
``` python
def demo1(a = 'hello',b='world'):
     print(a + “->” + b);
     #如果函数调用没有传递参数,就使用默认值
例4.
``` python
def demo2(*param):
     print()
```
- 2.**函数的调用**:  
``` python
add(1,2)    结果: 3  
add(num1=1,num2=2)   结果: 3
add.__doc__          #函数定义过程中,num1,num2是形参’ 
```
## 五、文件

![打开模式](https://upload-images.jianshu.io/upload_images/13687958-8589e5c1f98c887b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 1.open(): 
> open(文件路径,打开模式):以指定模式(上述表格)读取指定文件( \ 斜杠要转义)。
如果要读取的文件与此.py文件在同一目录,那么直接写文件名就行

- 2.文件对象方法

![文件对象方法](https://upload-images.jianshu.io/upload_images/13687958-60d94f07d5a23285.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

