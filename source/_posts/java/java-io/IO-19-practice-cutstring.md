---
title: IO流 - 十九：练习 - 按字节截取字符串
date: 2018-01-15 10:52:46
tags: IO
category: java
keywords: java,io
description: java-IO流
---

练习

	public class CutStringByByte {
	
		public static void main(String[] args) throws UnsupportedEncodingException {
			/**
			 * "ab你好"
			 * 97 98 -60 -29 -70 -60
			 * 
			 * 在java中，字符串"abcd"与字符串"ab你好"的长度是一样的，都是四个字符。
			 * 但是，对应的字节数不同，一个汉字占两个字节。
			 * 定义一个方法：按照最大的字节数来截取子串。
			 * 如："ab你好"，如果取三个字节，那么子串就是ab与你的半个字节，半个字节需要舍弃
			 * 如果取四个字节，就是"ab你"，取五个字节还是舍弃半个字节，还是"ab你"。
			 */
			String str = "ab你好cd谢谢";
			int length = str.getBytes("gbk").length;
			for(int x=0; x<length; x++) {
				System.out.println("截取"+(x+1)+"个字节结果是："+cutStringByByte(str, x+1));
			}
			
			System.out.println("=================================");
			
			/**
			 * utf-8 一个汉字对应三个字节，而且都是负数
			 * 
			 */
			String str2 = "ab你好cd谢谢";
			int length2 = str2.getBytes("utf-8").length;
			for(int x=0; x<length2; x++) {
				System.out.println("截取"+(x+1)+"个字节结果是："+cutStringByutf8(str2, x+1));
			}
			
		}
		
		private static String cutStringByutf8(String str, int length) throws UnsupportedEncodingException {
			byte[] buf = str.getBytes("utf-8");
			
			//从后向前计算负数（代表汉字的字节）的个数
			int count = 0;
			for(int x = length-1; x>=0; x--) {
				if(buf[x]<0)
					count++;
				else
					break;
			}
			
			if(count%3 == 0)
				return new String(buf,0,length,"utf-8");
			else if(count%3 == 1)
				return new String(buf,0,length-1,"utf-8");
			else
				return new String(buf,0,length-2,"utf-8");
		}
	
		public static String cutStringByByte(String str, int length) throws UnsupportedEncodingException {
			byte[] buf = str.getBytes("gbk");
			
			//从后向前计算负数（代表汉字的字节）的个数
			int count = 0;
			for(int x = length-1; x>=0; x--) {
				if(buf[x]<0)
					count++;
				else
					break;
			}
			//偶数个负数
			if(count%2 == 0)
				return new String(buf,0,length,"gbk");
			//奇数个负数
			else
				return new String(buf,0,length-1,"gbk");
		}
	
	}
