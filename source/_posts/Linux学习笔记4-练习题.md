---
title: Linux学习笔记4(练习题)
date: 2019-05-04 17:03:53
categories:
- Linux
tags:
- Linux
- 操作系统
---
# 四.练习题
## 1.如和通过命令行重启linux操作系统？
```     
   reboot
```
## 2.如何在命令行中快速删除光标前/后的内容？ 
```    
    前:ctrl+u  后:ctrl+k
```
## 3.如何删除/tmp下所有A开头的文件？
```     
    #rm -f /tmp/A*
```
## 4.系统重要文件需要备份，如何把/etc/passwd备份到/tmp目录下? 
```
    #cp /etc/passwd /tmp/
```
## 5.如何查看系统最后创建的3个用户？  
```  
    #tail -3 /etc/passwd
```
## 6.什么命令可以统计当前系统中一共有多少个账户？ 
```    
    1).#wc -l /etc/passwd     
    2).#cat /etc/passwd|wc -1
```
## 7.如何创建/tmp/test.conf文件？  
``` 
    #touch /tmp/test.conf
```
## 8.如何通过vim编辑打开/tmp/test.conf?  
```   
    #vim /tmp/test.conf
```
## 9.如何查看/etc/passwd的头三行和尾三行？
```    
     (头).#head -3 /etc/passwd
     (尾).#tail -3 /etc/passwd
```
## 10.如何一次性创建目录/text/1/2/3/4?
```
    #mkdir -p /test/1/2/3/4
```
## 11.如何最快的返回到当前用户的家目录？
```
    #cd ~
```
## 12.如何查看/etc所占的磁盘空间
```
    #du -sh /etc
```
## 13.如何删除/tmp下的所有文件？
```
    #rm -rf /tmp/*
```
## 14.尝试启动Apache的服务，并且检查是否启动成功。
```
     #service httpd start
     #ps -ef|grep httpd
```
## 15.使用已学过的命令杀死Apache的进程
```
    #killall httpd
```
![aae.gif](https://upload-images.jianshu.io/upload_images/13687958-aa226dbc37f552d1.gif?imageMogr2/auto-orient/strip)

