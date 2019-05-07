---
title: Python基础知识全网最全3(内部方法)
date: 2019-05-07 13:59:37
categories:
- Python
tags:
- Python
---
## 1.综合
- 1.**type(****数据)**:  获取数据的类型

- 2.**isinstance()**:  检查元素类型,返回布尔值
例:
``` python
a = '小甲鱼'   
isinstance(a,str)         # 结果为 True
```
- 3.**assert**:  assert(断言) 条件。当条件语句为假时,程序终止并抛出异常

- 4.**len(demo)**:  得到demo数组(或字符串)的长度

- 5.**range([start],stop,[step=1])**:  
> 生成一个从start参数的值开始到stop参数的值结束的数字序列,start、step参数可选并且step默认值为1

- 6.**append()**:  向列表中添加元素

- 7.**extend()**:   extend将参数(新的列表)添加至列表中(即连接两个列表)

- 8.**insert()**:  insert(位置,元素) 即将元素插入到指定位置

- 9.**remove()**:  remove(元素) 从列表中删除此元素

- 10.**del**: delarr[i]:删除列表中下标为i的元素  
``` Python
del arr: 清空数组,删除所有元素
```
- 11.**pop()**:  
> **arr.pop()**: 删除数组arr中最后一个元素并返回该元素的值
**arr.pop(i)**: 删除数组arr中下标为i的元素并返回该元素的值
- 12.**not in**:  
``` python
123 not in arr:如果数组中没有123这个元素,返回False。与in相反
```
- 13.**count()**:  计算元素在数组中出线的次数,例:arr.count(元素);
- 14.**index()**:  arr.index(123):返回123在arr数组中第一次出现的位置的下标
arr.index(123,3,7):123在arr[3]与aaa[6]之间首次出现的位置的下标
- 15.**reverse()**:  数组反转。

- 16.**sort()**: 
``` python
arr.sort(): 对arr进行排序(默认从小到大)
arr.sort(reverse=True):  对arr进行排序(从大到小)
```
- 17.**filter()**:  
例:
``` python
list(filter(None,[1,0,False,True])) à [1,True]
list( filter ( lambda x : x % 2 ), range(10) )à [1,3,5,7,9]
```
- 18.**map()**:  
例: 
``` python
list( map( lambda x : x * 2,range(10)))
等价于：
     [ 0 , 2 , 4, 6 , 8 , 10 , 12 , 14 , 16 , 18 ]
```
- 19.**str()**:  返回一个对象的string格式

## 2、字符串

1.**casefold()**: str.casefold():将字符串str中所有大写字母转为小写
2.**format()**:
``` python
'{0} love {1}.{2}'.format('I','FishC','com')  
'{a} love {b}.{c}'.format(a='I',b='FishC',c='com')
'{0} love {b}.{c}'.format('I', b='FishC',c='com')
'{a} love {b}.{0}'.format(a='I',b='FishC','com')
以上四条结果均为: I love FishC.com

'{{0}}'.format('不打印')              结果为:’{0}’
'{0:.lf}{1}'.format(27.658,'GB')      结果为:’27.7GB
```
![字符串格式化符号含义](https://upload-images.jianshu.io/upload_images/13687958-5587a17bb8bf6a7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
例:
``` python
'%c %c %c'% (97,98,99)   -> 'a b c'
'%d + %d = %d' %(4,5,4+5) -> '4 +5 = 9'
```
## 3、序列
- 1.**list()**:
例:
``` python
a = 'a b c'   
list(b)       即: ['a',' ','b',' ','c']
c = (1,2,3)   
list(c)       即: [ 1 , 2 , 3 ]
```
- 2.**max(arr)**:返回数组arr中最大的值

- 3.**sum(arr,[index])**:
> sum(arr):返回arr中数字的总和
sum(arr,8):返回arr中数字的总和+8

- 4.**enumerate(arr)**:
``` python
a = [ 6 , 2 , 4 ]           #返回的是一个对象
list(enumerate(a))   即: [(0,6),(1,2),(2,4)]
```
- 5.**sorted(arr)**:对列表arr进行排序

- 6.**reversed(arr)**:对列表arr进行反转,返回的是一个对象

- 7.**zip()**:
例: 
``` python
a = [2,3,4,5,6]  b = [7,8,9]
list( zip(a , b) )    即: [ (2,7) , (3,8) , (4,9) ]
```
## 4、map字典内嵌方法(key-value集合)

- 1.**fromkeys()**:  
语句 : 
``` python
demo = {}
demo.fromkeys( (1,2,3) , ”哈哈” )
结果: {1:'哈哈',2:'哈哈',3:'哈哈'}
```
- 2.**keys()**: demo.keys()= [ 1 , 2 , 3 ]

- 3.**values()**:demo.values() = '哈哈',' ‘哈哈','哈哈']

- 4.**items()**:demo.items() = [(1,'哈哈'),(2,'哈哈'),(3,'哈哈')]

- 5.**get()**:  
``` python
用法一:
     demo.get(key)               #查找key对应的value
用法二:
     demo.get(key,defaultvalue)  #查找key对应的value,如果没有此key,就用defaultvalue充当默认值
```
- 6.**clear()**:
清空字典:
``` python  
b={1:'la',2:'sese'}
a= b
a.clear()  结果: a = {}  b = {}
```
- 7.**copy()**:复制字典

- 8.**pop()**:  
``` python
用法一: 
     a.pop(key)      #弹出key对应的item
用法二: 
     a.pop()         #随机弹出
```
- 9.**setdefault()**:\
``` python
a.setdefault('abc')         #字典里会多出一个{‘abc’,None}
a.setdefault(5,'def')       #字典里会多出一个{ 5 , ’def’ }
```
- 10.**update()**:
``` python
a = {1:'one','小白':'two'}
b = { ‘小白’ : ‘小狗’ }
a.update(b)    等价于: a = {1:'one','小白':'小狗'}
``` 
## 5、set集合

- 1.创建:  
``` python
#一种是直接把一队元素用花括号括起来
num1 = { 1 , 2 , 3 , 4 , 5 }
#另一种是使用set()工厂函数
num2 = set( [ 1 , 2 , 3 , 4 , 5 ])
```
- 2.add(元素): 向set集合中添加一个元素

- 3.remove(元素): 从set集合中移除该元素

- 4.frozenset(): 定义方法同set() , 定义一个元素不可变的set集合

