---
title: Java基础方法2(字符串分割与格式化)
date: 2019-05-04 16:33:55
categories:
- Java
tags:
- Java
---
## 字符串分割与格式化
``` java
        public static void main(String[] args) {

              Stringstr="192.168.0.1";

              String[]firstArray=str.split("\\.");  

              //按照"."进行分割，使用转义字符"\\.";

              String[] secondArray=str.split("\\.",2);   
 
               //按照"."进行两次分割，使用转义字符"\\.";

              System.out.println("str的原址为:["+str+"]");   //输出str原值；
   
              System.out.print("全部分割的结果:");

              for(String a:firstArray) {

                       System.out.print("["+a+"]");

              }                                      //输出全部分割的结果

              System.out.println();                       //换行

              System.out.print("分割两次的结果:");

              for(String a:secondArray) {

                      System.out.print("["+a+"]");

              }
 
              System.out.println();

              Date date = new Date();                    //创建Date对象date

              //如下 将date进行格式化

             String day =String.format("%td",date);

             String year = String.format("%tY",date);

             String month = String.format("%tB",date); //将date进行格式化

             System.out.println("今天是："+day+"号");

             System.out.println("今年是："+year+"年");

             System.out.println("今月是："+month+"月"); //日期字符格式化

             String hour = String.format("%tH",date);

             String minute = String.format("%tM",date);

             String second =String.format("%tS",date); //将date进行格式化

             System.out.println("现在是："+hour+"时"+minute+"分"+second+"秒");

             //输出的信息常用年月日组合见p99；                                             //时间字符格式化

             String str3 = String.format("%x",200);

             System.out.println("200的十六进制数是："+str3); //常规类型格式化
 
             String day =String.format("%tc",date);       //全部日期和时间信息

             String year = String.format("%tF",date);       //”年—月—日” 格式

             String month = String.format("%tr",date);     //时:分:秒 PM(AM)格式

}
```


