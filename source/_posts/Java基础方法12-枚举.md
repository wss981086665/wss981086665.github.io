---
title: Java基础方法12(枚举)
date: 2019-05-04 16:46:19
categories:
- Java
tags:
- Java
---
## 12.枚举类型
``` java
interface Constants{                          //使用枚举类型设置常亮
	public static final int Constants_A=1;
	public static final int Constants_B=12;
}
public class ConstantsTest{
	enum Constants2{
		Constants_A,Constants_B;
	}
	public static void doit(int c) {   //定义一个方法，这里的参数为int型
		switch(c) {              //根据常量的值做不同的操作
		     case Constants.Constants_A:
		    	 System.out.println("doit() Constants_A");
		    	 break;
		     case Constants.Constants_B:
		    	 System.out.println("doit() Constants_B");
		    	 break;
		}
	}
	public static void doit2(Constants2 c) { //定义一个参数对象是枚举类型的方法
		switch(c) {                    //根据枚举类型对象做不同操作
		     case Constants_A:
		    	 System.out.println("doit2() Constants_A");
		    	 break;
		     case Constants_B:
		    	 System.out.println("doit2() Constants_B");
		}
	}
	public static void main(String[] args) {
		ConstantsTest.doit(Constants.Constants_A);   //使用接口中定义的常量
		ConstantsTest.doit2(Constants2.Constants_A); //使用枚举类型中的常量
		ConstantsTest.doit2(Constants2.Constants_A); //使用枚举类型中的常量
		ConstantsTest.doit(3);
       //ConstantsTest.doit2(3);
	}
}
 
17.1深入了解枚举类型
import static java.lang.System.out;
public class ShowEnum{
	enum Constants2{
		Constants_A,Constants_B,Constants_C         //将常量放在枚举类型中
	}
	//定义比较枚举类型方法，参数类型为枚举类型
	public static void compare(Constants2 c) {
		//根据values()方法返回的数组做循环操作
		for(int i=0; i<Constants2.values().length; i++) {
			//将比较结果返回
			out.println(c+"与"+Constants2.values()[i]+"的比较结果为: "
					+c.compareTo(Constants2.values()[i]));
		}
	}

	public static void main(String[] args) {
		for(int i=0; i<Constants2.values().length; i++) {
			//将枚举成员变量打印
			out.println("枚举类型成员变量: "+Constants2.values()[i]);
		}
		compare(Constants2.valueOf("Constants_B"));
		for(int i=0; i<Constants2.values().length; i++) {
			//在循环中获取枚举类型成员的索引位值
			out.println(Constants2.values()[i]+"在枚举类型中位置索引值"
		+Constants2.values()[i].ordinal());
		}
	}
}
```