---
title: 常用对象API-StringBuffer类
date: 2017-12-22 14:58:18
tags: api
category: java
keywords: java, api, stringBuffer
description: java api StringBuffer类
---

StringBuffer就是字符串缓冲区，用于存储数据的容器，初始被容量16个字符。

#### 1、特点

(1)、长度是可变的

(2)、可以存储不同类型的数据

(3)、最终转成字符串进行使用

(4)、可以对字符串进行插入修改

#### 2、方法

(1)、添加 返回StringBuffer类型的对象

	StringBuffer append(data)
	
	StringBuffer sb = new StringBuffer();
	sb.append(4);
	sb.append(true);
	sb.insert(1, false);
	System.out.println(sb); //4falsetrue输出字符串

(2)、指定位置插入
	
	insert(index, data) 

(3)、删除 返回StringBuffer类型的对象
		
	StringBuffer delete(start, end)，包含头，不包含尾
	
	StringBuffer deleteCharAt(int index)，删除指定位置元素
	
	StringBuffer sb = new StringBuffer("abce");
	sb.delete(1, 3);
	System.out.println(sb);//ae
	
(4)、查找

	char charAt(index);
	int indexOf(stirng);
	int lastIndexOf(string);
	
(5)、修改

	StringBuffer replace(int start, int end, string str)

	void setCharAt(index, char);	

	sb.replace(1,3,"nba");//anbae
	
	sb.setCharAt(2, '1');//abqe	
	
#### 3、StringBuilder类

jdk1.5以后出现了功能和StringBuffer一样的对象。就是StringBuilder。

不同的是：

StringBuffer是线程同步的，通常用于多线程。

StringBuilder是线程不同步的，通常用于单线程，提高效率。

	public class StringBuilderTest {

		public static void main(String[] args) {
			int[] arr = {3,1,5,1,2};
			String s = arrayToString(arr);
			System.out.println(s);
		}
		
		public static String arrayToString(int[] arr) {
			StringBuilder sb = new StringBuilder();
			sb.append("[");
			for(int i= 0; i<arr.length; i++) {
				if(i!=arr.length - 1)
					sb.append(arr[i]+", ");
				else
					sb.append("]");
			}
			return sb.toString();
		}
		
		public static String arrayToString2(int[] arr) 		{
			String str = "[";
			for(int i=0; i<arr.length; i++) {
				if(i!=arr.length - 1)
					str+=arr[i]+", ";
				else
					str+=arr[i]+"]";
			}
			return str;
		}
	}

字符串拼接的方式会在字符串常量池中生成多个字符串。因此，推荐用缓冲区容器的方式。

