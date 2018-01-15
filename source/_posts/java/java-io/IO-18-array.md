---
title: IO流 - 十八、操作数组的流
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流
---


#### 1、操作字节数组的流：

源和目的都是内存，没有调用底层资源，关闭流无效，关闭流后仍可用。

ByteArrayInputStream与ByteArrayOutputStream

	public class ArrayStreamDemo1 {
	
		public static void main(String[] args) {
			
			ByteArrayInputStream bis = new ByteArrayInputStream("abcde".getBytes());
			ByteArrayOutputStream bos = new ByteArrayOutputStream();
			int ch = 0;
			while((ch = bis.read()) != -1) {
				bos.write(ch);
			}
			System.out.println(bos.toString());
		}
	
	}

操作字符数组的流：

CharArrayInputStream与CharArrayOutputStream

操作字符串的流

StringReader与StringWriter
