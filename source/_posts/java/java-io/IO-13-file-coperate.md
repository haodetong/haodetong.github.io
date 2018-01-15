---
title: IO流 - 十三、IO流 - 文件切割合并
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流
---

#### 1、文件切割

	public class SplitFile {
	
		private static final int SIZE = 1024*1024;
	
		public static void main(String[] args) throws IOException {
			//指定切割文件
			File file = new File("IMG_0215.JPG");
			
			//读取流
			FileInputStream fis = new FileInputStream(file);
			
			//写入流
			FileOutputStream fos = null;
			
			//缓冲区
			byte[] buf = new byte[SIZE];
			
			//存储目录
			File dir = new File("//Users//haojie//eclipse-workspace//hello world//partfiles");
			if(!dir.exists())
				dir.mkdir();
			
			//切割
			int length = 0;
			int count = 1;
			while((length = fis.read(buf)) != -1) {
				fos = new FileOutputStream(new File(dir,(count++)+".part"));
				fos.write(buf, 0, length);;
			}
			
			//关闭流
			fos.close();
			fis.close();
		}
	
	}

#### 2、文件合并

	public class Merge {
	
		public static void main(String[] args) throws IOException {
			
			File dir = new File("//Users//haojie//eclipse-workspace//hello world//partfiles");
			mergeFile(dir);
		}
		
		public static void mergeFile(File dir) throws IOException {
			//读取流
			ArrayList<FileInputStream> al = new ArrayList<FileInputStream>();
			for(int x=1; x<3; x++) {
				al.add(new FileInputStream(new File(dir,x+".part")));
			}
			//合并流
			Enumeration<FileInputStream> en = Collections.enumeration(al);
			SequenceInputStream sis = new SequenceInputStream(en);
			//输出流
			FileOutputStream fos = new FileOutputStream(new File(dir,"1.JPG"));
			//缓冲区
			byte[] buf = new byte[1024];
			int length = 0;
			while((length = sis.read(buf)) != -1) {
				fos.write(buf, 0, length);
			}
			//关闭流
			fos.close();
			sis.close();
		}
	
	}

#### 3、文件切割合并+配置文件

	public class FileSplitMergeUtil {
	
		private static final int SIZE = 1024*1024;
	
		public static void main(String[] args) throws IOException {
			
			//切割
			File file = new File("IMG_0215.JPG");
			splitFile(file);
			
			//合并
			File dir = new File("//Users//haojie//eclipse-workspace//hello world//partfiles");
			mergeFile(dir);
		}
		
		//切割传入的文件
		public static void splitFile(File file) throws IOException {
			
			//读取流
			FileInputStream fis = new FileInputStream(file);
			
			//写入流
			FileOutputStream fos = null;
			
			//缓冲区
			byte[] buf = new byte[SIZE];
			
			//存储目录
			File dir = new File("//Users//haojie//eclipse-workspace//hello world//partfiles");
			if(!dir.exists())
				dir.mkdir();
			
			//切割
			int length = 0;
			int count = 1;
			while((length = fis.read(buf)) != -1) {
				fos = new FileOutputStream(new File(dir,(count++)+".part"));
				fos.write(buf, 0, length);
				fos.close();
			}
			
			/**
			 * 配置文件
			 * partcount 碎片数量
			 * filename 被切割文件名称
			 */
			Properties prop = new Properties();
			
			//配置文件设置
			prop.setProperty("partcount", count+"");
			prop.setProperty("filename", file.getName());
			
			//配置文件输出流
			fos = new FileOutputStream(new File(dir,count+".properties"));
			
			//写入配置信息
			prop.store(fos, "fileinfo");
			
			//关闭流
			fos.close();
			fis.close();
		}
		
		public static void mergeFile(File dir) throws IOException {
			//过滤配置文件
			File[] files = dir.listFiles(new SuffixFilter(".properties"));
			if(files.length != 1)
				throw new RuntimeException(dir+"获取配置文件失败");
			File confile = files[0];
			
			//读取配置文件
			Properties prop = new Properties();
			FileInputStream fis = new FileInputStream(confile);
			prop.load(fis);
			String filename = prop.getProperty("filename");
			int count = Integer.parseInt(prop.getProperty("partcount"));
			
			//检查碎片文件个数
			File[] partFiles = dir.listFiles(new SuffixFilter(".part"));
			if(partFiles.length != (count-1)) {
				throw new RuntimeException("碎片文件与配置信息中的数量不符");			
			}
			
			//读取流
			ArrayList<FileInputStream> al = new ArrayList<FileInputStream>();
			for(int x=0; x<partFiles.length; x++) {
				al.add(new FileInputStream(partFiles[x]));
			}
			
			//合并流
			Enumeration<FileInputStream> en = Collections.enumeration(al);
			SequenceInputStream sis = new SequenceInputStream(en);
			
			//输出流
			FileOutputStream fos = new FileOutputStream(new File(dir,filename));
			
			//缓冲区
			byte[] buf = new byte[1024];
			int length = 0;
			while((length = sis.read(buf)) != -1) {
				fos.write(buf, 0, length);
			}
			
			//关闭流
			fos.close();
			sis.close();
		}
	}
	
	public class SuffixFilter implements FilenameFilter {
	
		private String suffix;
		
		public SuffixFilter(String suffix) {
			super();
			this.suffix = suffix;
		}
	
		public boolean accept(File dir, String name) {
			return name.endsWith(suffix);
		}
	
	}
