---
title: IO流 - 三、字节流
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

#### 1、操作文件基本演示

写

	public class Demo8 {
	
		public static void main(String[] args) throws IOException {
			
			//创建字节输出流对象
			FileOutputStream fos = new FileOutputStream("demo.txt");
			
			//数据直接写入目标文件，没有缓冲区
			fos.write("aaaaaddfsdss".getBytes());
			
			fos.close();
			
		}
	
	}

逐个字节读取

public class Demo9 {

	public static void main(String[] args) throws IOException {
		
		FileInputStream fis = new FileInputStream("demo.txt");
		
		int ch = 0;
		while((ch = fis.read()) != -1) {
			System.out.println((char)ch);
		}
		
		fis.close();
	}

}

逐个数组读取

	public class Demo10 {
	
		public static void main(String[] args) throws IOException {
			
			FileInputStream fis = new FileInputStream("demo.txt");
			
			byte[] buf = new byte[1024];
			int length = 0;
			
			while((length = fis.read(buf, 0, length)) != -1) {
				System.out.println(new String(buf,0,length));
			}
			
			fis.close();
			
		}
	
	}
	
#### 2、练习 - 复制mp3

直接复制

	public class Demo11 {
	
		public static void main(String[] args) throws IOException {
			
			FileInputStream fis = new FileInputStream("0.mp3");
			FileOutputStream fos = new FileOutputStream("1.mp3");
			
			byte[] buf = new byte[1024];
			int length = 0;
			
			while((length = fis.read()) != -1) {
				fos.write(buf,0,length);
			}
			
			fos.close();
			fis.close();
		}
	
	}

高效复制

	public class Demo11 {
	
		public static void main(String[] args) throws IOException {
			
			FileInputStream fis = new FileInputStream("0.mp3");
			BufferedInputStream bufis = new BufferedInputStream(fis);
			
			FileOutputStream fos = new FileOutputStream("1.mp3");
			BufferedOutputStream bufos = new BufferedOutputStream(fos);
			
			byte[] buf = new byte[1024];
			
			int length = 0;
			while((length = bufis.read(buf)) != -1) {
				bufos.write(buf, 0, length);
			}
			
			bufis.close();
			bufos.close();
			
		}
	}
	
#### 3、演示键盘录入

读取键盘录入的一个数据，并打印在控制台上。

键盘本身就是一个标准的输入设备。

对于java而言，对于这种输入设备都有相应的对象。

java.lang包里的 System类

public static finael InputStream in

“标准”输入流，不用关闭。

键盘录入：

	public class Demo12 {
		public static void main(String[] args) throws IOException {
			InputStream in = System.in;
			int ch = in.read();
			System.out.println(ch);
		}
	}

读取键盘录入：

	public class Demo15 {

		public static void main(String[] args) throws IOException {
			
			InputStream in = System.in;
			int ch = 0;
			StringBuilder sb = new StringBuilder();
			while((ch=in.read())!=-1) {
				if(ch=='\r') 
					continue;
				if(ch=='\n') {
					String temp = sb.toString();
					if("over".equals(temp))
						break;
					System.out.println(temp.toUpperCase());
					sb.delete(0, sb.length());
				} else {
					sb.append((char)ch);
				}
			}
			
		}
	
	}