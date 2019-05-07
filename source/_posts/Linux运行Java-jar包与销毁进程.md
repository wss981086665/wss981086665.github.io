---
title: Linux运行Java jar包与销毁进程
date: 2019-05-04 17:27:00
categories:
- Linux
tags:
- Linux
- 操作系统
---
## 根据端口查看这个进程的pid：
``` c
netstat -lnp|grep 8080  #8080请换为你的apache需要的端口
```

## 查看进程的详细信息：
``` c
ps 1777    #1777就是上一步得到的pid
```
## 杀掉进程：
``` c
例如： kill -9 [PID]  #-9 表示强迫进程立即停止
```
## 后台运行jar包: 
``` c
nohup java -jar ** &
```
![aae.gif](https://upload-images.jianshu.io/upload_images/13687958-a7b10d84bc8f5b11.gif?imageMogr2/auto-orient/strip)

