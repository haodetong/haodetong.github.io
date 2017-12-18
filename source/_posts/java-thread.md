---
title: java多线程
date: 2017-12-15 10:47:02
tags: thread
category: java
keywords: java, thread
description: java多线程
---

#### 创建线程

##### 创建线程方式一：继承thread类。

步骤：

1、定义一个子类继承Thread类。

2、覆盖Thread类中的run方法。

3、直接创建Thread的子类对象创建线程。

4、调用start方法开启线程并调用线程的任务run方法执行。

	class Demo extends Thread{
		private String name;
		Demo(String name){
			this.name = name;
		}
		public void run(){
			for(int x=0; x<10; x++){
				System.out.println(name+"...x="+x);
			}
		}
	}

	class ThreadDemo2{
		public static void main(String[] args){
			Demo d1 = new Demo("张三");
			Demo d2 = new Demo("李四");
			d1.start();
			d2.start();
		}
	}
	

##### 创建线程方式二：实现Runnable接口。

步骤：

1、定义类实现Runnable接口。

2、覆盖接口中的run方法，将线程的任务代码封闭到run方法中。

3、通过Thread类创建线程对象，并将实现Runnable接口的子类对象，作为Thread类的构造函数的参数进行传递。
	
	为什么？因为线程的任务都封装在Runnable接口子类对象的run方法中。
	所以要

4、调用线程对象的start方法开启线程。

	class Demo implements Runnable{
		public void run(){
			show();
		}
		public void show(){
			for(int x=0; x<20; x++){
				system.out.println(Thread.currentThread().getname()+"........"+x);
			}
		}
	}
	
	class TreadDemo{
		public static void main(sting[] args){
			Demo d = new Demo();
			Thread t1 = new Thread(d);
			Thread t2 = new Thread(d);
			t1.start;
			t2.start;
		}
	}
	
##### 线程安全隐患排查原则

1、查找共享数据

2、共享数据代码的操作不止一条

事例：两个储户到银行存钱，每次存100，共存三次。银行总数从100到600递增。

	class Bank{
		private int sum;
		public void add(int num){
			sum = sum + num;
			try{
				Thread.sleep();
			}catch(InterruptedException e){
				//...
			}
			System.out.println("sum="+sum);
		}
	}
	
	class Cus implements Runnable{
		private b = new Bank();
		public void run(){
			for(int x=0; x<3; x++){
				b.add(100);
			}
		}
	}
	
	class BankDemo{
		public static void main(String[] args){
			Cus c = new Cus();
			Thread t1 = new Thread(c);
			Thread t2 = new Thread(c);
			t1.start();
			t2.start();
		}
	}
	
	
##### 分析多线程安全隐患：
	
共享数据有：private b
	
执行代码有一句：b.add(100);
	
b.add里有一个共享数据：private int sum;
	
执行语句有两句：sum = sum + num; system.out.printls...;
	
此处会产生安全隐患：比如第一次当线程0进来时，sum = 100，但没有执行system.out.println语句，第二次线程1进来，此时sum = 100+100,再执行线程0和线程1的输出语句时，就都成了200。
	
##### 解决方式一：

用同步代码块把产生线程安全隐患的代码封装，注意要保持多线程用的是同一个锁。
	
	
	class Bank{
		private int sum;
		private Object obj = new Object();
		public void add(int num){
			synchronized(obj){//三个线程共用一个对象锁obj
				sum = sum + num;
				try{
					Thread.sleep();
				}catch(InterruptedException e){
					//...
				}
				System.out.println("sum="+sum);
			}
		}
	}

##### 解决方式二：

同步函数

	class Bank{
		private int sum;
		public synchronized void add(int num){//同步函数
			sum = sum + num;
			try{
				Thread.sleep();
			}catch(InterruptedException e){
				//...
			}
			System.out.println("sum="+sum);
		}
	}

##### 同步的好处和弊端

好处：解决了线程的安全问题

弊端：降低了效率，因为同步外的线程都会判断同步锁。

##### 同步函数和同步代码块的区别：

1、同步函数使用的锁是this，同步代码块的锁是任意的对象。

2、建议使用同步代码块。

##### 静态同步函数的锁

静态同步函数的锁是该函数所属的字节码文件对象

该对象可以用this.getClass()方法获取，也可以用当前类名.class获取。

	class Bank{
		private int sum;
		public static synchronized void add(int num){
			sum = sum + num;
			try{
				Thread.sleep();
			}catch(InterruptedException e){
				//...
			}
			System.out.println("sum="+sum);
		}
	}

该静态同步函数add的同步锁即为该函数所属的字节码文件对象，即为Bank.class。

##### 单例模式涉及的多线程问题

	//饿汉式
	class Single{
		private static final Singles = new Single();
		private Single(){};
		public static Single getInstance(){
			return s;
		}
	}
	
	//懒汉式
	class Single{
		private static final Singles = null;
		private Single(){};
		public static Single getInstance(){
			if(s == null)
				s = new Single();
				return s;
		}
	}
	
	class SingleDemo{
		public static void main(String[] args){
			
		}
	}

饿汉式多线程安全隐患分析：

如果把getInstance函数封装为多线程的任务，1、多线程存在共享数据s；2、只有一条语句return s；因此，不会存在安全隐患。

懒汉式多线程安全隐患分析：

如果把getInstance函数封装为多线程的任务，1、多线程存在共享数据s；2、有多条语句；因此，存在安全隐患。比如：线程0进来时，s == null，挂住了，线程1双进来了，s == null，挂住了。线程0取得执行权，生成对象并返回。线程1又取得执行权，又生成了对象并返回。就不能保证单例设计模式对象的唯一性了。

因此，懒汉式需要同步函数。

	public static synchronized Single getInstance(){
		if(s == null)
			s = new Single();
			return s;
	}

解决了安全问题，但是每一次线程进来时都会判断同步锁是否一样，降低了效率。

同步代码块解决安全问题以及效率问题。

	public static Single getInstance(){
		if(s == null){ //a处的if
			synchronized(Single.class){
				if(s == null) //b处的if
					s = new Single();
					return s;
			}
		}
		return s;
	}
	
分析：线程0进入getInstance，判断a处的s == null,进入if，挂起。线程1进入判断a处的s == null，进入if挂起。线程0取得执行权，判断b处的s == null，生成对象并返回。线程1取得执行权，判断b处的s已经不为null了，跳出if。线程2进入getInstance，判断a处的s已经不为null了，直接return s。

##### 多线程死锁事例

	class Test implements Runnable{
		private boolean flag = true;
		public void run(){
			if(flag){
				while(true)
					synchronized(MyLock.locka){
						System.out.prinln(Thread.currentThread().getName()+"if...locka...");
					synchronized(MyLock.lockb){
							System.out.prinln(Thread.currentThread().getName()+"if...lockb...");					
						}
					}
			}else{
				while(true)
					synchronized(MyLock.lockb){
						System.out.prinln(Thread.currentThread().getName()+"else...lockb...");	
					synchronized(MyLock.locka){
							System.out.prinln(Thread.currentThread().getName()+"else...locka...");	
						}
					}
			}
		}
	}
	
	class MyLock{
		public static final Object locka = new Object();
		public static final Object lockb = new Object();
	}
	
	class DeadLockTest{
		public static void main(String[] args){
			Test a = new Test();
			Thread t1 = new Thread(a);
			Thread t2 = new Thread(a);
			t1.start();
			try{
				Thread.sleep(10);//冻结main主线程10毫秒
			}catch(){
				//...
			}
			a.flag = false;
			t2.start();
		}
	}


