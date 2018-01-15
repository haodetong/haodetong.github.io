---
title: IO流 - 二、缓冲区
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

### 二、缓冲区

提高了对数据的读写效率。

对应类：

BufferedWriter

BufferedReader

#### 1、缓冲区单个字符读写
	
	public class Demo6 {
		
		public static void main(String[] args) throws IOException {
			FileReader fr = new FileReader("demo.txt");
			BufferedReader bufr = new BufferedReader(fr);
			
			FileWriter fw = new FileWriter("buf_copy.txt");
			BufferedWriter bufw = new BufferedWriter(fw);
			
			int ch = 0;
			while((ch = bufr.read())!=-1) {
				bufw.write(ch);
			}
			bufw.close();
			bufr.close();
			
		}
		
	}

#### 2、缓冲区逐行读写

	public class Demo6 {
		
		public static void main(String[] args) throws IOException {
			FileReader fr = new FileReader("demo.txt");
			BufferedReader bufr = new BufferedReader(fr);
			
			FileWriter fw = new FileWriter("buf_copy.txt");
			BufferedWriter bufw = new BufferedWriter(fw);
			
			String line = null;
			
			while((line = bufr.readLine())!=null) {
				bufw.write(line);
				bufw.newLine();
				bufw.flush();
			}
			
			bufw.close();
			bufr.close();
			
		}
		
	}
	
#### 3、缓冲区 - 装饰设计模式

装饰设计模式：

对一组对象的功能进行增强时，就可以使用该模式解决问题。

	public class Person {
		public void eat() {
			System.out.println("....eating...");
		}
	}
	
	//通过继承进行功能扩展
	class NewPerson extends Person{
		public void eat() {
			System.out.println("befor...eating...");
			super.eat();
			System.out.println("after...eating");
		}
	}
	
	//通过装饰类进行功能扩展
	class NewPerson2 {
		private Person p;
		NewPerson2(Person p){
			this.p = p;
		}
		void eat() {		
			System.out.println("befor...eating...");
			p.eat();
			System.out.println("after...eating");
		}
	}

装饰和继承的区别：

Writer

|-- TextWriter:用于操作文本

----- BufferedTextWriter:加入缓冲技术

|-- MediaWriter:用于操作媒体

----- BufferedMediaWriter:加入缓冲技术

继承导致原有类的体系越来越臃肿，不够灵活。

class Buffered {
	Buffered(Writer w){
		...
	}
}

装饰比继承更灵活。

#### 4、缓冲区 - 装饰类 - LineNumberReader
	
	public class Demo7 {
	
		public static void main(String[] args) throws IOException {
			
			FileReader fr = new FileReader("demo.txt");
			LineNumberReader lnr = new LineNumberReader(fr);
			
			String line = null;
			lnr.setLineNumber(100);
			while((line = lnr.readLine()) != null) {
				System.out.println(lnr.getLineNumber()+":"+line);
			}
		}
	
	}
	
	输出结果：
	101:abc
	102:cde
