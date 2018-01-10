---
title: 集合框架五 - 工具类
date: 2018-01-09 14:45:51
tags: collecionsFramework
category: java
keywords: java, api
description: java集合框架工具类

---

### 五、集合框架 - 工具类

Utilities

|-- Collections

|-- Arrays

#### 1、Collections - 排序

需求：对有重复元素的集合排序。

	public class Demo1 {
	
		public static void main(String[] args) {
			
			List<String> list = new ArrayList<String>();
			
			list.add("afgds");
			list.add("jlojaio");
			list.add("haljasld");
			list.add("xjvis");
			list.add("afgds");
			
			System.out.println(list);
			
			//对list集合进行指定顺序的排序。
			Collections.sort(list);
			System.out.println(list);
			
			//对list按长度排序，传入自定义比较器
			Collections.sort(list,new ComparatorByLength());
			System.out.println(list);
			
		}
	
	}
	
	public class ComparatorByLength implements Comparator<String> {

		@Override
		public int compare(String o1, String o2) {
			int temp = o1.length() - o2.length();
			return temp == 0 ? o1.compareTo(o2) : temp;
		}
	
	}
	
	输出结果：
	[afgds, jlojaio, haljasld, xjvis, afgds]
	[afgds, afgds, haljasld, jlojaio, xjvis]	
自定义Collections工具类的static sort(List<T> list) 

	public static <T extends Comparable<? super T>> void mysort(List<T> list) {
		
		for (int i = 0; i < list.size()-1; i++) {
			for (int j = i+1; j < list.size(); j++) {
				if(list.get(i).compareTo(list.get(j)) > 0) {
					/*
					T temp = list.get(i);
					list.set(i, list.get(j));
					list.set(j, temp);
					*/
					Collections.swap(list, i, j);
				}
			}
		}
		
		
	}

自定义Collections工具类的static  sort(List<T> list, Comparator<? super T> c) 

	public static <T> void mySort(List<T> list,Comparator<? super T> comp) {
			for (int i = 0; i < list.size()-1; i++) {
				for (int j = i+1; j < list.size(); j++) {
					if(comp.compare(list.get(i), list.get(j)) > 0) {
						Collections.swap(list, i, j);
					}
				}
			}
		}

#### 2、Collections - 折半&最值

折半查找，必须是对有序元素。

	public class Demo2 {
	
		public static void main(String[] args) {
	
			List<String> list = new ArrayList<String>();
			
			list.add("afgds");
			list.add("jlojaio");
			list.add("haljasld");
			list.add("xjvis");
			list.add("afgds");
			
			System.out.println(list);
			
			Collections.sort(list);
			
			int index = Collections.binarySearch(list, "afgds");
			
			System.out.println(index);
			
		}	
	}
	
	输出结果：
	[afgds, jlojaio, haljasld, xjvis, afgds]
	0
	
最值：有没有序都可以。

	public class Demo2 {

		public static void main(String[] args) {
	
			List<String> list = new ArrayList<String>();
			
			list.add("afgds");
			list.add("jlojaio");
			list.add("haljasld");
			list.add("xjvis");
			list.add("afgds");
			
			System.out.println(list);
			
			//取最大值
			String max = Collections.max(list);
			System.out.println("max:"+max);
			
			//取最长值
			String longest = Collections.max(list, new ComparatorByLength());
			System.out.println("longest:"+longest);
					
		}
	
	}
	
#### 3、Collections - 逆序&替换

逆序：reverseOrder方法，返回一个比较器，可以将具备自然排序的集合进行逆序。

	public class Demo3 {
	
		public static void main(String[] args) {
			TreeSet<String> ts = new TreeSet<String>(Collections.reverseOrder());
			
			ts.add("afgds");
			ts.add("jlojaio");
			ts.add("haljasld");
			ts.add("xjvis");
			ts.add("afgds");
		
			System.out.println(ts);
	
		}
	
	}	
	
	输出结果：倒序排列
	[xjvis, jlojaio, haljasld, afgds]
	
也可对已有的比较器进行逆转

	public class Demo3 {
	
		public static void main(String[] args) {
					
			TreeSet<String> ts = new TreeSet<String>(Collections.reverseOrder(new ComparatorByLength()));
			
			ts.add("afgds");
			ts.add("jlojaio");
			ts.add("haljasld");
			ts.add("xjvis");
			ts.add("afgds");
			
			System.out.println(ts);
		}
	
	}
	
	输出结果：从长到短排列
	[haljasld, jlojaio, xjvis, afgds]
	

static <T> boolean	replaceAll(List<T> list, T oldVal, T newVal) 使用另一个值替换列表中出现的所有某一指定值。

	public class Demo4 {
	
		public static void main(String[] args) {
			
			List<String> list = new ArrayList<String>();
			
			list.add("afgds");
			list.add("jloj");
			list.add("halj");
			
			System.out.println(list);
			
			Collections.replaceAll(list, "halj", "haojie");
			
			System.out.println(list);
		}
	
	}
	
	输出结果：
	[afgds, jloj, halj]
	[afgds, jloj, haojie]
	
#### 4、Collections - 其它方法&将非同步集合转成同步集合的方法
          
使用指定元素替换指定列表中的所有元素。	
	
	static <T> void	fill(List<? super T> list, T obj) 

使用默认随机源对指定列表进行置换。随机排序，洗牌

	static void	shuffle(List<?> list) 

返回指定 collection 支持的同步（线程安全的）collection。

	static <T> Collection<T> synchronizedCollection(Collection<T> c) 

返回指定列表支持的同步（线程安全的）列表。	
	static <T> List<T>	synchronizedList(List<T> list) 
       
返回由指定映射支持的同步（线程安全的）映射。

	static <K,V> Map<K,V>	synchronizedMap(Map<K,V> m) 

返回指定 set 支持的同步（线程安全的）set。
	
	static <T> Set<T>	synchronizedSet(Set<T> s) 

#### 5、Arrays - toString方法

Arrays.toString方法

	public class Demo1 {
	
		public static void main(String[] args) {
			
			int[] arr = {3,2,1,4,5,6};
			System.out.println(Arrays.toString(arr));
		}
	
	}

	经典实现
	
	public static String toString(int[] a) {
        if (a == null)
            return "null";
        int iMax = a.length - 1;
        if (iMax == -1)
            return "[]";

        StringBuilder b = new StringBuilder();
        b.append('[');
        for (int i = 0; ; i++) {
            b.append(a[i]);
            if (i == iMax)
                return b.append(']').toString();
            b.append(", ");
        }
    }

#### 6、Arrays - asList方法

将数组转成集合

	static <T> List<T> asList(T... a) 
          
好处：可以使用集合中的方法来操作数组中的元素，除了改变长度的方法。

a、注意：数组的长度是固定的，所以对于集合的增删方法是不可以使用的。否则会发生UnsupportedOperationException

	public class Demo2 {
		public static void main(String[] args) {
			
			String[] arr = {"aaa","bbb","ccc"};
			
			List<String> list = Arrays.asList(arr);
			
			boolean b = list.contains("aaa");
			
			System.out.println(b);
			
		}
	}

b、如果数组中的元素是对象，那么转成集合时，直接将数组中的对象元素，作为集合中的元素进行存储。

如果数组中的元素是基本类型数值，那么会将该数组作为集合中的元素进行存储，因为集合中只能存储对象，而不能存储基本类型数据。

	public class Demo3 {
		public static void main(String[] args) {
			
			int[] arr = {31,22,44,12};
			
			List<int[]> list = Arrays.asList(arr);
			
			System.out.println(list.size());
		
		}
	}
	
	输出结果：
	1

#### 7、Collection-toArray方法

集合转数组：可以对集合中的元素操作的方法进行限定，不允许对其增删。

使用的是Collecion接口中的toArray方法

toArray方法需要传入一个指定类型的数组。

如果长度小于集合的size，那么该方法会创建一个同类型并和集合相同size的数组。

如果长度大于集合的size，那么该方法就会使用指定的数组，存储集合中的元素，其它位置默认为null。

建议，最好长度就指定为集合的size。
	
	public class Demo4 {
	
		public static void main(String[] args) {
			
			List<String> list = new ArrayList<String>();
			list.add("ab3");
			list.add("sds");
			list.add("asfa");
			
			String[] arr = list.toArray(new String[5]);
			
			System.out.println(Arrays.toString(arr));
			
		}
	
	}
	
	输出结果：
	[ab3, sds, asfa, null, null]

#### 8、集合框架 - jdk5.0特性 - ForEach循环

	public class Demo5 {
	
		public static void main(String[] args) {
			
			/**
			 * foreach语句：
			 * 格式：
			 * for(类型 变量: Collection集合|数组){
			 * 
			 * }
			 * 
			 * 传统for循环和高级for循环的区别：
			 * 传统for循环可以对语句执行很多次，因为可以控制循环的增量和条件。
			 * 高级for循环是一种简化形式，必须有遍历的目标，可以是数组，也可以是Collecion单列集合。
			 * 
			 * 对数组的遍历，如果仅仅是为了获取数组中的元素，可以使用高级for循环。
			 * 如果要对数组的角标进行操作建议使用传统for循环。
			 */
			
			List<String> list = new ArrayList<String>();
			
			list.add("abc");
			list.add("def");
			list.add("ghi");
			list.add("jkl");
			
			//迭代器
			Iterator<String> it = list.iterator();
			
			while(it.hasNext()) {
				System.out.println(it.next());
			}
			
			//高级for循环，通常只用于遍历，简化书写
			for(String s : list) {
				System.out.println(s);
			}
			
			//遍历数组
			int[] arr = {3,8,2,1,7};
			for(int i : arr) {
				System.out.println(i);
			}
			
			//高级for循环遍历map集合，要先把map集合转为单列集合。
			Map<Integer,String> map = new HashMap<Integer,String>();
			
			map.put(3, "zhangsan");
			map.put(4, "lisi");
			map.put(5, "wangwu");
			map.put(7, "zhaoliu");
			
			for(Integer key : map.keySet()) {
				String value = map.get(key);
				System.out.println(key+":"+value);
			}
			
			for(Map.Entry<Integer, String> merryEntry : map.entrySet()) {
				Integer key = merryEntry.getKey();
				String value = merryEntry.getValue();
				System.out.println(key+"::"+value);
			}
			
		}
	
	}

#### 9、集合框架 - jdk5.0特性 - 函数可变参数

	public class Demo6 {
	
		public static void main(String[] args) {
			
			int sum = newAdd(5,1,4,7,3);
			System.out.println("sum="+sum);
			
			int sum1 = newAdd(3,5,2,5,3,2,2);
			System.out.println("sum1="+sum1);
			
		}
		
		/**
		 * 函数是可变参数。
		 * 其实就是一个数组，但是接收的是数组元素。
		 * 自动将这些元素封装为数组。简化了调用者的书写。
		 * 注意：可变参数类型，必须定义参数列表的结尾。
		 */
	
		private static int newAdd(int a,int... arr) {
			int sum = 0;
			for (int i = 0; i < arr.length; i++) {
				sum+=arr[i];
			}
			return sum;
		}
	
	}
	
