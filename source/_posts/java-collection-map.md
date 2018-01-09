---
title:  集合框架四 - Map集合
date: 2018-01-09 14:44:18
tags: collecionsFramework
category: java
keywords: java, api
description: java map集合

---

### 四、集合框架 - Map集合

#### 1、集合特点&常用方法

Collection：一次添加一个元素，集合称为单列集合。

Map：一次添加一对元素，存储的是键值对，集合中必须保证键的唯一性。

常用方法：

添加：
	
	value put(key,value):返回前一个和key关联的值，如果没有返回null

删除：
	
	void clear():清空map集合
	value remove(key):根据指定的key删除这个键值对，返回被删除键值对的value。
	
判断：

	boolean containskey(key)
	boolean containsValue(value)
	boolean isEmpty()

获取：

	value get(key):通过键获取值，如果没有该键返回null。因此，可以通过返回null，来判断是否包含指定键。
	int size():获取键值对的个数。

方法演示：

	public class MapDemo1 {
	
		public static void main(String[] args) {
			
			Map<Integer,String> map = new HashMap<Integer,String>();
			
			method(map);
			
		}
	
		private static void method(Map<Integer, String> map) {
			
			//添加
			System.out.println(map.put(8, "wangcai"));
			System.out.println(map.put(8, "xiaoqiang"));
			map.put(2, "zhangsan");
			map.put(4, "lisi");
			System.out.println(map);
			
			//删除
			System.out.println("remove:"+map.remove(2));
			
			//判断
			System.out.println("containskey:"+map.containsKey(7));
			
			//获取
			System.out.println("get:"+map.get(6));
			
			
		}
	
	}

#### 2、重点方法keySet

取出map中的所有元素。

原理：通过keySet方法取出map中的所有键所在的Set集合，再通过Set的迭代器获取每一个键，再通过map集合的get方法获取每个键所对应的值。

	public class MapDemo2 {
	
		public static void main(String[] args) {
			
			Map<Integer,String> map = new HashMap<Integer,String>();
			
			method(map);
		
		}
	
		private static void method(Map<Integer, String> map) {
			
			map.put(2, "zhangsan");
			map.put(8, "lisi");
			map.put(7, "wangwu");
			map.put(6, "zhaoliu");
			
			Set<Integer> keySet = map.keySet();
			Iterator<Integer> it = keySet.iterator();
			
			while(it.hasNext()) {
				Integer key = it.next();
				String value = map.get(key);
				System.out.println(key+":"+value);
			}
		
		}
	
	}
	
	输出结果：
	2:zhangsan
	6:zhaoliu
	7:wangwu
	8:lisi

#### 3、重点方法entrySet

该方法将键和值的映射关系作为对象存储到了Set集合中，而这个映射关系的类型就是Map.Entry类型（结婚证）。

	public class MapDemo3 {
	
		public static void main(String[] args) {
			
			Map<Integer,String> map = new HashMap<Integer,String>();
			
			method(map);
			
		}
	
		private static void method(Map<Integer, String> map) {
			
			map.put(2, "zhangsan");
			map.put(8, "lisi");
			map.put(7, "wangwu");
			map.put(6, "zhaoliu");
			
			Set<Map.Entry<Integer,String>> entrySet = map.entrySet();
	
			Iterator<Map.Entry<Integer,String>> it = entrySet.iterator();
			
			while(it.hasNext()) {
				Map.Entry<Integer, String> merryEntry = it.next();
				Integer key = merryEntry.getKey();
				String value = merryEntry.getValue();
				System.out.println(key+":"+value);
			}
		
		}
	
	}

#### 4、values方法

Collection<V> values()

返回此映射中包含的值的Collection视图。
	
	public class MapDemo4 {
	
		public static void main(String[] args) {
			
			Map<Integer,String> map = new HashMap<Integer,String>();
			
			method(map);
			
		}
	
		public static void method(Map<Integer, String> map) {
			map.put(2, "zhangsan");
			map.put(8, "lisi");
			map.put(7, "wangwu");
			map.put(6, "zhaoliu");
			
			Collection<String> values = map.values();
			
			Iterator<String> it = values.iterator();
			
			while(it.hasNext()) {
				System.out.println(it.next());
			}
			
		}
	
	}

#### 5、Map集合-常见子类对象

Map常见子类：

|-- Hashtable：

内部结构是哈希表，是同步的。不允许null作为键和值。
	
|------ Properties：

可以用来存储键值对型的配置文件的信息，可以和IO技术相结合。

|-- HashMap：

内部结构是哈希表，不是同步的。允许null作为键和值。

|-- TreeMap：

内部结构是二叉树，不是同步的。可以对Map集合中的键排序。


#### 6、HashMap存储自定义对象

	public class MapDemo5 {
	
		public static void main(String[] args) {
			
			/**
			 * 将学生对象和学生的归属地通过键与值存储到map集合中。
			 */
			
			HashMap<Student,String> hm = new HashMap<Student,String>();
			
			hm.put(new Student("zhangsan",20),"上海");
			hm.put(new Student("lisi",21),"北京");
			hm.put(new Student("wangwu",30),"临沂");
			hm.put(new Student("wangwu",30),"临沂");
			
			Set<Student> keySet = hm.keySet();
			
			Iterator<Student> it = keySet.iterator();
			
			while(it.hasNext()) {
				Student key = it.next();
				String value = hm.get(key);
				System.out.println(key.getName()+":"+key.getAge()+":"+value);
			}
			
			
		}
	
	}
	
	输出结果：
	zhangsan:20:上海
	wangwu:30:临沂
	wangwu:30:临沂
	lisi:21:北京

	Student类继承自Person2类，通过在Person2类中覆盖重写hashCode和equals方法，去重，保证唯一性。


#### 7、TreeMap存储自定义对象

	public class MapDemo6 {
	
		public static void main(String[] args) {
			
			/**
			 * 将学生对象和学生的归属地通过键与值存储到map集合中。
			 * 按姓名排序
			 */
			
			TreeMap<Student,String> tm = new TreeMap<Student,String>(new ComparatorByName());
			
			tm.put(new Student("zhangsan",20),"上海");
			tm.put(new Student("lisi",21),"北京");
			tm.put(new Student("wangwu",30),"临沂");
			tm.put(new Student("wangwu",30),"临沂");
			
			Set<Map.Entry<Student,String>> entrySet = tm.entrySet();
			
			Iterator<Map.Entry<Student, String>> it = entrySet.iterator();
			
			while(it.hasNext()) {
				Map.Entry<Student, String> merryEntry = it.next();
				Student key = merryEntry.getKey();
				String value = merryEntry.getValue();
				System.out.println(key.getName()+":"+key.getAge()+":"+value);
			}
		}
	
	}
	
	public class ComparatorByName implements Comparator<Person2> {
	
		@Override
		public int compare(Person2 o1, Person2 o2) {
			Person2 p1 = (Person2)o1;
			Person2 p2 = (Person2)o2;
			int temp = p1.getName().compareTo(p2.getName());
			return temp==0?p1.getAge()-p2.getAge():temp;
		}	
		
	}

#### 8、LinkedHashMap

	public class MapDemo7 {
	
		public static void main(String[] args) {
			
			HashMap<Integer,String> hm = new LinkedHashMap<Integer,String>();
			
			hm.put(7, "aaaa");
			hm.put(2, "bbbb");
			hm.put(1, "dddd");
			hm.put(10, "ccc");
			
			Iterator<Map.Entry<Integer,String>> it = hm.entrySet().iterator();
			
			while(it.hasNext()) {
				Map.Entry<Integer, String> merryEntry = it.next();
				Integer key = merryEntry.getKey();
				String value = merryEntry.getValue();
				
				System.out.println(key+":"+value);
			}
			
		}
	
	}
	
	输出结果：
	7:aaaa
	2:bbbb
	1:dddd
	10:ccc
	
	由HashMap变为LinkedHashMap之后，存和取的顺序保持了一致。


#### 9、Map集合练习 - 按自然顺序，记录字母出现次数

	public class MapDemp8 {
	
	
		public static void main(String[] args) {
			
			String str = "jslsjflAAsCCjl  ++js--lajaslasjfsala";
			
			String s = getCarCount(str);
			
			System.out.println(s);
			
		}
	
		private static String getCarCount(String str) {
			
			//字符串变字符数组
			char[] charArr = str.toCharArray();
			
			//TreeSet集合
			TreeMap<Character,Integer> tm = new TreeMap<Character,Integer>();
			
			for (int i = 0; i < charArr.length; i++) {
				
				if(!(charArr[i] >= 'a' && charArr[i] <= 'z' || charArr[i] >= 'A' && charArr[i] <= 'Z')) {
					continue;
				}
				
				Character chara = charArr[i];
				Integer val = tm.get(chara);
				if(val == null) {
					tm.put(chara, 1);
				}else {
					Integer count = tm.get(chara) + 1;
					tm.put(chara,count);
				}
				
				
			}
			
			return mapToString(tm);
		}
	
		private static String mapToString(TreeMap<Character, Integer> tm) {
			StringBuilder sb = new StringBuilder();
			
			Set<Character> keySet = tm.keySet();
			
			Iterator<Character> it = keySet.iterator();
			
			while(it.hasNext()) {
				Character key = it.next();
				Integer value = tm.get(key);
				sb.append(key+"("+value+")");
			}
			
			return sb.toString();
		}
	
	}
	
	输出结果：
	A(2)C(2)a(5)f(2)j(6)l(6)s(7)
