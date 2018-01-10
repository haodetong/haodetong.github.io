---
title: 其它对象API
date: 2018-01-09 15:16:19
tags: api
category: java
keywords: java, api
description: java其它对象api
---

### 一、System类

System类中的方法和属性都是静态的。
  
常见方法：
  
long currentTimeMillis():获取当前时间的毫秒值
  
Properties getProperties():获取当前系统的属性信息	

	public class SystemApi {
		public static void main(String[] args) {
			
			long millisTime = System.currentTimeMillis();
			System.out.println(millisTime);
			
			Properties prop = System.getProperties();
			
			Set<String> nameSet = prop.stringPropertyNames();
			
			for(String name:nameSet) {
				String value = prop.getProperty(name);
				System.out.println(name+"::"+value);
			}
			
			
		}
	}

### 二、Runtime类

Runtime:

没有构造方法摘要，说明该类不可以创建对象。

又发现该类有非静态方法，说明该类应该有提供的静态的返回该类对象的方法。

而且只有一个这种方法，说明Runtime类使用了单例设计模式。

	public class RuntimeApi {
	
		public static void main(String[] args) throws IOException, InterruptedException {
			
			Runtime r = Runtime.getRuntime();
			
			Process p = r.exec("open -a Safari.app http://www.baidu.com");
			Thread.sleep(2000);
			p.destroy();
		}
	
	}

### 三、Math类

常用方法：

double ceil():返回大于参数的最小整数。

double floor():返回小于参数的最大整数。

double round():返回四舍五入的整数。

max(a,b):返回最大值

pow(a,b):a的b次方

double random():返回大于等于0.0小于1.0的随机double值，与java.util中的Random类相似。

### 四、Date类

#### （一）、日期对象和毫秒值之间的转换

##### 毫秒值-->日期对象

1、通过Date对象的构造方法 new Date(timeMills);

2、通过setTime方法。

目的是通过Date对象的方法，对该日期中的年月日等字段进行操作。

##### 日期对象-->毫秒值

1、通过getTime方法。

目的是通过具体的数值进行运算。

	public static void thord1() {
			//long time = System.currentTimeMillis();//1515489394135
			
			Date date1 = new Date();//将当前日期和时间封装成Date对象
			System.out.println(date1);//Tue Jan 09 17:16:34 CST 2018
			
			Date date2 = new Date(1515489367117l);//将指定毫秒封装成Date对象
			System.out.println(date2);//Tue Jan 09 17:16:07 CST 2018
	}

#### （二）、日期对象转成字符串

	public class DateDemo {
	
		public static void main(String[] args) {
			
			Date date = new Date();
			
			//获取日期格式对象。具备着默认的风格
			DateFormat dateFormat = DateFormat.getDateInstance();
			String str_date = dateFormat.format(date);
			System.out.println(str_date); //Jan 9, 2018
			
			DateFormat dateFormat2 = DateFormat.getDateTimeInstance();
			String str_date2 = dateFormat2.format(date);
			System.out.println(str_date2);//Jan 9, 2018 5:48:43 PM
			
			//其它风格 FULL LONG SHORT MEDIUM
			//DateFormat dateFormat3 = DateFormat.getDateInstance(DateFormat.FULL);//Tuesday, January 9, 2018
			
			//DateFormat dateFormat3 = DateFormat.getDateInstance(DateFormat.LONG);//January 9, 2018
			
			//DateFormat dateFormat3 = DateFormat.getDateInstance(DateFormat.SHORT);//1/9/18
			
			DateFormat dateFormat3 = DateFormat.getDateInstance(DateFormat.MEDIUM);//Jan 9, 2018
			
			String str_date3 = dateFormat3.format(date);
			System.out.println(str_date3);
			
			//日期和时间
			DateFormat dateFormat4 = DateFormat.getDateTimeInstance(DateFormat.LONG,DateFormat.LONG);//Jan 9, 2018
			
			String str_date4 = dateFormat4.format(date);
			System.out.println(str_date4);
			
			//自定义风格
			DateFormat dateFormats = DateFormat.getDateInstance();
			dateFormats = new SimpleDateFormat("yyyy-MM-dd");
			String str_date5 = dateFormats.format(date);
			System.out.println(str_date5);//2018-01-09
			
			
		}
	}
	
#### （三）、字符串转成日期对象

使用的是DateFormat类中的parse方法。

	public class DateFormatDemo {
	
		public static void main(String[] args) throws ParseException {
			
			//默认的日期字符串格式转成日期对象
			String str_date = "Jan 9, 2018 5:48:43 PM";
			DateFormat dateFormat = DateFormat.getDateInstance();
			Date date = dateFormat.parse(str_date);
			System.out.println(date);
			
			//将自定义的日期字符串格式转成日期对象
			String str_date2 = "2018--1--10";
			DateFormat dateFormat2 = DateFormat.getDateInstance(DateFormat.LONG);
			dateFormat2 = new SimpleDateFormat("yyyy--MM--dd");
			Date date2 = dateFormat2.parse(str_date2);
			System.out.println(date2);
			
		}
	
	}

#### （四）、Date类 - 练习

	public class Demo1 {
	
		public static void main(String[] args) throws ParseException {
			
			/**
			 * 练习：
			 * "2017.12.20"到"2018.1.10"中间相隔多少天
			 * 
			 * 思路：
			 * 1、日期字符串转日期对象
			 * 2、日期对象方法获取毫秒数
			 * 3、毫秒数相减，再计算天数
			 */
			
			String str_date1 = "2017.12.20";
			String str_date2 = "2018.1.10";
			
			//DateFormat dateFormat = DateFormat.getDateInstance();
			SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy.MM.dd");
			Date date1 = dateFormat.parse(str_date1);
			Date date2 = dateFormat.parse(str_date2);
			
			long date1Millis = date1.getTime();
			long date2millis = date2.getTime();
			
			int day = (int) Math.abs(date1Millis - date2millis)/1000/60/60/24;
			
			
			System.out.println(day);
		}
	
	}

### 四、Calendar类

常见方法：

set()

get()

add():偏移

##### 一、基本演示
	public class CalendarDemo {
	
		public static void main(String[] args) {
			
			Calendar c = Calendar.getInstance();
			int year = c.get(Calendar.YEAR);
			int month = c.get(Calendar.MONTH)+1;
			int day = c.get(Calendar.DAY_OF_MONTH);
			int week = c.get(Calendar.DAY_OF_WEEK);
			
			System.out.println(year+"年"+month+"月"+day+"日"+getWeek(week));
			
		}
	
		private static String getWeek(int i) {
			String[] weeks = {"","星期日","星期一","星期二","星期三","星期四","星期五","星期六"};
			return weeks[i];
		}
	}

##### 二、练习

	public class CalendarDemo {
	
		public static void main(String[] args) {
			
			Calendar c = Calendar.getInstance();
			//指定日期
			c.set(2018, 0, 20);
			showDate(c);
			
			//日期偏移
			c.add(Calendar.YEAR, 2);
			showDate(c);
			
		}
	
		public static void showDate(Calendar c) {
			//Calendar c = Calendar.getInstance();
			int year = c.get(Calendar.YEAR);
			int month = c.get(Calendar.MONTH)+1;
			int day = c.get(Calendar.DAY_OF_MONTH);
			int week = c.get(Calendar.DAY_OF_WEEK);
			
			System.out.println(year+"年"+month+"月"+day+"日"+getWeek(week));
		}
	
		private static String getWeek(int i) {
			String[] weeks = {"","星期日","星期一","星期二","星期三","星期四","星期五","星期六"};
			return weeks[i];
		}
	
	}
