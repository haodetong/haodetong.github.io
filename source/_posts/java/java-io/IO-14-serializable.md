---
title: IO流 - 十四、IO流 - 对象的序列化、反序列化
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

ObjectInputStream与ObjectOutputStream

被操作的对象需要实现Serializable标记接口

#### 1、ObjectOutputStream-对象序列化

将对象写到硬盘上。

	FileOutputStream fos = new FileOutputStream("t.tmp");
	ObjectOutputStream oos = new ObjectOutputStream(fos);
	
	oos.writeInt(12345);
	oos.writeObject("Today");
	oos.writeObject(new Date());
	
	oos.close;

存储自定义对象时，要实现对象序列化，对象要实现Serializable接口
	
	class Person implements Serializable{
		...
	}
	
	ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("obj.object"));
	oos.writeObject(new Person("小强",30));
	oos.close;

#### 2、ObjectInputStream-对象反序列化

读取存储在硬盘上的对象。

ObjectInputStream ois = new ObjectInputStream(new FileInputStream("obj.object"));

Person p = ois.readObject();
System.out.println(p.getName()+p.getAge());

ois.close();

#### 3、序列化接口 - Seriallizable

显示化序列号
	  
	class Person {
		private static final long serialVersionUID = 97274;
	}
	
将对象存储到硬盘后，即使改动了Person类，依然能识别该类和取出的对象是同一个版本。

#### 4、关键字 - transient

静态属性不会加入序列化写入到硬盘中。

非静态属性，如果不想写入到硬盘中，需要关键字transient（短暂的）修饰。

private static int age;

private transient String name;

都不会被序列化写入硬盘中。