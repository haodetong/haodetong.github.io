---
title: api-string
date: 2017-12-21 09:39:07
tags: api
category: java
keywords: java, api, string
description: java api string类
---

### String类

#### 1、创建对象

	String s = "abc";//常量池中创建一个对象

	String s1 = new String("abc");//堆内存中创建两个对象，一个string类型的对象，一个“abc”对象

	System.out.println(s == s1);//false，比较地址

	System.out.println(s.equals(s1));//true,object中的equals方法比较的是地址，但object类中equals方法比较的是内容。

#### 2、构造函数

通过数组创建字符串

	byte[] arr = {64, 66, 67, 68};
	String s = new String(arr);
	System.out.println(s);//ABCD


截取数组中的一部分，创建字符串

	char[] arr = {'a', 'b', 'c', 'd'}；
	String s = new String(arr, 1, 3);
	System.out.println(s);//bcd

#### 3、获取

1、length() 长度

2、charAt(int index) 根据索引获取字符

3、根据字符获取索引

	indexOf(int ch) 
	
	indexOf(int ch, int fromIndex)
	
	indexOf(String str)
	
	indexOf(String str, fromIndex)
	
	lastIndexOf(int ch)
	
	lastIndexOf(int ch, int fromIndex)
	
	lastIndexOf(String str)
	
	lastIndexOf(String str, int fromIndex)

#### 4、获取字符串中的一部分字符串

	subString(int beginIndex, int endIndex)//包括开始，不包括结束
	
	subString(int beginIndex)

#### 5、转换

(1)、String[] split 切割，将字符串转为字符串数组
	
	split(String regex)//切割，正则，将字符串转为数组
	
	String str = "张三.李四.王五";
	
	String[] arr = s.split("\\.");
	
(2)、char[] toCharArray 切割，将字符串转为字符数组
	
(3)、byte[] getBytes 将字符串变成字节数组

(4)、字符串中的字母大小写转换

	String toUpperCase(): 大写
	String toLowerCase(): 小写
	
(5)、将字符串中的内容进行替换

	String replace(char oldch, char newch)
	
	String replace(String s1, String s2)

(6)、去掉字符串两端空格

	String trim()
	
(7)、将字符串进行连接

	String concat(String);

(8)、将基本数据类型转换为字符串

	String valueOf(int short byte long double float)
	
#### 6、判断

(1)、两个字符串内容是否相同

	boolean equals(Object obj)
	
	boolean equalsIgnoreCase(String str);//忽略大小写

(2)、字符串中是否包含指定字符串

	boolean contains(String str)

(3)、字符串是否以指定字符串开头/结尾

	boolean startsWith(String str);
	
	boolean endsWith(String str);
	
#### 7、比较

int compareTo(String anotherString)

比较两个字符串中各个字符的Unicode值。

此字符串按字典顺序小于字符串参数，返回小于0的值，大于的话，返回一个大于0的值，相等的话，返回0。

#### 8、练习

(1)、字符串数组排序

	public static void main(String[] args){
		String[] arr = {"nba","abc","hjk","efs","wyn",};
		printArray(arr);
		sortString(arr);
		printArray(arr);
	}
	
	public static void printArray(String[] arr){
		System.out.print("[");
		for(int i=0; i<arr.length; i++){
			System.out.print(arr[i]+", ");
		}
		System.out.print("]");
	}
	
	public static void sortString(String[] arr){
		for(int i=0; i< arr.length-1; i++){
			for(int j=i+1; j< arr.length; j++){
				if(arr[i].compareTo(arr[j])>0)//字符串比较 用compareTo方法
					swap(arr,i,j);
			}
		}
	}
	
	public static void swap(String[] arr, int i, int j){
		String temp = arr[i];
		arr[i] = arr[j];
		arr[j] = temp;	
	}

(2)、一个子串在整串中出现的次数

方法一：

通过indexOf获取第一次出现的位置。

记录出现的位置，通过subString在剩余的字符串中继续查找子串。

循环查找，直到indexOf返回-1。

	public static int getKeyStringCount(String str, String key){
		int count = 0;
		int index = 0;
		while((index = str.indexOf(key)) > 0){
			str = str.subString(index+key.length());
			count++;
		}
		return count;
	}
	
缺点：每次取子串，字符串常量池中都会增加字符串。

方法二：

记录每一次出现的位置，下一次查找，通过indexOf(String str, fromindex)方法，从该位置继续往下查找，直到返回-1。
	
	public static int getKeyStringCount(String str, String key){
		int count = 0;
		int index = 0;
		while((index = str.indexOf(key,index)) > 0){
			index = index + key.length();
			count++;
		}
		return count;
	}

(3)、两个字符串中最大相同的子串

	public static void main(String[] args){
		String s1 = "jskkskabcdlslslsl";
		String s2 = "xjxabcdjas";
		String s = getMaxSubstring(s1, s2);
		System.out.println("s="+s);
	}
	
	public static String getMaxSubstring(String s1, String s2){
		String max = null, min = null;
		max = (s1.length()>s2.length())?s1:s2;
		min = max.equals(s1)?s2:s1;
		for(int i=0; i<min.length(); i++){
			for(int a=0, b=s2.length()-i; b!=s2.length()+1, a++, b++){
				String sub = s2.subString(a, b);
				if(s1.contains(sub))
					return sub;
			}
		}
		return null;
	}
	