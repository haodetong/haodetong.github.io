---
title: IO流 - 九、File对象
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

#### 1、File对象 - 构造函数&字段

	//可以将一个已存在的，或者不存在的文件或者目录，封装为File对象
	File f1 = new File("c:\\a.txt");
	File f2 = new File("c:\\","a.txt");
	File f3 = new File("c:\\");
	File f4 = new File(f,"a.txt");
	File f5 = new File("c:"+File.separator+"abc\\a.txt");
	System.out.println(f5);//c:\abc\a.txt
	
#### 2、File对象 - 常用方法

（1）、获取

	获取文件名称、文件路径、文件大小、文件修改时间。
	File file = new File("a.txt");
	String name = file.getName();
	String absPath = file.getAbsolutePath();
	String path = file.getPath();
	long length = file.length();
	long time = file.lastModified();	

（2）、创建和删除
	
	创建文件：如果文件不存在则创建，存在则不创建
	File file = new File("a.txt");
	boolean b = file.createNewFile();
	
	删除文件：
	boolean b = file.delete();	
	
	创建文件夹目录：
	File dir = new File("abc");
	boolean b = dir.mkdir();//make directory
	
	删除单个文件夹目录：
	boolean b = dir.delete();
	
	创建多级目录：
	File dir = new File("a\\b\\c\\d");
	dir.mkdirs();
	
（3）、判断
	
	判断文件是否存在
	File f = new File("a.txt");
	boolean b = f.exists();
		
	判断是否是文件，最好先判断是否存在，不存在则为false
	boolean b = f.isFile();
	
	判断是否是目录
	boolean b = f.isDerectory();
	
	
（4）、重命名
	
	//c盘的0.mp3重命名为了9.mp3并被剪切到了d盘
	File f1 = new File("c:\\0.mp3");
	File f2 = new File("d:\\9.mp3");
	boolean b = f1.renameTo(f2);
	
（5）、系统根目录和容量获取
	
	系统根目录
	File[] files = File.listRoots();
	for(File file:files) {
		System.out.println(file);
	}
	输出结果：/
	
	File file  = new File("//");
	System.out.println("getFreeSpace:"+file.getFreeSpace());
	System.out.println("getTotalSpace:"+file.getTotalSpace());
	System.out.println("getUsableSpace:"+file.getUsableSpace());
	
（6）、获取目录内容

	String[] list()
	获取当前目录下的文件以及文件夹的名称，包含隐藏文件。	调用list方法的File对象中封装的必须是目录。
	
	File file = new File("//Users//haojie//Projects//hexo");
	String[] contents = file.list();
	for(String con:contents) {
		System.out.println(con);
	}
	输出结果：
	scaffolds
	.DS_Store
	db.json
	source
	node_modules
	public
	.gitignore
	package.json
	_config.yml
	.git
	.deploy_git
	themes	 		
	
（7）、过滤器
	
//通过文件名进行过滤
String[] list(FilenameFilter filter);

	public class ListDemo {
	
		public static void main(String[] args) {
			File dir = new File("//Users//haojie//Projects//hexo//source//_posts");
			String[] names = dir.list(new FileFilterBySuffix(".md"));
			for(String name:names) {
				System.out.println(name);
			}
		}
	
	}
	
	public class FileFilterBySuffix implements FilenameFilter {
	
		private String suffix;
		
	
		public FileFilterBySuffix(String suffix) {
			super();
			this.suffix = suffix;
		}
	
		public boolean accept(File dir, String name) {
			//通过文件后缀名进行过滤
			return name.endsWith(suffix);
		}
	
	}
	
	
//通过文件对象进行过滤
File[] listFiles(FileFilter filter) 
	
	public class ListFilesDemo {

		public static void main(String[] args) {
			File dir = new File("//Users//haojie//Projects//hexo");
			File[] files = dir.listFiles(new FileFilterByHidden());
			
			for(File file:files) {
				System.out.println(file);
			}
		}
	
	}	
	
	public class FileFilterByHidden implements FileFilter {
	
		@Override
		public boolean accept(File pathname) {
			//过滤非隐藏文件
			return !pathname.isHidden();
		}
	
	}	
	
	
#### 3、File对象 - 练习

#### （1）、深度遍历文件夹

	//列出指定文件夹下的所有目录和文件并按层级缩进
	public class Demo3 {
	
		public static void main(String[] args) {
			File dir = new File("//Users//haojie//Projects//hexo//source");
			listAll(dir,0);
		}
	
		private static void listAll(File dir,int level) {
		
			System.out.println(getSpace(level)+dir.getName());				
			level++;
			
			//获取指定目录下当前的所有文件夹或者文件对象
			File[] files = dir.listFiles();
			
			for (int i = 0; i < files.length; i++) {
				if(files[i].isDirectory()) {
					listAll(files[i],level);
				}else {
					System.out.println(getSpace(level)+files[i].getName());				
				}
			}
		}
	
		private static String getSpace(int level) {
			StringBuilder sb = new StringBuilder();
			for (int i = 0; i < level; i++) {
				sb.append("    ");
			}
			return sb.toString();
		}
	
	}

#### （2）、递归

注意：递归一定要明确条件，否则会栈溢出

	public class Demo4 {
	
		public static void main(String[] args) {
			toBinary(6);
			System.out.println(getSum(5));
		}
		
		//num!
		private static int getSum(int num) {
			if(num == 1)
				return 1;
			return num + getSum(num-1);
		}
		
		//转二进制
		private static void toBinary(int num) {
			
			if(num > 0) {
				toBinary(num/2);
				System.out.println(num%2);
			}
			
		}
	
	}

#### (3)、删除目录

从里往外删，删除指定目录及目录下所有的目录和文件

	public class Demo5 {
	
		public static void main(String[] args) {
			
			File dir = new File("//Users//haojie//a");
			removeDir(dir);
			
		}
	
		private static void removeDir(File dir) {
			File[] files = dir.listFiles();
			
			for(File file:files) {
				if(file.isDirectory()) {
					removeDir(file);
				} else {
					System.out.println(file+":"+file.delete());
				}
			}
			
			System.out.println(dir+":"+dir.delete());
		}
		
	}
