---
title: 集合框架
date: 2017-12-27 11:35:39
tags: api
category: java
keywords: java, api
description: java集合框架
---

### 集合框架的构成和分类

java中集合类的关系图
				

http://blog.csdn.net/real_neu/article/details/52821491


### 一、概述

#### 1、特点 ：

* 用于存储对象的容器。

* 集合的长度可变。

* 集合中不能存储基本数据类型值。

#### 2、体系&共性功能

框架的顶层Collection接口：

1、添加 

	boolean add(Object obj)
	
	boolean addAll(Collection coll);
	
2、删除

	boolean remove(Object)
	boolean removeAll(Collection coll)
	void clear()

3、判断

	boolean contains(Object obj)
	boolean containsAll(Collection coll)
	boolean isEmpty();
	
4、获取

	int size();
	Iterator iterator(): 取出元素的方式：迭代器
			
5、其它

	boolean retainAll(Collection coll): 取交集
	Object toArray(): 将集合转为数组
	
#### 3、方法演示

	import java.util.ArrayList;
	import java.util.Collection;
	
	public class CollectionDemo {
	
		public static void main(String[] args) {
			Collection coll = new ArrayList();
			//show(coll);
			
			Collection c1 = new ArrayList();
			Collection c2 = new ArrayList();
			show(c1,c2);
		}
		
		public static void show(Collection c1, Collection c2) {
			//给c1添加元素
			c1.add("abc1");
			c1.add("abc2");
			c1.add("abc3");
			c1.add("abc4");
			
			//给c2添加元素
			c2.add("abc2");
			c2.add("abc6");
			c2.add("abc7");
			
			System.out.println("c1:"+c1);
			System.out.println("c2:"+c2);
			
			//演示addAll
			c1.addAll(c2);
			System.out.println("c1:"+c1);
			
			//演示removeAll，将两个集合中的相同元素从调用removeAll的集合中删除
			//boolean b = c1.removeAll(c2);
			//System.out.println("removeAll:"+b);
			//System.out.println("c1:"+c1);
			
			//演示containsAll
			boolean c = c1.contains(c2);
			System.out.println("containsAll:"+c);
			
			//演示retainAll
			System.out.println("c1:"+c1);
			System.out.println("c2:"+c2);
			boolean d = c1.retainAll(c2);
			System.out.println(d);
			System.out.println("c1:"+c1);
		}
		
		public static void show(Collection coll) {
			//添加
			coll.add("abc1");
			coll.add("abc2");
			coll.add("abc3");
			System.out.println(coll);
			
			//删除，会改变集合的长度
			coll.remove("abc2");
			System.out.println(coll);
			
			//清空
			coll.clear();
			System.out.println(coll);
			
			//判断
			System.out.println(coll.contains("abc3"));
		}
	
	}
	
#### 4、迭代器


使用方法
 
	import java.util.ArrayList;
	import java.util.Collection;
	import java.util.Iterator;
	
	public class IteratorDemo {
	
		public static void main(String[] args) {
			Collection coll = new ArrayList();
			coll.add("abc1");
			coll.add("abc2");
			coll.add("abc3");
			coll.add("abc4");
			System.out.println(coll);
			//调用集合中的迭代器方法，是为了获取集合中的迭代器对象
	//		Iterator it = coll.iterator();
	//		while(it.hasNext()) {
	//			System.out.println(it.next());
	//		}
			
			for(Iterator it = coll.iterator(); it.hasNext(); ) {
				System.out.println(it.next());
			}
			
		}
	
	}

原理

	Iterator iterator(): 取出元素的方式：迭代器
	
	该对象必须依赖于具体容器，因为每一个容器的数据结构都不同。
	
	所以该迭代器对象是在容器内部实现的。
	
	对于使用容器者而言，具体的实现方式不重要，只要通过容器获取到实现的迭代器的对象即可，也就是iterator方法。
	
	Iterator接口就是对所有的Collection容器进行元素取出的公共接口。

#### 5、List和Set的特点
	
	Collection

	|--	List：有序（存入和取出的顺序一致），元素都有索引，元素可以重复。
	
	|-- Set：元素不能重复。

List：特有的常见方法

1、添加
	
	void add(index,element)
	void add(index,collection)

2、删除
	
	Object remove(index)
	
3、修改
	
	Object set(index,element)

4、获取

	Object get(index)
	int indexOf(Object)
	int lastIndexOf(object)
	List subList(fromIndex,toIndex)

list集合是可以完成对元素的增删改查的。
	
方法演示
	
	public class listDemo2 {
	
		public static void main(String[] args) {
			List list = new ArrayList();
			show(list);
		}
	
		private static void show(List list) {
			list.add("abc1");
			list.add("abc2");
			list.add("abc3");
			list.add("abc4");
			
			Iterator it = list.iterator();
			while(it.hasNext()) {
				System.out.println("next:"+it.next());
			}
			
			//list特有的取出元素的方式，set只能通过迭代器方式取出元素
			for(int x=0; x<list.size(); x++) {
				System.out.println("get:"+list.get(x));
			}
		}
	
	}

演示2

	public class listDemo3 {
	
		public static void main(String[] args) {
			List list = new ArrayList();
			list.add("abc1");
			list.add("abc2");
			list.add("abc3");
			
			Iterator it = list.iterator();
			while(it.hasNext()) {
				Object obj = it.next();
				if(obj.equals("abc2")) {
					list.add("abc9");
				}else {				
					System.out.println("next:"+obj);
				}			
			}
			
			System.out.println(it.next());
		}
	
	}
	
	执行时，抛出并发修改的异常。
	Exception in thread "main" 		
	java.util.ConcurrentModificationException
	
	原因：
	Iterator it = list.iterator();迭代器检查list集合长度为3。
	当list.add("abc9")，修改了集合长度时，迭代器不知道。
	
	解决方案：使用Iterator接口的子接口listIterator来完成在迭代中对元素进修改。
	
	public class listDemo3 {

		public static void main(String[] args) {
			List list = new ArrayList();
			list.add("abc1");
			list.add("abc2");
			list.add("abc3");
			
			//获取列表迭代器对象，它可以实现在迭代过程中对元素的增删改查，注意只有list集合具备该迭代功能
			ListIterator it = list.listIterator();
			
			while(it.hasNext()) {
				Object obj = it.next();
				if(obj.equals("abc2")) {
					//it.add("abc9");
					it.set("abc9");
				}
			}
			
			System.out.println(list);
			
			while(it.hasPrevious()) {
				System.out.println("previous:"+it.previous());
			}
			
		}

	}
	
#### 6、List常用子类的特点

List:

|-- Vector：内部是数组数据结构，是同步的。增删，查询都很慢。

|-- ArrayList：内部是数组数据结构，是不同步的。替代了Vector。查询的速度快。

|-- LinkedList：内部是链表数据结构，是不同步的。增删元素的速度很快。
	
-- addFirst()

-- addLast()

-- getFirst() 获取但不移除，如果链表为空，抛出NoSuchElement异常

-- getLast()

-- removeFirst() 获取并移除，如果链表为空，抛出NoSuchElement异常

-- removeLast()

jdk1.6

-- offerFirst()

-- offerLast()

-- peekFirst() 获取但不移除，如果链表为空，返回null

-- peekLast()

-- pollFirst() 获取并移除，如果链接为空，返回null

-- pollLast()
	
	/**
	 * 请使用LinkedList来模拟一个堆栈或者队列数据结构
	 * 堆栈：先进后出
	 * 队列：先进先出
	 */
	public class linkedListDemo {
		public static void main(String[] args) {
			
			DuiLie dl = new DuiLie();
			dl.myAdd("abc1");
			dl.myAdd("abc2");
			dl.myAdd("abc3");
			dl.myAdd("abc4");
			
			while(!dl.isNull()) {
				System.out.println(dl.myGet());
			}
		}
	}
	
	class DuiLie{
		private LinkedList link;
		
		public DuiLie() {
			link = new LinkedList();
		}
		
		public void myAdd(Object obj) {
			link.addLast(obj);
		}
		
		public Object myGet() {
			return link.removeFirst();
		}
		
		public boolean isNull() {
			return link.isEmpty();
		}
	}

#### 7、ArrayList集合存储自定义对象

	public class arrayListTest {
	
		public static void main(String[] args) {
			
			ArrayList al = new ArrayList();
			al.add(new Person("lisi1",21));
			al.add(new Person("lisi2",22));
			al.add(new Person("lisi3",23));
			al.add(new Person("lisi4",24));
			
			Iterator it = al.iterator();
			while(it.hasNext()) {
				//System.out.println(((Person) it.next()).getName());
				Person p = (Person) it.next();
				System.out.println(p.getName()+"--"+p.getAge());
			}
			
		}
	
	}
	
	class Person{
		private String name;
		private int age;
		
		public Person() {
			super();
		}
	
		public Person(String name, int age) {
			super();
			this.name = name;
			this.age = age;
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
	
### 集合框架-HashSet集合

Set：元素不可以重复，是无序的。

Set接口中的方法和Collection一致。

|-- HashSet：内部数据结构是Hash表，是不同步的。

|-- TreeSet：

	public static void main(String[] args) {
			HashSet hs = new HashSet();
			
			hs.add("aaa");
			hs.add("bbb");
			hs.add("ccc");
			hs.add("ddd");
			
			Iterator it = hs.iterator();
			
			while(it.hasNext()) {
				System.out.println(it.next());
			}
			
		}

集合框架 - 哈希表

HashSet集合数据结构是哈希表，所以存储元素的时候，首先使用元素的hashCode方法来确定位置，如果位置相同，在通过元素的equals方法来确定内容是否相同。	
	
	class Person{
		
		...
		
		public int hashCode() {
			//System.out.println(this);
			return name.hashCode()+age*27;
		}
	
		public boolean equals(Object obj) {
		if(this == obj)
				return true;
		
		if(!(obj instanceof Person))
			throw new ClassCastException("类型不对");
		
		Person p = (Person)obj;
		
		System.out.println(this.name+"..."+this.age+"...equals..."+p.name+"..."+p.age);
		
		return this.name.equals(p.name) && this.age == p.age;
	}
	
		...
		
	}	

	public class hashSetDemo2 {
	
		public static void main(String[] args) {
			
			HashSet hs = new HashSet();
			
			hs.add(new Person("lisi1",21));
			hs.add(new Person("lisi2",22));
			hs.add(new Person("lisi3",23));
			hs.add(new Person("lisi4",24));
			hs.add(new Person("lisi4",24));
			
			Iterator it = hs.iterator();
			
			while(it.hasNext()) {
				Person p = (Person)it.next();
				System.out.println(p.getName()+"..."+p.getAge());
			}
			
		}
	
	}

#### 8、删除ArrayList集合中的重复元素

	public class arrayListPractice {
	
		public static void main(String[] args) {
			ArrayList al = new ArrayList();
			al.add("abc1");
			al.add("abc2");
			al.add("abc3");
			al.add("abc4");
			al.add("abc1");
			al.add("abc3");
			
			System.out.println(al);
			
			al = getSingleElement(al);
			
			System.out.println(al);
		}
	
		private static ArrayList getSingleElement(ArrayList al) {
			//1、定义临时容器
			ArrayList temp = new ArrayList();
			//2、迭代al集合
			Iterator it = al.iterator();
			while(it.hasNext()) {
				Object obj = it.next();
				//3、判断是否重复
				if(!(temp.contains(obj))) {
					temp.add(obj);
				}
			}
			return temp;
		}
	
	}
	
	输出结果：
	[abc1, abc2, abc3, abc4, abc1, abc3]
	[abc1, abc2, abc3, abc4]

	换成自定义对象测试：
	
		public static void main(String[] args) {
			ArrayList al = new ArrayList();
			al.add();
			al.add("abc2");
			al.add("abc3");
			al.add("abc4");
			al.add("abc1");
			al.add("abc3");
			
			System.out.println(al);
			
			al = getSingleElement(al);
			
			System.out.println(al);
		}
	
	输出结果：
	[lisi1:21, lisi2:22, lisi3:23, lisi4:24, lisi2:22, lisi3:23]
	[lisi1:21, lisi2:22, lisi3:23, lisi4:24, lisi2:22, lisi3:23]
	
	原因分析：
	contains方法是用的equals方法来比较两个元素是否相同。
	equals方法是自定义类继承的object中的方法，所以比较的是对象的内存地址。
	
	所以，要到Person类中对equals方法进行重写覆盖，就像String类对equals方法进行了重写一样，只要内容相同，即视为元素相同。
	
	public boolean equals(Object obj) {
		if(this == obj)
				return true;
		
		if(!(obj instanceof Person))
			throw new ClassCastException("类型不对");
		
		Person p = (Person)obj;
		
		//System.out.println(this.name+"..."+this.age+"...equals..."+p.name+"..."+p.age);
		
		return this.name.equals(p.name) && this.age == p.age;
	}
	
#### 9、集合框架-LinkedHashSet集合

HashSet保证了唯一，但是无序。

LinkedHashSet用链表进行了扩展。

	public static void main(String[] args) {
		HashSet hs = new LinkedHashSet();
		
		hs.add("abc2");
		hs.add("abc1");
		hs.add("abc3");
		hs.add("abc4");
		
		Iterator it = hs.iterator();
		while(it.hasNext()) {
			System.out.println(it.next());
		}
	}
	
	输出结果：
	abc2
	abc1
	abc3
	abc4
	
	保证了集合既有序且唯一。

#### 10、集合框架 - TreeSet集合

TreeSet：可以对Set集合中的元素进行排序。是不同步的。

判断元素唯一性的方式：就是根据比较方法的返回结果是否为0，是0，就是相同元素，不存。

TreeSet对元素进行排序的方式一：

让元素自身具备比较功能，元素就需要实现Comparable接口，重写覆盖compareTo方法。

	public static void main(String[] args) {
		TreeSet ts = new TreeSet();
		
		ts.add("abc");
		ts.add("aaa");
		ts.add("abbc");
		ts.add("acsw");
		
		Iterator it = ts.iterator();
		
		while(it.hasNext()) {
			System.out.println(it.next());
		}
		
	}
	
	输出结果：	
	aaa
	abbc
	abc
	acsw

String类已经实现了Comparable接口，重写了compareTo方法，所以添加时对元素进行了排序。自定义的类，需要按需求自定义排序方式。

	class Person implements Comparable{
		...
		//按年龄排序，年龄相同的按姓名排序
		public int compareTo(Object o) {
			Person p = (Person)o;
			int temp = this.age - p.age;
			return temp == 0?this.name.compareTo(p.name):temp;
		}
		...
	}
	
	public class TreeSetDemo {
		public static void main(String[] args) {
			TreeSet ts = new TreeSet();
			
			ts.add(new Person("zhangsan",28));
			ts.add(new Person("wangwu",21));
			ts.add(new Person("lisi",26));
			ts.add(new Person("zhaoliu",28));		
			
			Iterator it = ts.iterator();
			
			while(it.hasNext()) {
				System.out.println(it.next());
			}
			
		}
	}
	
	输出结果：
	wangwu:21
	lisi:26
	zhangsan:28
	zhaoliu:28
