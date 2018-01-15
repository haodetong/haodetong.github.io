---
title: IO流 - 十七、操作基本数据类型的流对象 - DataStream
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流
---

DataInputStream与DataOutputStream

	public class DataStream {
	
		public static void main(String[] args) throws IOException {
			
			writeData();
			readData();
			
		}
	
		private static void readData() throws IOException {
			DataInputStream dis = new DataInputStream(new FileInputStream("abc.txt"));
			String str = dis.readUTF();
			System.out.println(str);
			dis.close();
		}
	
		private static void writeData() throws IOException {
			DataOutputStream dos = new DataOutputStream(new FileOutputStream("abc.txt"));
			dos.writeUTF("你好");
			dos.close();
		}
	
	}
