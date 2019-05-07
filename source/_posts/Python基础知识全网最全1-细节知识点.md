---
title: Python基础知识全网最全1(细节知识点)
date: 2019-05-04 10:27:11
categories:
- Python
tags:
- Python
---
## 1.语法注意点 
- 0.注释: 
``` Python
 1.单行注释: #      
 2.多行注释: 
         '''              """ 
                 或
         '''              """
```
- 1.**dir(__builtins__)**:查看所有的BIF组件
- 2.**help(input)**:查看input的具体信息
- 3.**字符串**:前面加上 r 变成原生字符串，即程序不进行转义;例如str = r”hello”
 - 4 .**逻辑与操作符**: and   or not
 - 5.**布尔值**:  True False 首字母是大写的

- 6.**运算符**:  Python中 10/8 = 1.25   10//8 = 1         2 ** 3 = 8  即2的3次方
![优先级](https://upload-images.jianshu.io/upload_images/13687958-e719b02016c3d11c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 7.**判断语句**:  if和 elif: 相当于我们熟悉的 if 和else if
- 8.**三元操作符**: demo= x if x > y else y  即: 真 if 条件 else 假

- 9.**for循环**: 
```
for demo in arrs
     print(demo)
```
- 10.**arr[1:3]**:  返回数组中下标1到3(不包括三)的元素,即只返回arr[1],arr[2]

- 11.**arr * 3**:将arr拷贝三份,但并不改变自身

- 12.**分片**: 
> arr =arr2[]: 拷贝arr2到arr里,之后不会随着arr2改变而改变，直接复制的方法(arr = arr2)会使arr随着arr2改变而改变
- 13.**元组(tuple)**: 戴上枷锁的数组(数组元素不可改变)
``` Python
元组的创建: arr = (1,2,3,4,5,6)                 #小括号
       ()  (1,) (1,2,3)  1,2,3  以上四种都属于元组  (1)是int类型
元组添加元素(类似于字符串)
       arr = (1,2,3,4)
       arr = arr[:2] + (5,) + arr[2:]
       结果: arr = (1,2,5,3,4)
```
- 14.**全局变量**: 
> 函数内部调用全局变量要声明: **`global count`**;

- 15.**闭包**:
``` python 
def Fun1(x):
      def Fun2(y):
            return x * y;
      return Fun2;

调用: Fun1(x)(y)
```
- 16.**闭包内全局变量**: 
``` python
def Fun1():
     x = 5
     def Fun2():
           nonlocal x                #声明使用的是外函数的变量
           x *= x
           return x
     return Fun2()
```
- 17.**匿名函数(lambda)**: 
``` python
g = lambda x : 2 * x + 1            #冒号前面是参数，后面是语句
相当于: 
      def g(x):
            return 2 * x +1
```
- 18.**递归(recursion)**: 
设置最大递归次数:
``` python
import sys
      sys.setrecursionlimit(100000)
```
- 19.**字典(map)**: 
``` Python
# 创建方式1: 
     demo = {1:"one", 2:"two", 3:"three"}
     # 获取"two": 
           demo[2]
# 创建方式2(dict()):  
     demo = dict((('F',10),('G',20),('H',30)))       # 中间一层括号是元组
     # 相当于: demo = {'F':10, 'G':20', 'H':30 }
# 创建方式3:  
     demo = dict(abc='你好', xyz='优秀')
     # 相当于: demo = { 'abc':'你好', 'xyz':'优秀'}
     # 获取'你好': 
           demo['abc']
     # 添加一组数据: 
           demo['ijk']= '当然'
```
- 20.**迭代器(Fibs.py)**: 

1.**iter(): \_\_iter__()**: 对象调用iter()得到该对象的迭代器
     ***注意**: \_\_是两个下划线，而不是一个下划线*
2.**next(): \_\_next\_\_()**: 调用next()迭代器就会返回下一个值 
例:
``` python
string = "FishC"
     it = iter(string)
     next(it)           #此处返回'F'
     #next()没有元素时会抛出StopIteration异常
```
例:循环输出string:
``` Python
while true:
     try:
           each =next(string)
     exceptStopIteration:
           break
     print(each)
# for in循环是靠iter()迭代机制实现的
```
- 21.**生成器(yield)**: 其实是迭代器的一种实现
例: 
``` Python
def libs():
     a = 0
     b = 0
     while True:
          a,b = b,a + b
          yield a           #返回a并停止
```
- 22.**列表推导式**: 
例: 
```
a = [i for i in range(100) if not(i%2) and i%3]
      #结果: 100以内所有能整出2但不能整除3的数
```
- 23.**字典推导式**: 
例: 
``` python
b = { i : i % 2 == 0 for i in range(10) }
       结果: 0-10以内所有数字与数字是否为偶数的对应关系的字典:
       即: { 0 : True , 1 :False , … , 9 : False }
```
- 24.**集合推导式**: 
例: 
``` python
c = { i for i in [ 1 , 1 , 2 , 3 , 4 , 5 , 6 , 7 , 8 , 3 , 2 , 1 ]}
      结果: { 1 , 2 ,3 , 4 , 5 , 6 , 7 , 8 }   #不会出现重复的数字
```
- 25.**元组推导式**: 生成的是一组迭代器,可以将数据迭代输出

- 26.**路径编码配置**: 
在site-packages文件夹里设置sitecumtomize.py文件,并写入:
``` python
import sys
sys.setdefaultencoding(‘gd2312’)
```
这样python自动安装的时候就会加载此文件,设置默认编码,路径拼写路径过程中就不会出错
## 2.类型转换

- 1.**str->int**   例:
``` Python
a='520'  b = int(a)  即 b = 520    (str即字符串类型)
```
- 2.**int->str**  例:
``` Python
a = 5.99  b = str(a)  即 b = '5.99'
a = 5e19  b = str(a) 即 b = '5e+19'
```
## 3.异常处理

### (1).异常类型总结

![异常类型总结](https://upload-images.jianshu.io/upload_images/13687958-7082ac35dec3552f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![异常类型总结](https://upload-images.jianshu.io/upload_images/13687958-a703b8a15f3377cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### (2).try-except语句
用法:  
``` Python
try:
     检测范围
except Exception[asreason]:
     出现异常(Exception)后的处理代码
```
例1:  
``` Python
try:
     Sum = 1 + '1'
     f = open('我是一个文件.txt')       #此时文件不存在    
except OSError as reason:
     print('文件出错啦T_T\n错误的原因是' + str(reason))
except TypeError as reason:
     print('类型出错啦T_T\n错误的原因是' + str(reason))
```
例2:  
``` Python
try:
     Sum = 1 + '1'
     f = open('我是一个文件.txt')        
except OSErrorTypeError:
     print('出错啦T_T')
```
### (3).try-finally语句
用法:  
``` python
try:
     检测范围
except Exception[asreason]:
     出现异常(Exception)后的处理代码
finally:
     无论如何都会被执行的代码
```
### (4).raise引发一个异常
用法:  
``` python
raise 异常名称
raise 异常名称:提示信息
```
### (5).with语句
用法:  
``` python
with open('我是一个文件.txt') as f:
     for each_line in f:
          print(each_line)
# 如果文件存在,with语句会自动调用f.close()
```
## 4.模块！模块！

- 1.**if \_\_name__ == '\_\_main\_\_'**:

> 如果是运行程序本身就执行条件后的语句
如果是在外部把此程序当成模块使用,则不执行条件后的语句

- 2.**搜索路径**: 最佳存放路径: 'C:\\python\\lib\\site-packages'
添加搜索路径:
``` phthon
import sys
sys.path.append("路径…")
```
- 3.**包(package)**: 
创建包:
>1.创建一个文件夹,用于存放相关的模块,文件的名字即包的名字
> 2.在文件夹中创建一个__init__.py的模块文件,内容可以为空
> 3.将相关的模块放入文件夹中
> 4.导入: import 报名.模块名 as 字段

