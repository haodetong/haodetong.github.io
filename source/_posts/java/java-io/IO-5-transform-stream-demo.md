---
title: IO流 - 五、需求演示
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

#### 1、需求：将键盘录入的数据写入到一个文件中

	public static void main(String[] args) throws IOException {
			
			BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
			
			BufferedWriter bufw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("demo.txt")));
			
			String line = null;
			while((line=bufr.readLine()) != null) {
				if("over".equals(line))
					break;
				bufw.write(line.toUpperCase());
				bufw.newLine();
				bufw.flush();
			}
		}

#### 2、需求：将一个文本文件内容显示在控制台上
	
	BufferedReader bufr = new BufferedReader(new InputStreamReader(new FileInputStream("demo.txt")));
				
	BufferedWriter bufw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(System.out)));
	
	String line = null;
	while((line=bufr.readLine()) != null) {
		if("over".equals(line))
			break;
		bufw.write(line.toUpperCase());
		bufw.newLine();
		bufw.flush();
	}
 
 
#### 3、需求：将一个文本文件中的内容复制到另一个文件中

	BufferedReader bufr = new BufferedReader(new InputStreamReader(new FileInputStream("a.txt")));
				
	BufferedWriter bufw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("b.txt")));
	
	String line = null;
	while((line=bufr.readLine()) != null) {
		if("over".equals(line))
			break;
		bufw.write(line.toUpperCase());
		bufw.newLine();
		bufw.flush();
	}
