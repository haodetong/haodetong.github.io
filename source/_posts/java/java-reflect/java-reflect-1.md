---
title: java反射机制
date: 2018-01-17 19:06:09
tags: reflect
category: java
keywords: java, reflect
description: java reflect

---

#### 1、概述

java反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法，对于任意一个类，都能够调用它的任意方法和属性，这种动态的获取信息以及动态调用对象的方法的功能就称为java语言的反射机制。

可以理解为对类的解剖。

#### 2、获取Class类的对象的三种方式

（1）、Object类中的getClass()方法

	Person p = new Person();
	Class pc = p.getClass();

（2）、通过静态属性class

	Class pc = Person.class;

（3）、通过Class类中的forName方法
	
	String className = "src/reflect/Person";//带包的路径
	Class pc = Class.forName(className);

#### 3、获取Class中的构造函数
	
	class Person {
		public String name;
		private int age;
		Person() {
			
		}
		Person(int age, String name) {
			this.name = name;
			this.age = age;
		}
	}

	//字节码文件对象，class对象的类
	Class pc = Class.forName(className);
	
	//生成该字节码文件对象，class对象，调用的是空参的构造函数
	Object obj = pc.newInstance();

	//实参的构造函数，生成对象，先获取构造函数
	Constructor ct = pc.getConstructor(int.class,String.class);
	
	//通过构造器对象的newInstance方法生成对象
	Object obj = ct.newInstance(38,"小明");

#### 4、获取Class中的字段

	//字节码文件对象，class对象的类
	Class pc = Class.forName(className);
	
	//只能获取public字段
	Field nameField = pc.getField("name");
	
	//能获取public和private字段
	Field ageField = pc.getDeclaredField("age");
	
	//对私有字段的访问取消权限检查，暴力访问
	ageField.setAccessible(true);
	
	//生成对象
	Object obj = pc.newInstance();
	
	//age赋值
	ageField.set(obj,22);
	
	//age取值
	Object ageValue = field.get(obj);

	syso(ageValue);//22

#### 5、获取Class中的方法

	//字节码文件对象，class对象的类
	Class pc = Class.forName(className);

	//获取所有的public方法，包含父类中的
	Method[] methods = pc.getMethods();
	
	//获取本类中所有的public和private方法，不包含父类中的
	methods = pc.getDeclaredMethods();
	
	//获取指定的空参方法show，null代表空参
	Method showMethod = pc.getMethod("show",null);
	
	//生成对象
	Object obj = pc.newInstance();
	
	//调用show方法，null代表实参
	showMethod.invoke(obj,null);
	
	//获取指定的带参数的方法paramMethod
	Method paramMethod = pc.getMethod("paramMethod", String.class, int.class);
	
	//调用paramMethod方法
	paramMethod.invoke(obj,"小强",22);
	
#### 6、反射练习


	public class ReflectPractice {
		public static void main(String[] args) throws IOException, ReflectiveOperationException {
			//主板开启
			Mainboard mb = new Mainboard();
			mb.run();
			
			//加载声卡，网卡等设备
			//读取配置文件，获取设备类名称
			File configFile = new File("pci.properties");
			if(!configFile.exists())
				configFile.createNewFile();
			Properties prop = new Properties();
			FileInputStream fis = new FileInputStream(configFile);
			prop.load(fis);
			for(int x=0; x<prop.size(); x++) {
				String pciName = prop.getProperty("pci"+(x+1));
				//根据设备名，获取该设备字节码文件对象
				Class clazz = Class.forName(pciName);
				//实例化设备对象
				PCI p = (PCI)clazz.newInstance();
				//运行设备
				mb.usePCI(p);
			}
			fis.close();
		}
	}
	
	public interface PCI {
		public void open();
		public void close();
	}	
	
	public class Mainboard {
	
		public void run() {
			System.out.println("mainboard run");
		}
		
		public void usePCI(PCI p) {
			if(p != null) {			
				p.open();
				p.close();
			}
		}
	
	}
	
	public class NetCard implements PCI {

		@Override
		public void open() {
			System.out.println("netcard open");
		}
	
		@Override
		public void close() {
			System.out.println("netcard close");
		}
	
	}
	
	public class SoundCard implements PCI{

		@Override
		public void open() {
			System.out.println("sound open");
		}
	
		@Override
		public void close() {
			System.out.println("sound close");
		}
	
	}
	
	file pci.properties
	
	pci1=reflect.SoundCard
	pci2=reflect.NetCard
	
	只需要向配置文件中添加设备名称即可，不用再修改mainborad文件，增加了程序的扩展性。
	
	