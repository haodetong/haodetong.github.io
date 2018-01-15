---
title: IO流 - 十、IO流 - Properties集合
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

#### 1、基本功能

Map

|-----Hashtable

|-------------Properties 		

Properties集合特点：

1、该集合中的键和值都是字符串类型。

2、集合中的数据可以保存流中，或者从流中获取。

通常该集合用于操作以键值对形式存在的配置文件。

	public class Demo1 {
	
		public static void main(String[] args) {
			
			Properties prop = new Properties();
			//存储
			prop.setProperty("zhangsan", "32");
			prop.setProperty("lisi", "32");
			prop.setProperty("wangwu", "32");
			prop.setProperty("zhaoliu", "32");
			//取出
			Set<String> names = prop.stringPropertyNames();
			
			for(String name:names) {
				String value = prop.getProperty(name);
				System.out.println(name+":"+value);
			}
			
		}
	
	}

#### 2、list方法

public void list(PrintStream out)	

将属性列表输出到指定的输出流。此方法用于调试。

	Properties prop = new Properties();
	//存储
	prop.setProperty("zhangsan", "32");
	prop.setProperty("lisi", "32");
	prop.setProperty("wangwu", "32");
	prop.setProperty("zhaoliu", "32");
	/*
	//取出
	Set<String> names = prop.stringPropertyNames();
	
	for(String name:names) {
		String value = prop.getProperty(name);
		System.out.println(name+":"+value);
	}
	*/
	//prop.list(System.out);
	
	Properties props = System.getProperties();
	props.list(System.out);	

#### 3、store方法

store(OutputStream out,String comments)

Store(Writer writer,String comments)

将集合中的字符串，键值信息持久化存储到硬盘的文件中。

	public class Dmo2 {
	
		public static void main(String[] args) throws IOException {
			
			Properties prop = new Properties();
			//存储
			prop.setProperty("zhangsan", "32");
			prop.setProperty("lisi", "32");
			prop.setProperty("wangwu", "32");
			prop.setProperty("zhaoliu", "32");
			
			//将这些集合中的键值对信息持久化存储到文件中，需要关联输出流。
			FileOutputStream fos = new FileOutputStream("info.txt");
			prop.store(fos, "name+age");
			
			fos.close();
			
		}
	
	}
	
	info.txt

	#name+age
	#Sat Jan 13 10:32:19 CST 2018
	zhangsan=32
	lisi=32
	zhaoliu=32
	wangwu=32

#### 4、修改配置信息

load(InputStream inStream)

load(Reader reader)

读取配置信息

	public class Demo3 {
	
		public static void main(String[] args) throws IOException {
			
			Properties prop = new Properties();
			
			FileInputStream fis = new FileInputStream("info.txt");
			
			prop.load(fis);
			prop.list(System.out);
					
		}
	}
	
	输出结果：
	-- listing properties --
	zhangsan=32
	lisi=32
	zhaoliu=32
	wangwu=32

模拟load方法

	public static void myLoad() throws IOException {
		Properties prop = new Properties();
		
		BufferedReader bufr = new BufferedReader(new FileReader("info.txt"));
		
		String line = null;
		while((line = bufr.readLine()) != null) {
			if(line.startsWith("#")) 
				continue;
			String[] arr = line.split("=");
			System.out.println(arr[0]+"::"+arr[1]);
		}
		
	}

修改硬盘的文件中已有的配置信息

	public class Demo4 {
		
		/**
		 * 读取文件
		 * 键值对信息存储到集合中
		 * 通过集合方法修改数据
		 * 通过流将修改后的数据存储到文件
		 * @param args
		 * @throws IOException 
		 */
		
		public static void main(String[] args) throws IOException {
			//读取文件流，流可以直接操作File对象
			File file = new File("info.txt");
			if(!file.exists()) {
				file.createNewFile();
			}
			FileReader fr = new FileReader(file);
			
			//创建空集合
			Properties prop = new Properties();
			
			//集合加载流数据
			prop.load(fr);
			//prop.list(System.out);
			
			//修改集合数据
			prop.setProperty("zhangsan", "20");
			
			//写入文件流
			FileWriter fw = new FileWriter(file);
			
			//集合持久化存储文件流
			prop.store(fw,"");
			
			//关闭流
			fr.close();
			fw.close();
		}
	
	}

#### 5、Properties集合 - 练习

定义功能，获取应用程序的运行次数，超过5次，给出到期提示并退出程序。

思路：使用计数器，并以文件形式保存在硬盘中。

	public class Demo5 {
		public static void main(String[] args) throws IOException {
			//封装配置文件对象
			File confile = new File("count.properties");
			if(!confile.exists()) {
				confile.createNewFile();
			}
			
			//输入流
			FileInputStream fis = new FileInputStream(confile);
			
			//创建空集合
			Properties prop = new Properties();
			
			//加载数据流
			prop.load(fis);
			
			//获取数据
			String value = prop.getProperty("time");
			
			//计数器
			int count = 0;
			if(value != null) {
				count = Integer.parseInt(value);
				if(count >= 5) {
					throw new RuntimeException("使用次数到期");
				}
			}
			count++;
			
			//计数
			prop.setProperty("time", count+"");
			
			//输出流
			FileOutputStream fos = new FileOutputStream(confile);
			
			//存储
			prop.store(fos, "");
			
			fis.close();
			fos.close();
			
		}
	}
	
#### 6、Properties集合 - 练习 - 文件清单列表

获取指定目录下，指定扩展名的所有文件，把这些文件的绝对路径写入到一个文本中。

	public class Demo6 {
	
		public static void main(String[] args) {
			//指定文件夹
			File dir = new File("test");
			                  
			//定义集合
			List<File> list = new ArrayList<File>();
			
			//定义过滤器
			FilenameFilter filter = new FilenameFilter() {
				public boolean accept(File dir, String name) {
					return name.endsWith(".md");
				}
			};
			
			//深度遍历文件夹
			getFiles(dir,filter,list);
			
			//遍历list写入文件
			File destfile = new File("markdownlist.txt");
			writeToFile(list,destfile);
			
					
		}
		/**
		 * 深度遍历，找到所有的文件并过滤，把符合条件的文件存储到容器中
		 * @param dir
		 * @param filter
		 * @param list
		 */
		public static void getFiles(File dir, FilenameFilter filter, List<File> list) {
			File[] files = dir.listFiles();
			for(File file:files) {
				if(file.isDirectory()) {
					//递归
					getFiles(dir, filter, list);
				} else {
					//过滤并添加集合
					if(filter.accept(dir, file.getName())) {
						list.add(file);
					}
				}
			}
		}
		
		//写入到文件中
		public static void writeToFile(List<File> list, File destFile) {
			BufferedWriter bufw = null;
			try {
				bufw = new BufferedWriter(new FileWriter(destFile));
				for(File file:list) {
					bufw.write(file.getAbsolutePath());
					bufw.flush();
					bufw.newLine();
				}
			} catch (IOException e) {
				throw new RuntimeException("写入失败");
			} finally {
				if(bufw != null)
					try {
						bufw.close();
					} catch (IOException e) {
						throw new RuntimeException("关闭失败");
					}
			}
			
		}
	
	}