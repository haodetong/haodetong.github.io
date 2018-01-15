---
title: IO流 - 六、流的操作基本规律
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

转换流：

InputStreamReader:字节到字符的桥梁，解码。

OutputStramWriter:节符到字节的桥梁，编码。

操作规律：找到应该用哪些对象

1、明确源和目的

	源：InputStream Reader
	
	目的：OutputStream Writer
	
2、明确数据是否是纯文本数据

	源：是纯文本数据：Reader；不是纯文本数据：InputStream
	
	目的：是纯文本数据：Writer；是纯文本数据：OutputStream
	
3、明确具体的设备

源设备
	
	硬盘：File
	键盘：System.in
	内存：数组
	网络：Socket流

目的设备
	硬盘：File
	控制台：System.out
	内存：数组
	网络：Socket流
	
4、是否需要其它功能（装饰类）
	
	1、高效缓冲区：Buffer
	
	...
