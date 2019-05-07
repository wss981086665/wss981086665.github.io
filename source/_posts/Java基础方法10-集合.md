---
title: Java基础方法10(集合)
date: 2019-05-04 16:44:00
categories:
- Java
tags:
- Java
---
## 10．Set集合
``` java
import java.util.Iterator;
import java.util.TreeSet;
public class UpdateStu implements Comparable<Object>{  //创建类实现Comparable接口
	String name;                            
	long id;
	public UpdateStu(String name, long id) {            //构造方法
		this.id = id;
		this.name = name;	
	}
	public int compareTo(Object o) {
		UpdateStu upstu = (UpdateStu)o;
		int result = id>upstu.id?1:(id==upstu.id?0:-1);    //参照代码说明
		return result;		
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public long getId() {
		return id;
	}
	public void setId(long id) {
		this.id = id;
	}
	public static void main(String[] args) {
		// TODO 自动生成的方法存根
		UpdateStu stu1 = new UpdateStu("李同学",01011);  
		UpdateStu stu2 = new UpdateStu("陈同学",01021);  //参照代码说明
		UpdateStu stu3 = new UpdateStu("王同学",01051);
		UpdateStu stu4 = new UpdateStu("马同学",01012);
		TreeSet<UpdateStu> tree = new TreeSet<>();
		tree.add(stu1);                                //向集合添加对象
		tree.add(stu2);
		tree.add(stu3);
		tree.add(stu4);
		Iterator<UpdateStu> it = tree.iterator();   //Set集合中所有对象的迭代器
		System.out.println("Set集合中的所有元素:");;
		while(it.hasNext()) {                            //遍历集合
			UpdateStu stu = (UpdateStu)it.next();
			System.out.println(stu.getId()+" "+stu.getName());
		}
		it = tree.headSet(stu2).iterator();           
//截取排在stu2对象之前的对象
		System.out.println("截取前面部分的集合: ");
		while(it.hasNext()) {                          //遍历集合
			UpdateStu stu = (UpdateStu)it.next();
			System.out.println(stu.getId()+" "+stu.getName());
		}
		it = tree.subSet(stu2, stu3).iterator();       
//截取排在stu2与stu3之间的对象
		System.out.println("截取中间部分的集合:");
		while(it.hasNext()) {                          //遍历集合
			UpdateStu stu = (UpdateStu)it.next();
			System.out.println(stu.getId()+" "+stu.getName());
		}
		
	}
}
List集合
	List<String> list = new ArrayList<>();
	list.add("a");
	list.add("b");
	list.add("c");
	int i = (int)(Math.random()*list.size());
	System.out.println("随机获取数组中的元素:"+list.get(i));
	list.remove(2);
	System.out.println("将索引是'2'的元素从数组中移除后，数组中的元素是:");
		for(int j=0; j<list.size(); j++) {
			System.out.println(list.get(j));
		}
Collection接口
Collection<String> list = new ArrayList<>();
		list.add("a");                        //向集合添加数据
		list.add("b");
		list.add("c");
		Iterator<String> it = list.iterator();//创建迭代器
		while(it.hasNext()) {
			String str = (String)it.next();   //获取集合中的元素
			System.out.println(str);
		}
```
