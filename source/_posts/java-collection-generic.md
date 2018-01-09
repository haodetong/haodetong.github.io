---
title:  集合框架二 - 泛型
date: 2018-01-09 14:36:44
tags: collecionsFramework
category: java
keywords: java, api
description: java集合框架泛型

---

### 二、集合框架-泛型

	public class GenericDemo {
	
		public static void main(String[] args) {
			ArrayList<String> al = new ArrayList<String>();
			
			al.add("abc");
			al.add("abc2");
			al.add("abc3");
			
			Iterator<String> it = al.iterator();
			while(it.hasNext()) {
				String str = it.next();
				System.out.println(str);
			}
			
		}
	
	}
	
#### 1、概述：

jdk1.5出现的安全机制。

好处：

1、将运行时期的问题ClassCastException转到了编译时期。

2、避免了强制转换的麻烦。

<>：什么时候用？当操作的引用类型不确定的时候，就使用<>。将要操作的引用数据类型传入即可。

其实<>就是一个用于接收具体引用数据类型的参数范围。

#### 2、泛型-擦除&补偿

泛型技术是给编译器使用的技术，用于编译时期。确保了类型的安全。

运行时，会将泛型去掉，生成的class文件中是不带泛型的，这个称之为泛型的擦除。

为什么擦除？是为了兼容运行的类加载器。

泛型的补偿：在运行时，通过获取元素的类型进行转换。不用使用者再强制转换了。

#### 3、泛型在集合中的应用

	public class GenericDemo2 {
	
		public static void main(String[] args) {
			TreeSet<Person2> ts = new TreeSet<Person2>();
			ts.add(new Person2("lisi8",21));
			ts.add(new Person2("lisi2",23));
			ts.add(new Person2("lisi",21));
			ts.add(new Person2("lis0",20));
			
			Iterator<Person2> it = ts.iterator();
			while(it.hasNext()) {
				Person2 p = it.next();
				System.out.println(p.getName()+":"+p.getAge());
			}
			
		}
	
	}
	
	public class Person2 implements Comparable<Person2> {
		private String name;
		private int age;
		
		public Person2() {
			super();
		}
		
		public Person2(String name, int age) {
			super();
			this.name = name;
			this.age = age;
		}
		
		public int compareTo(Person2 p){
			int temp = this.age - p.getAge();
			return temp==0?this.name.compareTo(p.getName()):temp;	
		}
	
		public String getName() {
			return name;
		}
		
		public void setName(String name) {
			this.name = name;
		}
		
		public int getAge() {
			return age;
		}
		
		public void setAge(int age) {
			this.age = age;
		}	
		
	}
	
	class CompareByname implements Comparator<Person2>{
		public int compare(Person2 o1, Person2 o2) {
			int temp = o1.getName().compareTo(o2.getName());
			return temp == 0?o1.getAge() - o2.getAge():temp;	
		}
	}
	
	//按年龄排序，输出结果：
	lis0:20
	lisi:21
	lisi8:21
	lisi2:23
	
	//按姓名排序，输出结果：
	lis0:20
	lisi:21
	lisi2:23
	lisi8:21

#### 4、泛型类

在jdk1.5后，使用泛型来接收类中要操作的引用数据类型

什么时候用？当类中的操作的引用数据类型不确定的时候，就使用泛型来表示。
	public class Student {
	
	}
	
	public class Worker {
	
	}

	public class Tool01<XX> {
		private XX q;
		
		public XX getObject() {
			return q;
		}
		public void setObject(XX object) {
			this.q = object;
		}
	}
	
	public class GenericDemo3 {
		public static void main(String[] args) {
			
			Tool01<Student> tool = new Tool01<Student>();
			tool.setObject(new Student());
			Student stu = tool.getObject();
			System.out.println(stu);
		
		}
	}
	
	当tool.setObject(new Student());传递Worker对象时tool.setObject(new Worker());编译就会报错。
	
#### 5、泛型方法

	class Tool02<XX> {//泛型定义在类上
		
		public void print(XX str) {
			System.out.println("print:"+str);
		}
		
		/**
		 * 将泛型定义到方法上
		 */
		public <W> void show(W str) {
			System.out.println("show:"+str);
		}
		
		/**
		 * 当定义静态方法时，不能访问类上定义的泛型。静态方法只能将泛型定义到方法上才能使用泛型。
		 */
		public static <Y> void output(Y str) {
			System.out.println("output:"+str);
		}
	}
	
	public class GenericDemo4 {

		public static void main(String[] args) {
			
			Tool02<String> tool = new Tool02<String>();
			
			//print方法只能使用生成对象时指定的String类型
			tool.print("abc");
			
			//泛型定义到了tool方法上，可以传递任意类型对象，编译都不会报错
			tool.show(new Integer(4));
			tool.show("abc");
			
			//静态方法的泛型只能定义到静态方法上可以传递任意类型对象
			Tool02.output("abc");
			Tool02.output(8);
			
		}
	
	}	

#### 6、泛型的接口

	public class GenericDemo5 {
	
		public static void main(String[] args) {
			
			InterImpl in = new InterImpl();
			in.show("abc");
			
			InterImpl2<Integer> in2 = new InterImpl2<Integer>();
			in2.show(5);
			
			
		}
	
	}
	
	interface Inter<T>{
		public void show(T t);
	}
	
	class InterImpl implements Inter<String>{
		public void show(String str) {
			System.out.println("show:"+str);
		}
	}
	
	class InterImpl2<X> implements Inter<X>{
		public void show(X x) {
			System.out.println("show:"+x);
		}
	}

#### 7、泛型限定（上限）

泛型的通配符：?

代表未知类型

	public class GenericDemp6 {
	
		public static void main(String[] args) {
			
			ArrayList<String> al = new ArrayList<String>();
			al.add("abc");
			al.add("ddd");
			
			ArrayList<String> al2 = new ArrayList<String>();
			al.add("abc2");
			al.add("ddd2");
			
			printCollection(al);
			printCollection(al2);
			
		}
	
		public static void printCollection(Collection<?> al) {
			Iterator<?> it = al.iterator();
			while(it.hasNext()) {
				System.out.println(it.next());
			}
		}
		
		public static <X> void printCollection2(Collection<X> al) {
			Iterator<X> it = al.iterator();
			while(it.hasNext()) {
				X str = it.next();
				System.out.println(str);
			}
		}
	
	}

? extends E 限定只能传E或者E的子类

	public class Student extends Person2{
	
		public Student() {
			super();
		}
	
		public Student(String name, int age) {
			super(name, age);
		}
		
		public String toString() {
			return "student:"+getName()+":"+getAge();
		}
		
	}
	
	public class Worker extends Person2 {
	
		public Worker() {
			super();
		}
	
		public Worker(String name, int age) {
			super(name, age);
		}
		
		public String toString() {
			return "worker:"+getName()+":"+getAge();
		}
	}
	
	public class GenericDemo7 {

		public static void main(String[] args) {
			
			ArrayList<Worker> al = new ArrayList<Worker>();
			al.add(new Worker("worker01",30));
			al.add(new Worker("worker02",32));
			
			ArrayList<Student> al2 = new ArrayList<Student>();
			al2.add(new Student("worker01",10));
			al2.add(new Student("worker02",11));
			
			printCollection(al);
			printCollection(al2);
			
		}
	
		//限定只能传Person2的对象或者Person2的子类对象
		private static void printCollection(ArrayList<? extends Person2> al) {
			
			Iterator<? extends Person2> it = al.iterator();
			
			while(it.hasNext()) {
				System.out.println(it.next().toString());
			}
		
		}
	
	}

#### 8、泛型限定（下限）

? super E 限定只能传E或者E的父类

	public class GenericDemo7 {
	
		public static void main(String[] args) {
			
			ArrayList<Person2> al = new ArrayList<Person2>();
			al.add(new Person2("Person",30));
			al.add(new Person2("Person",32));
			
			ArrayList<Student> al2 = new ArrayList<Student>();
			al2.add(new Student("worker01",10));
			al2.add(new Student("worker02",11));
			
			printCollection(al);
			printCollection(al2);
			
		}
		
		//限定只能传Student的对象或者父类Person2的对象，而不能传Worker对象
		private static void printCollection(ArrayList<? super Student> al) {
			
			Iterator<? super Student> it = al.iterator();
			
			while(it.hasNext()) {
				System.out.println(it.next());
			}
		
		}
	
	}

#### 9、泛型限定-上限的体现

一般在存储元素时候都是用上限，因为这样取出的时都是按照上限类型来运算的。不会出现类型的安全隐患。

	public class GenericDemo8 {
	
		public static void main(String[] args) {
			
			ArrayList<Person2> al = new ArrayList<Person2>();
			al.add(new Person2("Person",30));
			al.add(new Person2("Person",32));
			
			ArrayList<Student> al2 = new ArrayList<Student>();
			al2.add(new Student("studnet01",10));
			al2.add(new Student("studnet02",11));
		
			ArrayList<Worker> al3 = new ArrayList<Worker>();
			al3.add(new Worker("worker01",20));
			al3.add(new Worker("worker02",21));
			
			al.addAll(al2);
			al.addAll(al3);
			
		}
	
	}
	
	class MyCollection<E>{
		public void add(E e) {
			
		}
		public void addAll(MyCollection<? extends E> e){
			
		}
	}

#### 10、泛型限定-下限的体现

通常对集合中的元素进行取出操作时，可以用下限。

	public class GenericDemo9 {
	
		public static void main(String[] args) {
			
			TreeSet<Person2> al = new TreeSet<Person2>(new CompareByName2());
			al.add(new Person2("aaa",30));
			al.add(new Person2("bbb",32));
			al.add(new Person2("ccc",32));
			al.add(new Person2("ddd",34));
			
			TreeSet<Student> al2 = new TreeSet<Student>();
			al2.add(new Student("studnet01",10));
			al2.add(new Student("studnet02",11));
			
			al.addAll(al2);
			
			Iterator<Person2> it = al.iterator();
			
			while(it.hasNext()) {
				System.out.println(it.next());
			}
			
		}
	
	}
	
	class CompareByName2 implements Comparator<Person2>{
	
		@Override
		public int compare(Person2 o1, Person2 o2) {
			int temp = o1.getName().compareTo(o2.getName());
			return temp == 0? o1.getAge() - o2.getAge():temp;
		}
		
	}