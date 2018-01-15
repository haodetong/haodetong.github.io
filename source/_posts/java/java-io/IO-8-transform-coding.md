---
title: IO流 - 八、转换流的编码解码
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

#### 1、OutputStreamWriter转换流。字符转换成字节：编码。

需求：将一个中文字符串按照指定的编码表，写入到一个文本文件中。其实就是需要将内存中的“你好”字符转成字节存到硬盘中。

分析：

明确使用指定编码表，就不可以使用FileWriter，因为FileWriter内部是使用默认的本地编码表。只能使用其父类OutputStreamWriter类。

OutputStreamWriter接收一个字节流对象，既然是操作文本，那么就应该是FileOutputStream。

OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("a.txt"),charsetName);

需要高效

BufferedWriter bufw = new BufferedWriter(osw);
	
	默认编码表：
	FileWriter fw = new FileWriter("a.txt");
	fw.write("你好");
	fw.close;
	
	指定编码表：
	OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("a.txt"),"UTF-8");
	osw.write("你好");
	osw.close;
	

FileWriter fw = new FileWriter("a.txt");

OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("a.txt"),"GBK");

这两句代码的功能是等同的。

FileWriter：其实就是转换流并指定了本机默认编码表，而且是这个转换流的子类，用于方便的操作文本文件。

如果操作文件需要明确具体的编码。FileWriter就不可行了，必须用转换流。

#### 2、InputStreamReader转换流。字节转换成字符：解码。

需求：读取硬盘中的文件内容，到控制台输出。

其实就是通过转换流，读取硬盘中的字节转换为字符到内存，再从内存通过System.out转换流，将字符转换为字节输出到控制台。

	默认编码表：
	FileReader fr = new FileReader("a.txt");
	char[] buf = new char[10];
	int length = fr.read(buf);
	String str = new String(buf,0,length);
	System.out.println(str);
	
	指定编码表：
	InputStreamReader isr = new InputStreamReader(new FileInputStream("a.txt"),""utf-8"");
	char[] buf = new char[10];
	int length = isr.read(buf);
	String str = new String(buf,0,length);
	System.out.println(str);