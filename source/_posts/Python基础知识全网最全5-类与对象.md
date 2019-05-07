---
title: Python基础知识全网最全5(类与对象)
date: 2019-05-07 15:24:12
categories:
- Python
tags:
- Python
---
## 五、类与对象

### 1. 定义与生成
**定义:**
``` python
class Teacher:              #Python中的类名**约定以大写字母开头**
     age = 20               #属性
     weight = '10'
     def run(def):          #方法
     print('老师正在努力的跑…')
     def sleep(def):
          print('老师正在睡觉…')
```
 **继承:** 
``` python
class Demo(People):       #表明Demo类 *继承* 了People类。
     pass                 #pass表示不做任何事
classDemo(Test1,Test2…)   #可以 *多继承* ,逗号隔开
     pass
```
**生成:** 
``` python
Libai = Teacher()
```
### 2.  语法细节
- 1.**\_\_init__(self,param1,param2…)**: 
构造函数       #self相当于Java里的this

       #创建函数时自动调用,而且参数必须对应

- 2.**私有变量/方法**: 
> 在变量名或函数名前加上"_" 两个下划线即为私有
> 实际上是将变量名改为:   \_类名\_变量名
单下划线在外部仍然可以调用
- 3.**super()**: 用法: super().\_init\_()  #会自动寻找父类方法并传入需要的参数

- 4.**issubclass(class,classinfo)**:
> 如果第一个参数是第二个参数的子类,返回True,否则False
classinfo可以是类对象组成的元组,是其中任何一个类的子类即返回True
任何类都是object的子类

- 5.**isinstance(object,classinfo)**:
> 检查一个实例对象是否属于类。classinfo注意点同上
检查一个对象(object)是否属于classinfo类

- 6.**hasattr(object,name)**:测试一个对象(object)是否有指定的属性(name)(用引号括起来)

- 7.**getattr(object,name)**:如果没有就打印default字符串

- 8.**setattr(object,name,value)**:如果属性不存在,就创建这个属性并赋值

- 9.**delattr(object,name)**:删除对象中指定的属性

- 10.**x = property(get,set,del)**:
> 三个参数分别为获取属性的方法,设置属性的方法,删除属性的方法执行c1.x=18;  c1.x;  del c1.x时会分别调用这三个函数,对里面的函数所控制的参数进行操作

- 11.**\_\_new__(cls[, …])**:当继承一个不可变类型的时候,需要用此方法进行修改
例: 
``` python
class CapStr(str):          #将创建对象是传入的字符串改为全大写
     def _new_(cls,string):
          string = string.upper()
          return str._new_(cls,string)
```
- 12.**\_\_del__(self)**:
> 当对象将要被销毁的时候,对象会自动调用此方法。当没有任何变量引用这个对象的时候,就会调用对象的此方法进行回收

- 13.**__dict__**:以字典的方式返回对象中的属性和属性值     
例:
``` python
demo.__dict__
```
### 3.属性访问(rectangle.py)
``` python
(1) __getattr__(self,name)             # 定义当用户试图获取一个不存在的属性时的行为

(2) __getattribute__(self,name)        #定义当该类的属性被访问时的行为

(3) __setattr__(self,name,value)       #定义当一个属性被设置时的行为

(4) __delattr__(self,name)             #定义一个属性被删除时的行为
```
### 4.描述符(MyProperty.py  temp.py)

- 定义:描述符就是将某种特殊类型的类的实例指派给另一个类的属性

- **\_\_get__(self,instance,owner)**:用于访问属性,他返回属性的值

- **\_\_set__(self,instance,value)**:将在属性分配操作中调用,不返回任何内容

- **\_\_delete__(self,instance)**:控制删除操作,不返回任何内容

### 5.魔法方法

**格式**: \_\_demo__                #这种格式(**前后双下划线**)均为魔法方法

**使用**: 
例:
``` python
class CapStr(str):        #将创建对象是传入的字符串改为全大写
     def _new_(cls,string):
          string = string.upper()
          return str._new_(cls,string)
```
- **(1) 算数运算符**

![image](https://upload-images.jianshu.io/upload_images/13687958-991cd80cbb8764e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **(2) 反运算**

例: 
``` python
class Nint(int):
     def_radd_(self,other):
          return int._sub_(self,other)
```
运行: 
``` python
a = Nint(5)
b =Nint(3)        # b相当于上述公式里的other
```
结果: 
``` python
a + b = 8
1 + b = 2
```
![image](https://upload-images.jianshu.io/upload_images/13687958-0d9b6c2ba1dddbed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **(3) 增量赋值运算**

![增量赋值运算](https://upload-images.jianshu.io/upload_images/13687958-7a5114b2a6edd7df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **(4) 一元操作符**
![一元操作符](https://upload-images.jianshu.io/upload_images/13687958-209cbe475061c69f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 6.容器类型的协议(CountList.py)

![容器类型的协议](https://upload-images.jianshu.io/upload_images/13687958-bf4566c3be4c5a11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.容器类型的魔法方法

![容器类型的魔法方法](https://upload-images.jianshu.io/upload_images/13687958-d17ca7fb33961e09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

