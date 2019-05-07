---
title: Java基础方法4(数组)
date: 2019-05-04 16:36:02
categories:
- Java
tags:
- Java
---
## 4.数组操作
``` java
import java.util.Arrays;       //导入java.util.Array类
public class shuzucaozuo {    //替换数组中元素《通过Arrays类的静态方法fill();》
	public static void main(String[] args) { 
	// TODO 自动生成的方法存根
    int arr[]=new int[5];
    Arrays.fill(arr,8);
    for(int i=0;i<arr.length;i++) {           //循环遍历数组中的元素
    	System.out.println("第"+i+"个元素是:"+arr[i]);
    }                                 //指定数值替换整个数组的元素值            
    int arr2[]=new int[] {45,12,2,10};       //定义并初始化int型数组arr2
    Arrays.fill(arr2,1,2,8);                //使用fill方法对数组进行初始化
    for(int j=0;j<arr.length;j++) {
    	System.out.println("第"+j+"个元素是:"+arr2[j]);
}                      
//指定数值替换指定元素之间的元素值（前闭后开）

    Arrays.sort(arr2);                         //将数组进行排序
   /*int newarr[]=Arrays.copyOf(arr2,5);         //数组复制
    int newarr2[]=Arrays.copyOfRange(arr2,0,3);  //复制指定范围内的元素*/
	}

}


循环遍历操作
int arr2[][]= {{4,3},{1,2}};
    System.out.println("数组中的元素是:");
    int i=0;
    for(int x[]:arr2) {                    //外层循环变量为一维数组
    	i++;                           //外层计数器递增
    	int j=0;                        //内层循环计数器
    	for(int e:x) {                    //循环遍历每一个数组元素
    	   j++;
    	   if(i==arr2.length&&j==x.length) { //判断变量是
        		System.out.print(e);
    	}else                          //如果不是二维数组中最后一个元素
    		System.out.print(e+"、");           //输出信息
    }
} 
```
