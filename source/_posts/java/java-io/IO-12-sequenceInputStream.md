---
title: IO流 - 十二、序列流 - SequenceInputStream
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

对多个流进行合并

#### 1、构造方法

SequenceInputStream(Enumeration<? extends InputStream> e)


	public class demo1 {
	
		public static void main(String[] args) throws IOException {
			/**
			 * 将1.txt，2.txt，3.txt文件中的数据合并到一个文件4.txt中
			 */
			Vector<FileInputStream> v = new Vector<FileInputStream>();
			v.add(new FileInputStream("1.txt"));
			v.add(new FileInputStream("2.txt"));
			v.add(new FileInputStream("3.txt"));
			
			Enumeration<FileInputStream> en = v.elements();
			
			SequenceInputStream sis = new SequenceInputStream(en);
			
			FileOutputStream fos = new FileOutputStream("4.txt");
			
			byte[] buf = new byte[1024];
			
			int length = 0;
			
			while((length = sis.read(buf)) != -1 ) {
				fos.write(buf,0,length);
			}
			
			fos.close();
			sis.close();
		}
	
	}
	
#### 2、枚举和迭代
	
	//Vctor集合效率低。
	
	Vector<FileInputStream> v = new Vector<FileInputStream>();
	v.add(new FileInputStream("1.txt"));
	v.add(new FileInputStream("2.txt"));
	v.add(new FileInputStream("3.txt"));
	
	Enumeration<FileInputStream> en = v.elements();

	//ArrayList集合代替
	ArrayList<FileInputStream> al = new ArrayList<FileInputStream>();
	//用集合工具类中的方法，返回Enumeration类对象
	Enumeration<FileInputStream> en = Collections.enumeration(al);
