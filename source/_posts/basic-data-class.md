---
title: 基本数据类型包装类
date: 2017-12-23 11:28:25
tags: api
category: java
keywords: java, api
description: 基本数据类型包装类

---

#### 1、为了方便操作基本数据类型值，将其封装成了对象，在对象中定义属性和行为，丰富了该数据的操作。

数据类型		 | 包装类
------------- | -------------
byte  			 | Byte
short			 | Short
int				 | Integer
long			 | Long
flot			 | Float
double			 | Double
char			 | Character
boolean		 | Boolean

#### 2、基本数据类型和字符串之间的转换

基本类型-->字符串

(1)、基本类型数值+""

(2)、用String类中的静态方法valueOf(基本类型数值)

字符串-->基本类型

(1)、静态方法 parseInt, parseLong, parseBoolean。。。。

8种基本数据类型中，只有Character没有parse的用法。

(2)、如果字符串被Integer进行了对象的封装。可以使用别一个非静态方法intValue()将一个Integer对象转成基本数据类型值。

	Integer i = new Integer("123");
	System.out.println(i.intValue());

#### 3、进制转换

十进制-->其它进制

	//转二进制
	System.out.println(Integer.toBinaryString(60));
	System.out.println(Integer.toString(60,2));
	
	//转八进制
	System.out.println(Integer.toOctalString(60));
	System.out.println(Integer.toString(60,8));
	
	//转十六进制
	System.out.println(Integer.toHexString(60));
	System.out.println(Integer.toString(60,16));
	
其它进制-->十进制 parseInt("string", radix)
	
	//二进制转十进制
	System.out.println(Integer.parseInt("110",2));
	
	//八进制转十进制
	System.out.println(Integer.parseInt("74",8));
	
	//十六进制转十进制
	System.out.println(Integer.parseInt("3c",16));

#### 4、自动装箱拆箱
	
	Integer i = new Integer(4);
	jdk1.4以后简写为Integer i = 4; //自动装箱
	
	i = i + 6;
	是i = new Integer(i.intValue()+6)的简写，自动拆箱。
	
#### 5、小结

Integer a = new Integer(127);
Integer b = new Integer(127);

System.out.println(a==b);//fasle,对象比较地址
System.out.println(a.equals(b));//true，比较内容

Integer x = 129;
Integer y = 129;

System.out.println(x==y);//fasle
System.out.println(x.equals(y));//true

Integer x = 127;
Integer y = 127;

System.out.println(x==y);//true
System.out.println(x.equals(y));//true

x,y都为127时，x==y为true，都为129时，x==y为false

是因为jdk1.5以后，自动装箱时，如果装箱的在一个字节空间内（-128~127）,那么该数据会被共享，不会重新开辟空间，因此都为127时，比较x==y时，实际上是一个地址。

#### 6、练习

对字符串中的数值排序

	import java.util.Arrays;
	
	public class WrapperTest {
		private static final String SPACE_SEPARATOR = " ";
		
		public static void main(String[] args) {
			String numStr = "20 83 32     11 -7 50";
			System.out.println(numStr);
			numStr = sortStringNumber(numStr);
			System.out.println(numStr);
		}
	
		private static String sortStringNumber(String numStr) {
			//字符串变成字符串数组
			String[] str_arr = stringToArray(numStr);
			
			//字符串数组变成int数组
			int[] num_arr = toIntArray(str_arr);
			
			//int数组排序
			mySortArray(num_arr);
			
			//排序后的int数组变成字符串
			String temp = arrayToString(num_arr);
			
			return temp;
		}
	
		private static String arrayToString(int[] num_arr) {
			StringBuilder sb = new StringBuilder();
			for(int i=0; i<num_arr.length; i++) {
				if(i!=num_arr.length-1)
					sb.append(num_arr[i]+SPACE_SEPARATOR);
				else
					sb.append(num_arr[i]);
			}
			return sb.toString();
		}
	
		private static void mySortArray(int[] num_arr) {
			Arrays.sort(num_arr);
		}
	
		private static int[] toIntArray(String[] str_arr) {
			int[] arr = new int[str_arr.length];
			for(int i=0; i<arr.length; i++) {
				arr[i] = Integer.parseInt(str_arr[i]);
			}
			return arr;
		}
	
		public static String[] stringToArray(String numStr) {
			String[] str_arr = numStr.split(SPACE_SEPARATOR+"+");
			return str_arr;
		}
	
	}

