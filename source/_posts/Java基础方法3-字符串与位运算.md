---
title: Java基础方法3(字符串与位运算)
date: 2019-05-04 16:34:49
categories:
- Java
tags:
- Java
---
## 3. 字符串添加删除插入操作
``` java
public static void main(String[] args) {
	   // TODO 自动生成的方法存根
      StringBuilder builder = new StringBuilder("hello 你好");
      builder.append(" world");               //字符串追加操作

      System.out.println(builder.toString());     //5是起始位置
      builder.insert(5,"world"); 
//字符串添加，添加至字符生成器的内容
      System.out.println(builder.toString());  

      tringBuilder bf = new StringBuilder("StringBuilder"); //创建字符串生成器
      bf.delete(5,10);
      System.out.println(bf.toString());   
// 字符串删除操作位置5起始，位置10-1结束
}
//字符串中某位置加入一个字符串
String a = “hello”; 
StringBuffer sb = new StringBuffer(); 
1、 sb.append(a).insert(2,”aaa”); 
结果sb.toSring()为”heaaallo” 
2、 sb.append(a).replace(1, 3, “aaa”); //下标从第一开始，到第三个结束 
结果sb.toSring()为”haaalo” 
2、 sb.append(a).delete(1, 3);//下标从第一开始，到第三个结束 
结果sb.toSring()为”hlo”
附.位运算
//POJ3748  
public static void main(String[] args) {  
     Scanner in = new Scanner(System.in);  
     while(in.hasNext()) {  
//       String[] ss = in.nextLine().trim().split("[,]");  
        String[] ss = in.nextLine().split("[,]");
        Integer r = Integer.parseInt(ss[0], 16);//按16进制解析字符串  
        Integer x = Integer.parseInt(ss[1]);//默认按10进制解析  
        Integer y = Integer.parseInt(ss[2]);  
        r = r & ~(0x1 << x);//第x位设置成0  
        r = r & ~(0x1 << (y - 2));//0  
        r = r | (0x1 << (y - 1));//1  
        r = r | (0x1 << y);//1  
        System.out.println(Integer.toHexString(r));  
    }  
}  
```

