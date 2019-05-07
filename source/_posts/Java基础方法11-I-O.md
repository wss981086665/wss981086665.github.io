---
title: Java基础方法11(I/O)
date: 2019-05-04 16:45:01
categories:
- Java
tags:
- Java
---
## 11文件的创建与删除
``` java
public static void main(String[] args) {
		File file = new File("word.txt");
		if(file.exists()) {
			file.delete();
			System.out.println("文件已删除");
		}
		else {
			try {
				file.createNewFile();
				System.out.println("文件已创建");
			} catch (IOException e) {
				e.printStackTrace();
			}
		}

	} 
15.5 .带缓存的输入输出流
public static void main(String[] args) {
	String content[] = {"好久不见","最近好吗","常联系"};
	File file = new File("word.txt");
	try {			
		FileWriter fw = new FileWriter(file);   //创建FileOutputStream对象
		BufferedWriter bufw = new BufferedWriter(fw);
		for(int k=0; k<content.length; k++) {   //循环遍历数组
			bufw.write(content[k]); //将字符串数组中的元素写入到磁盘文件中
			bufw.newLine();      //将数组中的单个元素以单行的形式写入文件
		}
		bufw.close();                     //将BufferedWriter流关闭
		fw.close();                       //将FileWriter流关闭
	}catch(Exception e) {                  //处理异常
		e.printStackTrace();
	}
	try {
		FileReader fr = new FileReader(file);  //创建FileReader类
		BufferedReader bufr = new BufferedReader(fr);
		String s = null;                   //创建字符串对象
		int i = 0;                        //声明int类型
		while((s = bufr.readLine())!=null) {
		   i++;                         //将变量做自增运算
		   System.out.println("第"+i+"行:"+s);//输出文件数据
		}
		bufr.close();                     //将FileReader流关闭
		fr.close();                       //将FileReader流关闭
	}catch(Exception e) {                 //处理异常
		e.printStackTrace();
	}
}
15.6数据输入输出流
try {
			//创建FileOutputStream对象
			FileOutputStream fs = new FileOutputStream("word.txt");
			DataOutputStream ds = new DataOutputStream(fs);
			ds.writeUTF("使用writeUFT()方法写入数据");
			ds.writeChars("使用writeChars()方法写入数据");
			ds.writeBytes("使用writeBytes()方法写入数据");
			ds.close();
			FileInputStream fis = new FileInputStream("word.txt");
			DataInputStream dis = new DataInputStream(fis);
			System.out.println(dis.readUTF());
		}catch(Exception e) {
			e.printStackTrace();
		} 
获取文件信息
public static void main(String[] args) {
		File file = new File("word.txt");
		if(file.exists()) {
			String name = file.getName();
			long length = file.length();
			boolean hidden = file.isHidden();
			System.out.println("文件名称: "+name);
			System.out.println("文件长度是: "+length);
			System.out.println("该文件是隐藏文件吗: "+hidden);
		}else {
				System.out.println("文件不存在");
		}
	}
```
