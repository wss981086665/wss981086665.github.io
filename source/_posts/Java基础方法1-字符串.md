---
title: Java基础方法1(字符串)
date: 2019-05-04 16:30:10
categories:
- Java
tags:
- Java
---
## 字符串合集
``` java
       public static void main(String[] args) {
              // TODO自动生成的方法存根
              int i=1;
              double sum=0,s=1;
              String str="hello javaword";
              String arr="  and you";
              while(i<=20)
              {
                     s=s*i;
                     i++;
                     sum=sum+1/s;
              }
              System.out.println(sum);
              System.out.println(str);              //完整输出字符串
              System.out.println(str.charAt(6));      //指定下标字符输出
              System.out.println(str.length());       //字符串长度
              System.out.println(str.indexOf("o"));        
              //字符开头位置查找 字符串中没有即indexOf(String)=-1;
              System.out.println(str.lastIndexOf("o"));//字符末尾位置查找
              System.out.println(str+""+arr);       //字符串相连
              System.out.println(str.substring(3));   //截取下标之后的串    
              System.out.println(str.substring(0,3)); //截取指定下标之间的串
              System.out.println("字符串原来的长度:"+arr.length());

              System.out.println("去掉空格后的长度:"+arr.trim().length());

              System.out.println(str.replace("l","L")); 

             //字符串中"l"替换成"L"后返回的新字符串；

              System.out.println("字母串str是以'he'开头的吗？"+str.startsWith("he"));
              //判断字符串的开头与结尾（endsWith）
              //字符串比较1.equals（）方法2.equalsIgnoreCase（）第二种不区分大小写
              //用法：str.equals(String otherstr)  两种方法相同
              System.out.println(str.compareTo(arr));//按字典顺序比较str与arr的大小
              System.out.println(str.toUpperCase());
              //大小写改为大写，相反:toLowerCase
              System.out.println(str.toString());     
       }
```