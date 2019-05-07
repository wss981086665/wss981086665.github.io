---
title: Python基础知识全网最全2(引入模块)
date: 2019-05-07 13:37:35
categories:
- Python
tags:
- Python
---
## 1.Python标准库的使用
- **打开标准库(IDLE)**: Help  -->  Python Docs
- **Python模块交流社区**: [https://pypi.python.org/pypi](https://pypi.python.org/pypi)
***There have 全球python爱好者提交的模块***

- **快速了解模块**: 
``` python
import demo
print(demo.__doc__)      #查看demo模块的相关介绍
dir(demo)                #查看demo模块内置的方法               
demo.__all__             #查看这个模块所有可以供外界调用的所有东西的列表(类名 , 方法名)。
demo.__file__            #指明该模块源代码所在的位置
help(demo)               #获取demo模块的信息,比doc的信息更详细
```
- **from demo import**: 将\_\_all__里面的所有名字导入到命名空间

***即可以直接使用导入的名称***

## 2、random模块

> random用于随机生成的module
例:  
``` python
import random
secret = random.randint(1,100)     # 随机生成1~100以内的数字
```
## 3、os模块
### (1).os模块关于文件的函数:

![os模块关于文件的函数](https://upload-images.jianshu.io/upload_images/13687958-1967b514c9ebf996.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![os模块关于文件的函数](https://upload-images.jianshu.io/upload_images/13687958-b83bcf8b6b35d718.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### (2).os.path模块函数
使用方法:
``` python
os.path.函数名
```
![os.path模块函数](https://upload-images.jianshu.io/upload_images/13687958-1163b4c6543cd984.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 4.pickle模块
> 存放: pickling
读取: unpickling
### (1).dump():  
###### pickle.dump(对象,文件)
例: 
``` python
my_list = [123, 3.14, '小甲鱼', ['another list']]
pickle_file = open( 'my_list.pkl', 'wb')
pickle.dump(my_list, pickle_file)
```
### (2).load():  
``` python
pickle.load(文件名/路径)      #读取文件
```
## 5.easygui模块
导入:
``` python
import  easygui  as g        #推荐用法
```
## 6.time模块
### (1).时间元组(time.struct_time):
![时间元组](https://upload-images.jianshu.io/upload_images/13687958-52cbde36c80d5403.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 7.re模块(正则表达式)

### (1).模块方法:

- **1.re.search(r'格式','字符串')**:
例:匹配ip(格式):
``` python
(([01]{0,1}\d{0,1}\d|2[0-4]\d|25[0-5])\.){3}([01]{0,1}\d{0,1}\d|2[0-4]\d|25[0-5])
```
- **2.re.findall()**:  将匹配到的所有字符以列表的方式返回
例:
``` python
rs.findall(r'[a-z]','FishC.com')  
结果：
    ['i','s','h','c','o','m']
```
- **3.re.compile(r'格式')**: 创建模式对象

例: 
``` python
p = re.compile(r'[A-Z]')
p.findall('I Love')    
结果：['I','L']
```
- 4.result.group()**: 获得匹配的结果
例: 
``` python
result = re.search(r'(\w+) (\w+)','I love FishC.com!')
result.group()        结果：'love FishC'
result.group(1)       结果：'love'
result.group(2)       结果：'FishC'
```
- **5.result.start()**:  开始的位置(不是下标,下标从0开始)

- **6.result.end()**:    结束的位置

- **7.result.span()**:  字符串中字符位置的范围

### (2).基本语法:
- 1.**\num(****数字)**: 如果num值为1~99,则\n(数字)表示匹配到的前面第n个子组对应的字符串

例:
``` python
(a)(b)(c)\2  à  abcb
```
> 如果num是以0开头,或者3个数字的长度,那么就匹配八进制数字所对应的ASCII码值对应的字符

- 2.**取消贪婪模式**: 
> 例: <html>aaaaaaaaaaaaaaa</html>
<.+> 会匹配到<html>aaaaaaaaaaaaaaa</html>
<.+?> 会匹配到<html>  **-->**非贪婪模式

- 3.**[^a-z]**: 匹配除了a-z之外的所有字符,^放在后面就表示匹配 ^ 本身

- 4.**[\n]**: 匹配\n  相当于\转义字符

- 5.**\b**: 匹配一个单词边界,单词被定义为Unidcode的字母数字或下横线字符

- 6.**\B**: 匹配非单词边界,其实就是与\b相反

- 7.**编译标志**:
![编译标志](https://upload-images.jianshu.io/upload_images/13687958-096f35358f072541.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![编译标志](https://upload-images.jianshu.io/upload_images/13687958-b2388ff2fc78ccfb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![编译标志](https://upload-images.jianshu.io/upload_images/13687958-f8f75239414af011.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![编译标志](https://upload-images.jianshu.io/upload_images/13687958-bb6d127b88f42660.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 8.Scrapy框架

### (1).安装

- **1.安装pywin32**:下载安装

- **2.安装pip**: Python\Scripts\pip.exe

- **3.安装lxml**: pip install lxml

- **4.安装OpenSSL**: pip install OpenSSL

- **5.安装Scrapy**: pip install Scrapy
> 注意: 安装以上步骤之前要确保有Visual c++ 相应版本的支持

- **6.Selector**是一个选择器,他有四个基本方法:

![Selector的四个基本方法](https://upload-images.jianshu.io/upload_images/13687958-734a2749a8234c08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**获取网页信息**: 进入项目根目录,运行**cmd**
``` python
scrappy shell '网站地址'           #获取目标(会得到response)
response.body                     #得到网页源代码
response.headers                  #网页的头信息
```
**筛选**:根据response的信息用以上Selector中的函数进行筛选
``` Python
response.xpath() == response.selector.xpath()
```
![筛选](https://upload-images.jianshu.io/upload_images/13687958-0424ce37b2d7993d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
例: 
``` Python
response.xpath('//title')                     #返回一个列表
response.xpath('//title/text()').extract()    #返回标签中的字符串的列表
response.xpath('//ul/li/a/@href').extract()   #返回所有ul标签中全部li标签中href属性的值的列表
```
- **7.项目结构**(tutorial为项目名):

![项目结构](https://upload-images.jianshu.io/upload_images/13687958-10684276d28a74d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### (2).使用
- 1.**创建项目**: 
``` python
scrapy startproject '项目名'
```
这一步会在cmd运行的文件夹下创建一个以该项目名为名的文件夹
- 2.**定义Item****容器**: 在items.py中写入字段

- 3.**编写爬虫**: 
> 在spiders文件夹下创建py文件,用于编写爬虫类Spider,Spider是用户编写的用于从网站上爬取数据的类。Spider类包含用于下载的初始URL,然后是分析网页中的内容,还有**提取item方法**
**注意**: py文件里有一个name(爬虫名称)属性非常重要,是识别该爬虫的唯一标识
- 4.**获取网页信息**: 
``` python
test = scrappy.selector.Selector(response)
```
- 5.**筛选**: 
``` python
sites = test.xpath(//ul[@class="directory-url"]/li) .extract()
# 获取所有class为'directory-url'的ul标签里的全部li标签所包含的字符串
```
 - 6.**输出数据**: 
``` python
scrapy crawl dmoz –o items.json –t json
```
**cmd运行爬虫(调试)**: scrapy crawl 爬虫名称
## 9.Tkinter模块(GUI终极选择)
暂无
