---
title: Java基础方法6(Math类)
date: 2019-05-04 16:37:22
categories:
- Java
tags:
- Java
---
## 6．Math类
``` java
public static void main(String[] args) {
		//三角函数方法
	System.out.println("90度的正弦值:"+Math.sin(Math.PI/2));
	System.out.println("0度的余弦值:"+Math.cos(0));
	System.out.println("60度的正切值:"+Math.tan(Math.PI/3));
	System.out.println("2的平方根与2商的反正值:"+Math.asin(Math.sqrt(2)/2));
	System.out.println("2的平方根与2商的反余值:"+Math.asin(Math.sqrt(2)/2));
	System.out.println("1的反正切值:"+Math.atan(1));
	System.out.println("120度的弧度值:"+Math.toRadians(120.0));
	System.out.println("PI/2的角度值:"+Math.toDegrees(Math.PI/2));
		
	//指数函数方法
	System.out.println("e的平方值:"+Math.exp(2));
	System.out.println("以e为底2的对数值:"+Math.log(2));
	System.out.println("以10为底2的对数值:"+Math.log10(2));
	System.out.println("4的平方根值:"+Math.sqrt(4));
	System.out.println("8的立方根值:"+Math.cbrt(8));
	System.out.println("2的2次方值:"+Math.pow(2, 2));
}

public static void main(String[] args) {
		//取整函数方法
		System.out.println("使用ceil()方法取整:"+Math.ceil(5.2));
//返回第一个大于等于参数的整数
		System.out.println("使用floor()方法取整:"+Math.floor(2.5));
//返回第一个小于等于参数的整数
		System.out.println("使用rint()方法取整:"+Math.rint(2.7));
//返回与参数最接近的整数
		System.out.println("使用rint()方法取整:"+Math.rint(2.5));
//返回与参数最接近的整数
		System.out.println("使用round()方法取整:"+Math.round(3.4f));
//将参数加上0.5后返回最接近的整数
		//将参数加上0.5后返回最接近的整数，并将结果强制转换为长整型
		System.out.println("使用round()方法取整:"+Math.round(2.5));

		//取最大值、最小值、绝对值函数方法
		System.out.println("4和8的较大者:"+Math.max(4,8));
		System.out.println("4.4和4较小者:"+Math.min(4.4,4));
		System.out.println("-7的绝对值:"+Math.abs(-7));
	}
```
