---
title: thread-communication
date: 2017-12-18 16:42:27
tags: thread
category: java
keywords: java, thread
description: java多线程通信
---

#### 一、多线程通信

多个线程在处理同一资源，但是任务不同。

#### 1、示例

input类：输入

resource类： name、sex

output类：输出

input和output作为两个线程，处理同一个资源resource。

	class Resource{
		String name;
		String sex;
	}
	
	class Input implements Runnable{
		Resource r;
		Input(Resource r){
			this.r = r;
		}
		public void run(){
			int x = 0;
			while(true){
				synchronized(r){//同步锁r与output保持一致
					if(x == 0){
						r.name = "张三";
						r.sex = "男";
					}else{
						r.name = "李四";
						r.sex = "女";
					}
				}
				x = (x+1)%2;
			}
		}
	}
	
	class Output implements Runnable{
		Resource r;
		Input(Resource r){
			this.r = r;
		}
		public void run(){
			while(true){					
				synchronized(r){
					System.out.println(r.name+"...."+r.sex);
				}
			}
		}
	}
	
	class ResourceDemo{
		public static void main(String[] args){
			Resource r = new Resource();
			Input in = new Input(r);
			Output out = new Output(r);
			Thread t1 = new Thread(in);
			Thread t2 = new Thread(out);
			t1.start();
			t2.start();
		}
	}
	

线程分析：

input拿到执行权后，输入张三男，output拿到执行权，输出张三、男，input再拿到执行输入李四女，还是input拿到执行权，输入张三男，覆盖了李四女，output拿到执行权还是输出张三男。因此，无法实现input输入一次，output就输出一次的需求。


#### 2、多线程通信-等待唤醒机制

实现input输入一次，output就紧接着输出一次。

等待唤醒机制涉及的方法：

1、wait()：让线程处于冻结状态，被wait的线程会被存储到线程池中。

2、notify()：唤醒线程池中一个线程（任意）。

3、notifyAll()：唤醒线程池中的所有线程。

4、以上方法都必须定义在同步中。因为这些方法都是用于操作线程状态的方法。必须要明确到底操作的是哪个锁上的线程。

5、r锁即为线程input和output的监视器。

6、为什么操作线程的方法wait、notify、notifyAll定义在了Object类中，而没有定义在Thread类中。因为这些方法是监视器的方法。监视器就是锁。锁可以是任意对象，任意对象都继承自Object类。

	class Resource{
		String name;
		String sex;
		boolean flag = false;
	}
	
	class Input implements Runnable{
		Resource r;
		Input(Resource r){
			this.r = r;
		}
		public void run(){
			int x = 0;
			while(true){
				synchronized(r){//同步锁r与output保持一致
					if(r.flag)
						try{
							r.wait()
						}catch(InterruptedException e){
							//...
						}
					if(x == 0){
						r.name = "张三";
						r.sex = "男";
					}else{
						r.name = "李四";
						r.sex = "女";
					}
					r.flag = true;
					r.notify();
				}
				x = (x+1)%2;
			}
		}
	}
	
	class Output implements Runnable{
		Resource r;
		Input(Resource r){
			this.r = r;
		}
		public void run(){
			while(true){					
				synchronized(r){
					if(!r.flag)
						try{
							r.wait()
						}catch(InterruptedException e){
							//...
						}
						System.out.println(r.name+"...."+r.sex);
						r.flag = false;
						r.notify();
				}
			}
		}
	}
	
	class ResourceDemo{
		public static void main(String[] args){
			Resource r = new Resource();
			Input in = new Input(r);
			Output out = new Output(r);
			Thread t1 = new Thread(in);
			Thread t2 = new Thread(out);
			t1.start();
			t2.start();
		}
	}
	
线程分析：input和output分别取得执行权，并相互唤醒对方。

代码优化：不要直接访问Resource中的变量，提高安全性。

	class Resource{
		private String name;
		private String sex;
		private boolean flag = false;
		public synchronized void set(String name, String sex){
			if(flag){
				try{
						this.wait()
					}catch(InterruptedException e){
						//...
					}
				this.name = name;
				this.sex = sex;
				flag = true;
				this.notify();//同步函数的锁是this
			}
		}
		public synchronized void out(){
			if(!flag){
				try{
						this.wait()
					}catch(InterruptedException e){
						//...
					}
			}
			System.out.println(name+"...."+sex);
			flag = false;
			this.notify();
		}
	}
	
	class Input implements Runnable{
		Resource r;
		Input(Resource r){
			this.r = r;
		}
		public void run(){
			int x = 0;
			while(true){
				if(x == 0){
					r.set("张三","男");
				}else{
					r.set("张三","女");
				}
				x = (x+1)%2;
			}
		}
	}
	
	class Output implements Runnable{
		Resource r;
		Input(Resource r){
			this.r = r;
		}
		public void run(){
			while(true){					
				r.out();
			}
		}
	}
	
	class ResourceDemo{
		public static void main(String[] args){
			Resource r = new Resource();
			Input in = new Input(r);
			Output out = new Output(r);
			Thread t1 = new Thread(in);
			Thread t2 = new Thread(out);
			t1.start();
			t2.start();
		}
	}


#### 3、多线程通信-多生产者多消费者问题

	class Resource{
		private String name;
		private int count = 1;
		private boolean flag = false;
		public synchronized void set(String name){
			if(flag)
				try{
					this.wait()
				}catch(InterruptedException e){
					//...
				}
			this.name = name + count;
			count++;
			System.out.println(Thread.currentThread().getName()+"...生产者..."+this.name);
			flag = true;
			this.notify();//同步函数的锁是this
		}
		public synchronized void out(){
			if(!flag)
				try{
					this.wait()
				}catch(InterruptedException e){
					//...
				}
			System.out.println(Thread.currentThread().getName()+"...消费者..."+this.name);
			flag = false;
			this.notify();
		}
	}
	
	class Producer implements Runnable{
		private Resource r;
		Producer(Resource r){
			this.r = r;
		}
		public void run(){
			while(true){
				r.set("烤鸭");
			}
		}
	}
	
	class Consumer implements Runnable{
		private Resource r;
		Producer(Resource r){
			this.r = r;
		}
		public void run(){
			while(true){
				r.out();
			}
		}
	}
	
	//只有一个生产者和一个消费者，安全
	class ProducerConsumerDemo{
		public static void main(String[] args){
			Resource r = new Resource();
			Producer pro = new Producer();
			Consumer con = new Consumer();
			Thread t1 = new Thread(pro);
			Thread t2 = new Thread(con);
			t1.start();
			t2.start();
		}
	}
	
	//存在多个生产者和多个消费者，存在安全隐患
	class ProducerConsumerDemo{
		public static void main(String[] args){
			Resource r = new Resource();
			Producer pro = new Producer();
			Consumer con = new Consumer();
			Thread t1 = new Thread(pro);
			Thread t2 = new Thread(pro);
			Thread t3 = new Thread(con);
			Thread t4 = new Thread(con);
			t1.start();
			t2.start();
			t3.start();
			t4.start();
		}
	}
	
线程分析：只有一个生产者，一个消费者时，没有问题。但是当有多个生产者和多个消费者是会存在安全隐患，甚至死锁。

##### 隐患分析：

t0,t1,t2,t3都有执行资格。

线程t0拿到执行权，生产烤鸭1，flag为true。

t0再次拿到执行权被冻结。

t1拿到执行权也被冻结。此时，t0和t1都进入线程池。

t2拿到执行权，消费了烤鸭1，flag为false。唤醒线程池中被冻结的线程t0或t1。假设唤醒了t0。此时，t0,t2,t3有执行资格。

t2继续拿到执行权被冻结，进入线程池（t1,t2）。

t3拿到执行权也被冻结，进入线程池（t1,t2,t3）。

被唤醒的t0拿到执行权，没有判断if(flag)，直接往下执行，生产烤鸭2，flag为true。唤醒线程池中的一个线程。

如果t1被唤醒，此时t0和t1有执行资格，线程池中还有t2和t3。

t0拿到执行权，被冻结进入线程池。

被唤醒的t1拿到执行权，还是没有判断if(flag)，从被冻结的位置，直接往下执行，又生产了烤鸭3。

问题出现，烤鸭2没有被消费的情况下，生产了烤鸭3。

##### 隐患解决方法：t1拿到执行权后，要判断flag为true，不要再生产烤鸭3。

因此要将if改为while

	public synchronized void set(String name){
		while(flag)
			try{
				this.wait()
			}catch(InterruptedException e){
				//...
			}
		...
	}
	
	public synchronized void out(){
		while(!flag)
			try{
				this.wait()
			}catch(InterruptedException e){
				//...
			}
		...
	}
	

##### 死锁分析：

t0和t1被冻结进入线程池。

t2拿到执行权，消费烤鸭1，flag为false，唤醒t0。

t2和t3先后拿到执行权被冻结进入线程池。

t0拿到执行权，生产烤鸭，flag变为true，唤醒线程池中的任意一个线程。此时，线程池中有t1,t2,t3。

如果唤醒的是t1，此时有执行资格的为t0和t1。先后拿到执行权，由于flag为true，都被冻结进入线程池。此时，t0,t1,t2,t3都被冻结在线程池中。

没有线程被唤醒，没有线程有执行资格，没有线程能拿到执行权，死锁。

##### 死锁解决：

t0拿到执行权，生产烤鸭，flag变为true，唤醒线程池中的线程时，全部唤醒。

	public synchronized void set(String name){
		...
		this.notifyAll();
	}
	
	public synchronized void out(String name){
		...
		this.notifyAll();
	}

#### 4、多线程通信-多生产者多消费者问题-JDK1.5新特性-Lock

Lock接口是1.5版本对synchronized的替代。

同步代码块和同步函数对于锁的操作是隐式的。

jdk1.5以后将同步和锁封装成了对象，并将操作锁的隐式方式定义到了该对象中，将隐式动作变成了显式动作。

	Object obj = new Object();
	void show(){
		synchronized(obj){
			//...
		}
	}
	
	Lock lock = new ReentrantLock();
	void show(){
		try{
			lock.lock();//获取锁
			//...
		}catch(){
			//...
		}finally{
			lock.unlock();
		}
	}

#### 5、多线程通信-多生产者多消费者问题-JDK1.5新特性-Condition

	Object obj = new Object();
	void show(){
		synchronized(obj){
			//...
		}
	}
	
	同步代码块中的锁对象，只有一组操作线程的方法，就是继承自object的wait(),notify(),notifyAll()。
	
	Lock lock = new ReentrantLock();
	void show(){
		try{
			lock.lock();//获取锁
			//...
		}catch(){
			//...
		}finally{
			lock.unlock();
		}
	}
	
	jdk1.5以后将操作线程的方法封装为了对象即为condition接口。
	
	Lock替代的是synchronized的方法和语句的使用，Condition替代了Object监视器（即锁）的方法（wait,notify,notifyAll）的使用。
	
	通过Lock接口的newCondition方法，就可以使一个实现了Lock接口的锁对象生成多组监视器方法。
	
	interface Condition{
		await();
		signal();
		signalAll();
	}
	
	Lock lock = new ReentrantLock();
	Condition c1 = lock.newCondition();
	Condition c2 = lock.newCondition();

##### 优化烤鸭示例代码

	import java.util.concurrent.locks.*
	
	class Resource{
		private ...
		...
		//创建一个锁对象
		Lock lock = new ReentrantLock();
		//通过已有的锁获取该锁上的监视器对象
		Condition con = lock.newCondition();
		public void set(String name){
			lock.lock();
			try{
				while(flag)
					//try{this.wait();}catch(...){}
					try{con.await();}catch(...){}
					...
					//notifyAll();
					con.signalAll();
			}finally{
				lock.unlock();
			}
		}
		public void out(){
			lock.lock();
			try{
				while(!flag)
					//try{this.wait();}catch(...){}
					try{con.await();}catch(...){}
					...
					//notifyAll();
					con.signalAll();
			}finally{
				lock.unlock();
			}
		}
	}

##### notifyAll解决方法缺点

唤醒了所有的线程，t1还有可能优于t2和t2拿到执行权，降低了效率。

应该想办法做到：set的线程t0和t1只唤醒out的t2和t3，而out的线程t2和t3只唤醒t0和t1。

lock和condition满足条件，四个线程共用一把锁lock，但是一把锁有两个监视器，一个监视器用来监视set，另一个监视器用来监视out。

##### 优化烤鸭示例代码

	Lock lock = new ReentrantLock();
	Condition producer_con = lock.newCondition();
	Condition consumer_con = lock.newCondition();
	public void set(String name){
		lock.lock();
		try{
			while(flag)
				//try{this.wait();}catch(...){}
				//try{con.await();}catch(...){}
				try{producer_con();}catch(...){}
				...
				//notifyAll();
				//con.signalAll();
				//唤醒消费者的线程的一个线程即可
				consumer_con.signal();
		}finally{
			lock.unlock();
		}
	}
	public void out(){
		lock.lock();
		try{
			while(!flag)
				//try{this.wait();}catch(...){}
				//try{con.await();}catch(...){}
				try{consumer_con.await();}catch(...){}
				...
				//notifyAll();
				//con.signalAll();
				producer_con.signal();
		}finally{
			lock.unlock();
		}
	}

##### lock & condition

Lock接口：替代了同步代码块或者同步函数。将同步的隐式锁操作变成显式操作。同时更为灵活。可以给一个锁加上多组监视器。
	
	lock():获取锁
	
	unlock():释放锁

Condition接口：替代了Object中的wait，notify，notifyAll方法。将这些监视器的方法进行了封装，变成Condition监视器对象。可以任意锁进行组合。

	await():相当于wait()方法
	signal():相当于notify()方法
	signalAll():相当于notifyAll()方法

#### 6、wait和sleep的区别

1、wait可以指定时间，也可以不指定时间。sleep必须指定时间。

2、在同步中，对cpu的执行权和锁的处理不同。
	wait：释放执行权，也释放锁。
	sleep：释放执行权，不释放锁。

#### 7、停止线程方式 - 定义标记

1、stop方法。已过时。

2、run方法结束

任务中都有循环结构，只要控制住循环就可以结束任务。

控制循环通常就用定义标记来完成。

	class StopThread implements Runnable{
		private boolean flag = true;
		public void run(){
			while(flag){
				System.out.println(Thread.currentThread().getName()+"...");
			}
		}
		public void setFlag(){
			flag = false;
		}
	}
	
	class StopThreadDemo{
		public static void main(String[] args){
			StopThread st = new StopThread();
			Thread t1 = new Thread(st);
			Thread t2 = new Thread(st);
			t1.start();
			t2.start();
			
			int num = 1;
			for(;;){
				if(++num == 50){
					st.setFlag(0;
					break;
				}
				System.out.println("main..."+num);
			}
			System.out.println("over");
		}
	}

如果线程处于冻结状态，无法读取标记，可以用Thread类中的interrupt方法将线程从冻结状态强制恢复到运行状态中来，让线程具备cpu的执行资格。

强制动作会发生中断异常，需要处理。

	class StopThread implements Runnable{
		private boolean flag = true;
		public void run(){
			while(flag){
				try{
					wait();
				}catch(InterruptedException e){
					System.out.printlin(Thread.currentThread().getName()+"..."+e);
				}
				System.out.printlin(Thread.currentThread().getName()+"...+++");
			}
		}
		public void setFlag(){
			flag = false;
		}
	}
	
	class StopThreadDemo{
		public static void main(String[] args){
			StopThread st = new StopThread();
			Thread t1 = new Thread(st);
			Thread t2 = new Thread(st);
			t1.start();
			t2.start();
			
			int num = 1;
			for(;;){
				if(++num == 50){
					//st.setFlag(0;
					t1.interrupt();
					t2.interrupt();
					break;
				}
				System.out.println("main..."+num);
			}
			System.out.println("over");
		}
	}
	
#### 8、多线程 - 守护线程

setDaemon：后台线程，守护线程，用户线程。

运行时，跟前台线程一样，争夺cpu资源。

前台线程需要手动结束，后台线程当前台线程全部结束后自动结束。

setDaemon在线程开启之前调用。
	
	t1.setDaemon(true);
	t1.start();
	t2.setDaemon(true);
	t2.start();


