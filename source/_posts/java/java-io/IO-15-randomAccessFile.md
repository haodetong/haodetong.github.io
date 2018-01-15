---
title: IO流 - 十五、RandomAccessFile
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

随机访问文件，自身具备读写的方法。

通过skipBytes(int x)，seek(int x)来达到随机访问。

特点：

1、不是IO体系中的子类。

2、该对象既能读也能写。

3、该对象内部维护了一个byte数组，并通过指针可以操作数组中的元素。

4、可以通过getFilePointer方法获取指针的位置，和通过seek方法设置指针的位置。

5、其实该对象就是将字节输入流和输出流进行了封装。

6、该对象的源或目的只能是文件。通过构造函数就可以看出。可以是文本文件，也可是媒体文件。
	
	public class RandomAccessDemo1 {
	
		public static void main(String[] args) throws IOException {
			
			//randomWrite();
			
			randomRead();
			
		}
	
		public static void randomRead() throws FileNotFoundException, IOException {
			RandomAccessFile raf = new RandomAccessFile("ranacc.txt","r");
			//读4个字节，获取张三
			byte[] buf = new byte[4];
			raf.read(buf);
			String name = new String(buf);
			System.out.println("name="+name);
			
			//再读4个字节，读取年龄
			int age = raf.readInt();
			System.out.println("age="+age);
			
			//获取指针位置
			System.out.println("pos:"+raf.getFilePointer());//pos:8
			
			//设置指针位置，raf.seek(8);//随机读取，只要指定指针的位置即可
			
			raf.close();
		}
	
		public static void randomWrite() throws FileNotFoundException, IOException {
			RandomAccessFile raf = new RandomAccessFile("ranacc.txt","rw");
	
			raf.write("张三".getBytes());
			raf.writeInt(97);
			
			raf.close();
		}
	
	}

随机写入&细节

//往指定位置写入数据
	
	raf.seek(3*8);
	raf.write("哈哈".getBytes());
	raf.writeInt(108);
	raf.close;
