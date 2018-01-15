---
title: IO流 - 十一、打印流
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

打印流字符流与字节流

PrintWriter与PrintStream

可以直接操作输出流和文件


#### 1、PrintStream 字节打印流

	/**
		PrintStream:
		1、提供了打印方法可以对多种数据类型值进行打印，并保持数据的表现形式。
		2、它不抛IOException
		构造函数，接收三种类型的值：
		1、字符串路径
		2、File对象
		3、字节输出流
	*/
	610: 0000-0000 0000-0000 0000-0010 0110-0001
	PrintStream out = new PrintStream("print.txt");
	out.write(610);//b ,只写最低8位
	out.print(97);//97，将97先变成字符，保持原样形式打印到目的地。
	out.close;

#### 2、PrintWriter 字符打印流

	/**
		构造函数参数：
		1、字符串路径
		2、File对象
		3、字节输出流
		4、字符输出流
	*/
	
	
	public class PrintWriterDemo1 {

		public static void main(String[] args) throws IOException {
			//键盘输入流
			BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
			
			//打印流,自动刷新缓冲区
			PrintWriter out = new PrintWriter(new FileWriter("out.txt"),true);
			
			String line = null;
			while((line = bufr.readLine()) != null) {
				if("over".equals(line))
					break;
				out.println(line.toUpperCase());
			}
			out.close();
			bufr.close();
		}
	
	}
