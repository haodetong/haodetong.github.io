---
title: IO流 - 一、概述
date: 2018-01-15 10:09:36
tags: IO
category: java
keywords: java, IO
description: java-IO流-概述

---

#### （一）、字节流&字符流

输入流和输出流是相对于内存设备而言。

将外设中的数据读取到内存中：输入。

将内存的数据写入到外设中：输出。

字符流的由来：字节流读取文字字节数据后，不直接操作，而是先查指定的编码表，获取对应的文字，再对文字进行操作。

简单说，字符流就是字节流+编码表。

#### （二）、IO流常用基类

#### 1、字节流的抽象基类：

InputStream, OutputStream

#### 2、字符流的抽象基类：

Reader, Writer

#### (三)、字符流-FileWriter

换行和续写

	public class IoDemo1 {
		
		private static final String LINE_SPERATOR = System.getProperty("line.separator");
		
		public static void main(String[] args) throws IOException {
			
			FileWriter fw = new FileWriter("demo.php",true);
			
			//临时存储到缓存区中
			fw.write("abc"+LINE_SPERATOR);
	
			//刷新，将数据写入文件
			fw.flush();
			
			//继续添加到缓存区
			fw.write("cde");
			
			//关闭流，关闭前会先调用flush刷新缓冲区中的数据到文件中。
			fw.close();
			
		}
	
	}

异常处理

public class IoDemo2 {
	
	private static final String LINE_SPERATOR = System.getProperty("line.separator");
	
	public static void main(String[] args) {
		
		FileWriter fw = null;
		try {
			fw = new FileWriter("demo.php",true);
			fw.write("abc"+LINE_SPERATOR);
			fw.flush();		
			fw.write("cde");
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				if(fw != null)
					fw.close();
			} catch (IOException e) {
				throw new RuntimeException("关闭异常");
			}			
		}
		
		
	}

#### (四)、字符流-FileReader

#### 1、读取方式一 read()读取字符

	public class IoDemo3 {
	
		public static void main(String[] args) throws IOException {
			
			FileReader fr = new FileReader("demo.txt");
			
			int ch = fr.read();
			
			while(ch > -1) {
				System.out.println((char)ch);
			}
			
		}
	
	}

#### 2、读取方式二 

int read(char[])将数据存储到数组中，并返回数据的长度

	public class IoDemo3 {
	
		public static void main(String[] args) throws IOException {
			/*
			FileReader fr = new FileReader("demo.txt");
			
			int ch = fr.read();
			
			while(ch > -1) {
				System.out.println((char)ch);
			}
			*/
			FileReader fr = new FileReader("demo.txt");
			char[] buf = new char[1024];
			//将数据存储到buf数组中，并返回长度
			int length = fr.read(buf);
			while(length > -1) {
				System.out.println(new String(buf,0,length));
			}
			fr.close();
			
		}
	
	}

#### (五)、练习

读取一个文件数据，并复写到另一个文件。

#### 1、逐个字符读写

	public class Demo4 {
	
		public static void main(String[] args) throws IOException {
			
			//字符读取流
			FileReader fr = new FileReader("demo.txt");
			
			//字符写入流
			FileWriter fw = new FileWriter("copy.txt");
			
			//读写
			int ch = 0;
			while((ch=fr.read()) > -1) {
				fw.write(ch);
			}
			
			//关闭流
			fr.close();
			fw.close();
			
		}
	
	}

#### 2、数组读写

	public class Demo5 {
		
		private static final int BUFFER_SIZE = 1024;
		
		public static void main(String[] args) {
			FileReader fr = null;
			FileWriter fw= null;
			
			try {
				//读写流
				fr = new FileReader("demo.txt");
				fw = new FileWriter("copy.txt");
				
				//临时容器
				char[] buf = new char[BUFFER_SIZE];
				
				//定义变量记录读取到的字符，就是往数组里装的字符数
				int length = 0;
				while((length = fr.read(buf)) != -1) {
					fw.write(buf,0,length);
				}
				
			} catch (IOException e) {
				throw new RuntimeException("读写失败");
			} finally {
				if(fr != null)
					try {
						fr.close();
					} catch (IOException e) {
						e.printStackTrace();
					}
				if(fw != null)
					try {
						fw.close();
					} catch (IOException e) {
						e.printStackTrace();
					}
			}
		}
	
	}