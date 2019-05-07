---
title: Python基础知识全网最全6(网络爬虫)
date: 2019-05-04 10:45:51
categories:
- Python
tags:
- Python
- 爬虫
---
## 六、网络爬虫

### 1.  python如何访问互联网

#### (1).urllib模块:

> 实战1:  **下载图片download_cat.py**
实战2: **有道翻译translation.py**
实战3: **代理请求proxy_eg.py**
实战4: **爬取图片:****download_mm1.py**
注：为了不影响读者阅读博客，这些程序将会在博客末尾列出

- 1.**访问**: 
``` python
import urllib.request
response = urllib.request.urlopen(“http://www.xztywss.top”)
html= response.read()          #将网页所有代码转换成字符串返回
html= html.decode(“utf-8”)      #将网页源码进行转码
```
``` python
…
req = urllib.request.Request( url[, data][,head])
response = urllib.request.urlopen(req)
…
```
- 2.**response.geturl()**: 获取访问的网络地址

- 3.**response.info()**: 获取远程服务器返回的header信息

- 4.**response.getcode()**: 返回访问的状态。正常访问是200

- 5.**编码(urllib.parse)**: 
```
import urllib.parse
data = urllib.parse.urlencode(data).encode(‘utf-8’)
```
- 6.**JSON****转换**: 
``` python
import json
target =json.loads(html)         #此处target即为JSON数据
```
- 7.**隐藏(head****设置)**: (1).配置head参数然后添加到Request请求

#### (2).直接设置:Request.add_header(key,value)

- 8.**延时请求(sleep(index))**: 
``` python
import time
    ……
Time.sleep(5)            #程序停顿5秒
```
- 9.**代理请求(proxy)**: 
``` python
(1).参数是一个字典{'类型':'代理ip:端口号'}
        proxy_support =urllib.request.ProxyHandler({})
(2).定制、创建一个opener
        opener =urllib.request.build_opener(proxy_support)
(3)(非必要)
        opener.addheaders = [(key,value)]
(4).安装opener
        urllib.request.install_opener(opener)
(5).调用opener
        opener.open(url)
```
### 爬取示例:
> 注:  由于可能会有侵犯网站权限的风险，因此我将程序中的网站地址全覆盖掉了，读者可以自行根据图片网站的源码修改程序的相关内容。
``` Python
import urllib.request
import os
import random

def url_open(url):           #返回页面的html代码
    req = urllib.request.Request(url)
   
    response = urllib.request.urlopen(url)
    html = response.read()

    return html

def get_img_url(url):
    print(url)
    html2 = url_open(url).decode("gbk","ignore")
    e = html2.find('当前位置')
    f = html2.find('XXXX是全网最好的4K壁纸站')
    g = html2.find('img src=',e,f)
    h = html2.find('.jpg',g,f)

    imgstr2 = ("http://xxx.xxxx.com" + html2[g+9:h+4])    #/upload/allimg/
    return imgstr2
    
def find_imgs(url):
    html = url_open(url).decode('gbk',"ignore")
    img_addrs = []

    c = html.find('当前位置')
    d = html.find('上一页')
    a = html.find('/tupian',c)

    while a != -1:
        b = html.find('.html',a)
        if b != -1:
            imgstr = get_img_url("http://xxx.xxxx.com" + html[a:b+5])
            img_addrs.append(imgstr)
        else:
            b = a + 9
        a = html.find('/tupian',b,d)

    return img_addrs

def save_imgs(folder,img_addrs):
    for each in img_addrs:
        print(each)
        filename = str(random.randint(111111,999999)) + '.jpg'
        with open(filename,'wb') as f:
            img = url_open(each)
            f.write(img)

def download_mm(folder='4kBiZhi'):
    os.mkdir(folder)
    os.chdir(folder)

    index = ['2','3','4','5','6','7','8','9','10','11','12','13','14','15','16','17','18','19','20']
    for each in index:
        url = 'http://xxx.xxxx.com/index_'+ each + '.html'    #/index_?.html
        img_addrs = find_imgs(url)
        save_imgs(folder,img_addrs)

if __name__ == '__main__':
    download_mm()
```
### urllib模块-程序(终)
#### (1) download_cat.py
``` python
import urllib.request

response = urllib.request.urlopen("http://placekitten.com/500/600")
cat_img = response.read()

with open('cat_500_600.jpg','wb') as f:
    f.write(cat_img)

```
#### (2) translation.py
``` python
#由于不可知原因,此程序无法执行。但是代码书协逻辑值得参考

import urllib.request
import urllib.parse
import json

content = input("请输入需要翻译的内容:")

url = 'http://fanyi.youdao.com/translate_o?smartresult=dict&smartresult=rule'

head = {}
head['User-Agent'] = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36'

data = {}
data['i'] = content
data['from'] = 'AUTO'
data['to'] = 'AUTO'
data['smartresult'] = 'dict'
data['client'] = 'fanyideskweb'
data['salt'] = '15484690529866'
data['sign'] = '552cf5a85c7937e6dfb1c478b413148r'
data['ts'] = '1548469052987'
data['bv'] = 'b34b626f1c1da1753c455d5223882b60'
data['doctype'] = 'json'
data['keyfrom'] = 'fanyi.web'
data['ue'] = 'UTF-8'
data['action'] = 'FY_BY_CLICKBUTTION'
data['typoResult'] = 'true'
data = urllib.parse.urlencode(data).encode('utf-8')

req = urllib.request.Request(url,data,head)
response = urllib.request.urlopen(req)
html = response.read().decode('utf-8')

target = json.loads(html)

print(target)

# print("翻译结果:%s" % (target['translateResult'][0][0]['tgt']))

```
#### (3) proxy_eg.py
``` python
import urllib.request
import random

url = "http://www.ip111.cn/"

iplist = ['123.118.171.184:9999','223.241.78.137:8010','193.112.15.70:8118']

proxy_support = urllib.request.ProxyHandler({'http':random.choice(iplist)})

opener = urllib.request.build_opener(proxy_support)
opener.addheaders = [('User-Agent','Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36')]

urllib.request.install_opener(opener)

response = urllib.request.urlopen(url)
html = response.read().decode("utf-8")

print(html)
```
#### (4) download_mm1.py
``` python
import urllib.request
import os

def url_open(url):           #返回页面的html代码
    req = urllib.request.Request(url)
    req.add_header('User-Agent', 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36')
    response = urllib.request.urlopen(url)
    html = response.read()

    return html

def get_page(url):          #返回的是一组字符串类型的数字
    html = url_open(url).decode('utf-8')
    
    a = html.find('current-comment-page') + 23
    b = html.find(']',a)

    return html[a:b]

def find_imgs(url):
    html = url_open(url).decode('utf-8')
    img_addrs = []
    
    a = html.find('img src=')

    while a != -1:
        b = html.find('.jpg',a+255)
        if b != -1:
            img_addrs.append(html[a+9:b+4])
        else:
            b = a + 9
        a = html.find('img src=',b)

    return img_addrs
    
def save_imgs(folder,img_addrs):
    for each in img_addrs:
        filename = each.split('/')[-1]
        with open(filename,'wb') as f:
            img = url_open(each)
            f.write(img)

def download_mm(folder='ooxx',pages=10):
    os.mkdir(folder)
    os.chdir(folder)

    url = "http://xxoo.net/ooxx/"
    page_num = int(get_page(url))

    for i in range(pages):
        page_num -= i
        page_url = url + 'page-' + str(page_num) + '#comments'
        img_addr = find_imgs(page_url)
        save_imgs(folder,img_addrs)

if __name__ == '__main__':
    download_mm()
```