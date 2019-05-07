---
title: Java基础方法7(Math.random())
date: 2019-05-04 16:40:46
categories:
- Java
tags:
- Java
---
## 7．Math.random()方法
``` java
/**
	 *定义产生偶数的方法
	 *@param num1 起始范围参数
	 *@param num2 终止范围参数
	 *@return 随机的范围内偶数
	 */
public static int GetEvenNum(double num1,double num2) {
	//产生num1~num2之间的随机数
	int s = (int)num1+(int)(Math.random()*(num2-num1));
	if(s%2==0) {                                 //判断随机数是否为偶数
			return s;                             //返回
		}else                                   //如果是奇数
			return s+1;		                     //将结果加1后返回
	}
	
	//定义获取任意字符之间的随机数
public static char GetRandomChar(char cha1,char cha2) {
	return (char)(cha1+Math.random()*(cha2-cha1+1));
}
	
public static void main(String[] args) {
	//调用产生随机数方法
	System.out.println("任意一个2~32之间的偶数:"+GetEvenNum(2, 32));
	char a1 = (char)('a'+Math.random()*('z'-'a'+1));
	System.out.println("任意一个a~z之间的字符:"+a1);
		
	System.out.println("任意小写字符:"+GetRandomChar('a', 'z'));
	System.out.println("任意大写字符:"+GetRandomChar('A', 'Z'));
	System.out.println("0到9任意数字字符:"+GetRandomChar('0', '9'));
}
——————————————————————————————————
Random r = new Random();           //实例化一个Random类
	System.out.println("随机产生一个整数:"+r.nextInt());
	System.out.println("随机产生一个大于等于0小于10的整数:"+r.nextInt(10));
	System.out.println("随机产生一个布尔型的值:"+r.nextBoolean());
	System.out.println("随机产生一个双精度型的值:"+r.nextDouble());
	System.out.println("随机产生一个浮点型的值:"+r.nextFloat());
	System.out.println("随机产生一个概率密度为高斯分布的双精度值:"+r.nextGaussian());
```