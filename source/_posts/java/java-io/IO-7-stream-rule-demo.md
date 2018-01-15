---
title: IO流 - 七、流的操作规律 - 需求体现
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

#### 1、需求：复制一个文本文件

	FileReader fr = new FileReader("a.txt");
	
	FileWriter fw = new FileWriter("b.txt");
	
	BufferedReader bufr = new BufferedReader(fr);
	
	BufferedWriter bufw = new BufferedWriter(fw);

#### 2、需求：读取键盘录入信息，写入到文件中

	源：InputStream in = System.in;
	
	转换流：InputStreamReader isr = new InputStreamReader(in);
	
	目的：FileWriter fw = new FileWriter("b.txt");
	
	高效：BufferedReader = new BufferedReader(isr);
	
		 BufferedWriter = new BufferedWriter(fw);
	
#### 3、需求：将一个文本文件显示在控制台上

	源：FileReader fr = new FileReader("a.txt");
	
	读： String line = bufr.readLine();
		
	目的：OutputStream out = System.out;
	
		  OutputStreamWriter osw = new OutputStreamWriter(out);
	
	高效：BufferedReader bufr = new BufferedReader(fr);
	     BufferedWriter bufw = new BufferedWriter(osw);
	    
#### 4、需求：读取键盘录入数据，显示在控制台上

	源：InputStream in = System.in	;
	
	目的：OutputStream out = System.out;
	
	转换流：InputStreamReader isr = new InputStreamReader(in);
			OutputStreamWriter isw = new OutputStreamWriter(out);
		
	高效：BufferedReader bufr = new BufferedReader(isr);
		  BufferedWriter bufw = new BufferedWriter(isw);